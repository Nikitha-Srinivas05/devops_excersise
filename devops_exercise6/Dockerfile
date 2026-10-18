FROM python:3.12-slim
WORKDIR /app
RUN pip install prometheus-client
COPY delivery_metrics.py .
CMD ["python", "delivery_metrics.py"]