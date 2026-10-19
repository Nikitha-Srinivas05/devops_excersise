# Exercise 10: Multi-Node Kubernetes Cluster with Multiple Applications and ReplicaSets

## Objective

Deploy two Flask microservices on a 3-node Minikube cluster, with each replica placed on a different node via pod anti-affinity:

| Service | App | Replicas |
|---|---|---|
| Product Catalog | AppA | 2 |
| Shopping Cart | AppB | 3 |



## Environment

| Item | Value |
|---|---|
| OS | Microsoft Windows 11 Home Single Language 25H2 |
| Minikube | v1.38.1 (v1.39.0 available) |
| Kubernetes | v1.35.1 |
| Container runtime / driver | Docker 29.2.1 / Docker Desktop driver |
| Profile | `devops-multinode` |
| Working directory | `cd devops_exercise10` |

---

## Step 1a: Clean up and start a 3-node cluster

The existing `minikube` cluster was removed, then the multi-node cluster was created:

```powershell
minikube start --nodes 3 -p devops-multinode --force
```

**Result:** The cluster started with three nodes, each created with CPUs=2 and Memory=3072MB:

- `devops-multinode` (primary control-plane)
- `devops-multinode-m02` (worker)
- `devops-multinode-m03` (worker)

Addons enabled by default: `storage-provisioner`, `default-storageclass`. Minikube reported:
`Done! kubectl is now configured to use "devops-multinode" cluster and "default" namespace by default`


> **Screenshot 1:** Minikube cleanup and 3-node cluster start
>
> ![Minikube cleanup and 3-node cluster start](images/exercise10-minikube-start.png)



## Step 1b: product-catalog image build


> **Screenshot 2:** product-catalog image build
>
> ![product-catalog image build](images/exercise10-product-build.png)

## Steps 2 to 4: Application code and Docker images

Created `product_catalog.py`, `shopping_cart.py`, `Dockerfile.product` and `Dockerfile.shopping` as given in the exercise.

```powershell
docker build -t product-catalog:latest -f Dockerfile.product .
docker build -t shopping-cart:latest -f Dockerfile.shopping .
```

| Image | Build result | Build time |
|---|---|---|
| `product-catalog:latest` | FINISHED (10/10) | 9.8s |
| `shopping-cart:latest` | FINISHED (9/9) | 7.1s |


> **Screenshot 3:** shopping-cart image build
>
> ![shopping-cart image build](images/exercise10-shopping-cart-build.png)

## Step 5: Load images into Minikube

```powershell
minikube -p devops-multinode image load product-catalog:latest
minikube -p devops-multinode image load shopping-cart:latest
minikube -p devops-multinode ssh -- docker images
```

**Result:** Both images were present in the node, alongside the Kubernetes system images:

| Image | ID | Disk usage |
|---|---|---|
| `product-catalog:latest` | b2212b1194fa | 134MB |
| `shopping-cart:latest` | 3444f8e4d552 | 134MB |

(The `ssh -- docker images` output used the newer table format, with the `U` "In Use" marker.)


> **Screenshot 4:** Image load, `docker images` inside the node, and `kubectl apply`
>
> ![Image load, `docker images` inside the node, and `kubectl apply`](images/exercise10-image-load-and-kubectl-apply.png)

## Steps 6 and 7: Deploy the applications and services

```powershell
kubectl apply -f product_catalog_deployment.yaml
kubectl apply -f shopping_cart_deployment.yaml
kubectl apply -f product_catalog_service.yaml
kubectl apply -f shopping_cart_service.yaml
```

**Output:**

```
deployment.apps/product-catalog created
deployment.apps/shopping-cart created
service/product-catalog-service created
service/shopping-cart-service created
```

## Step 8: Verify pod distribution

```powershell
kubectl get pods -o wide
```

```
NAME                               READY   STATUS    RESTARTS   AGE     IP           NODE                
product-catalog-6794cdc954-w56cx   1/1     Running   0          2m44s   10.244.2.2   devops-multinode-m03
product-catalog-6794cdc954-ztn79   1/1     Running   0          2m44s   10.244.1.2   devops-multinode-m02
shopping-cart-7bf585c6f8-57whl     1/1     Running   0          2m21s   10.244.0.3   devops-multinode
shopping-cart-7bf585c6f8-99pgb     1/1     Running   0          2m21s   10.244.2.3   devops-multinode-m03
shopping-cart-7bf585c6f8-vpt92     1/1     Running   0          2m21s   10.244.1.3   devops-multinode-m02 
```


All 5 pods are `1/1 Running` with 0 restarts. The `requiredDuringSchedulingIgnoredDuringExecution` pod anti-affinity (topologyKey `kubernetes.io/hostname`) worked as intended: no two replicas of the same app share a node.

- Product Catalog (2 replicas) is on 2 different nodes.
- Shopping Cart (3 replicas) is spread across all 3 nodes, one per node.


> **Screenshot 5:** `kubectl get pods -o wide` showing pod distribution across nodes
>
> ![`kubectl get pods -o wide` showing pod distribution across nodes](images/exercise10-kubectl-get-pods-o-wide.png)

## Step 9: Access the services

```powershell
minikube -p devops-multinode service product-catalog-service
minikube -p devops-multinode service shopping-cart-service
```

| Service | NodePort URL | Tunnel URL (used for testing) |
|---|---|---|
| `product-catalog-service` | http://192.168.58.2:30871 | **http://127.0.0.1:63254** |
| `shopping-cart-service` | http://192.168.58.2:31537 | **http://127.0.0.1:56541** |

Minikube noted that with the Docker driver on Windows, the terminal running the tunnel must stay open.


> **Screenshot 6:** Product Catalog service URL and tunnel
>
> ![Product Catalog service URL and tunnel](images/exercise10-product-catalog-service-tunnel.png)

> **Screenshot 7:** Shopping Cart service URL and tunnel
>
> ![Shopping Cart service URL and tunnel](images/exercise10-shopping-cart-service-tunnel.png)

## Step 10: Testing

PowerShell aliases `curl` to `Invoke-WebRequest`, so the GET requests showed a security prompt (answered `y`). `curl.exe` was used for the POST request.

### Test 1: Product Catalog, list products

```powershell
curl http://127.0.0.1:63254/products
```

**Result:** `StatusCode: 200 OK`

```json
[{"id":1,"name":"Laptop","price":1200},{"id":2,"name":"Phone","price":800},{"id":3,"name":"Headphones","price":150}]
```

Response headers: `Content-Type: application/json`, `Content-Length: 117`, `Server: Werkzeug/3.1.9 Python/3.9.25`. This matches the expected response.


> **Screenshot 8:** GET /products response
>
> ![GET /products response](images/exercise10-test-get-products.png)

### Test 2: Shopping Cart, get empty cart

```powershell
curl http://127.0.0.1:56541/cart
```

**Result:** `StatusCode: 200 OK`, `Content: []`, `Content-Length: 3`. The cart is empty initially, as expected.


> **Screenshot 9:** GET /cart returning an empty list
>
> ![GET /cart returning an empty list](images/exercise10-test-get-cart-empty.png)

### Test 3: Shopping Cart, add an item

```powershell
curl.exe -X POST "http://127.0.0.1:56541/cart" -H "Content-Type: application/json" -d '{\"id\": 1, \"name\": \"Laptop\", \"quantity\": 1}'
```

**Result:**

```json
[{"id":1,"name":"Laptop","quantity":1}]
```

The item was added and returned in the cart, as expected.


> **Screenshot 10:** POST /cart adding an item
>
> ![POST /cart adding an item](images/exercise10-test-post-cart.png)

---

## Summary

| Check | Status |
|---|---|
| 3-node Minikube cluster (`devops-multinode`) | Passed |
| Registry addon enabled | Passed |
| Docker images built and loaded into the cluster | Passed |
| Product Catalog: 2 replicas on different nodes | Passed |
| Shopping Cart: 3 replicas on different nodes | Passed |
| Services exposed via NodePort and tunnel | Passed |
| `GET /products` returns product list | Passed |
| `GET /cart` returns empty list | Passed |
| `POST /cart` adds item | Passed |

Both applications were deployed on the multi-node Minikube cluster with ReplicaSets, and pod anti-affinity placed each replica of a service on a different node. If one node fails, the remaining replicas keep serving traffic, which gives the availability and fault tolerance the exercise set out to demonstrate.

**Observation:** One caveat is that the Shopping Cart stores its data in an in-memory Python list per pod. With 3 replicas behind a Service, a cart added through one pod will not be visible from the other pods, so a shared store such as Redis or a database would be needed in production.