# Tạo file docker-compose.yml có nội dung như bên dưới
```
version: '3'
services:
  prometheus:
    image: prom/prometheus
    volumes: 
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    command: 
      - '--config.file=/etc/prometheus/prometheus.yml'
    ports:
      - 9191:9090
  node-exporter:
    image: prom/node-exporter
    ports: 
      - 9100:9100
  cadvisor: 
    image: google/cadvisor:latest
    ports:
      - 8181:8080
    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run:rw
      - /sys:/sys:ro
      - /var/lib/docker/:/var/lib/docker:ro 
  grafana: 
    image: grafana/grafana
    user: "1000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=password
    depends_on:
      - prometheus
    ports: 
      - 3000:3000
```
# Tạo file cấu hình prometheus.yml
```
global:
  scrape_interval: 5s
  external_labels:
    monitor: 'Monitoring'
scrape_configs: 
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9191']
  - job_name: 'node-exporter'
    static_configs:
      - targets: ['localhost:9100'] 
  - job_name: 'cAdvisor'
    static_configs:
      - targets: ['localhost:8181']
```
>note: 2 file này đặt cùng trong 1 folder

# run bằng lệnh:
```
docker-compose up -d
```