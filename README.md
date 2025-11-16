# Docker Load Balancer Setup with Nginx + Docker Compose

A simple, clean, and production-ready setup to run **multiple Nginx instances** inside Docker containers and load balance them using **Nginx on the host server** with round-robin strategy.

---

## 📦 Project Structure

```
.
├── docker-compose.yml
├── nginx/
│   └── loadbalancer.conf
└── README.md
```

---

## 🧰 Requirements

- 🐧 Linux Server (Ubuntu/Debian recommended)
- 🐳 Docker & Docker Compose
- 🌐 Nginx

---

## 🚀 Setup Instructions

### 1. Update System
```bash
sudo apt update && sudo apt upgrade -y
```

### 2. Install Docker, Docker Compose & Nginx
```bash
sudo apt install docker.io docker-compose nginx -y
```

### 3. Start and Enable Docker
```bash
sudo systemctl start docker
sudo systemctl enable docker
```

### 4. Create Project Directory
```bash
mkdir ~/loadbalancer && cd ~/loadbalancer
```

### 5. Create `docker-compose.yml`

```yaml
version: '3.8'

services:
  web1:
    image: nginx:alpine
    ports:
      - "8081:80"
    container_name: web1
    restart: always

  web2:
    image: nginx:alpine
    ports:
      - "8082:80"
    container_name: web2
    restart: always

  web3:
    image: nginx:alpine
    ports:
      - "8083:80"
    container_name: web3
    restart: always
```

### 6. Run the Containers
```bash
docker-compose up -d
```

### 7. Test Individual Containers
```bash
curl http://localhost:8081
curl http://localhost:8082
curl http://localhost:8083
```

> You should see the default Nginx welcome page from each instance.

---

### 8. Remove Default Nginx Site
```bash
sudo rm /etc/nginx/sites-enabled/default
```

### 9. Create Load Balancer Config

**File:** `nginx/loadbalancer.conf`

```nginx
upstream backend {
    server 127.0.0.1:8081;
    server 127.0.0.1:8082;
    server 127.0.0.1:8083;
}

server {
    listen 80;
    server_name _;

    location / {
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /health {
        access_log off;
        return 200 "healthy\n";
    }
}
```

**Copy to system:**
```bash
sudo cp nginx/loadbalancer.conf /etc/nginx/conf.d/
```

### 10. Test Nginx Configuration
```bash
sudo nginx -t
```

### 11. Restart & Enable Nginx
```bash
sudo systemctl restart nginx
sudo systemctl enable nginx
```

### 12. Configure Firewall
```bash
sudo ufw allow 80
sudo ufw allow 22
sudo ufw --force enable
```

---

## 🌍 Final Test

```bash
echo "Open in browser: http://$(curl -s ifconfig.me)"
```

Or test health check:
```bash
curl http://$(curl -s ifconfig.me)/health
# Output: healthy
```

---

## 🎯 Features

- **3 independent Nginx containers**
- **Round-robin load balancing** via host Nginx
- **Health check endpoint**: `/health`
- **Production-ready** with proper headers & restart policies
- **Clean separation** of concerns

---

## 🛠️ Optional Enhancements

- Add SSL/TLS with Let's Encrypt
- Use custom domain instead of IP
- Scale containers dynamically: `docker-compose up -d --scale web1=3`
- Monitor with Prometheus + Grafana

---

## 🔗 Useful Commands

```bash
# View logs
docker-compose logs -f

# Stop all
docker-compose down

# Rebuild & restart
docker-compose down && docker-compose up -d --build
```

---

**Enjoy your scalable, load-balanced Nginx cluster!** 🚀

---

*Made with ❤️ for clean DevOps setups*  
*Feel free to ⭐ star this repo if you found it helpful!*
