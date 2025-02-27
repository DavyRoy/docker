FROM python:3.11-alpine AS builder
WORKDIR /app
COPY . .  
RUN pip install --no-cache-dir flask requests
FROM python:3.11-alpine
WORKDIR /app
COPY --from=builder /usr/local/lib/python3.11/site-packages /usr/local/lib/python3.11/site-packages
COPY app.py .
EXPOSE 5000
CMD ["python", "app.py"]
