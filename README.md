# Docker-Load-Balancer-Setup
🚀 Docker Load Balancer Setup (Nginx + Docker Compose)  A simple and clean setup for running multiple Nginx instances using Docker, and load balancing them with Nginx on the host server.

📦 Project Structure
├── docker-compose.yml
├── nginx/
│   └── loadbalancer.conf
└── README.md

🧰 Requirements

🐧 Linux Server (Ubuntu/Debian recommended)

🐳 Docker & Docker Compose

🌐 Nginx

🔧 1. Update System
sudo apt update && sudo apt upgrade -y

🐳 2. Install Docker, Docker Compose & Nginx
sudo apt install docker.io docker-compose nginx -y

⚙️ 3. Start and Enable Docker
sudo systemctl start docker
sudo systemctl enable docker

📁 4. Create Load Balancer Directory
mkdir ~/loadbalancer && cd ~/loadbalancer

📝 5. Create docker-compose.yml
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

▶️ 6. Run the Containers
docker-compose up -d

🧪 7. Test Container Outputs
curl http://localhost:8081
curl http://localhost:8082
curl http://localhost:8083

🗑️ 8. Remove Default Nginx Site
sudo rm /etc/nginx/sites-enabled/default

⚖️ 9. Create Nginx Load Balancer Config

File path: nginx/loadbalancer.conf

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


Copy it into system directory:

sudo cp nginx/loadbalancer.conf /etc/nginx/conf.d/

🧪 10. Test Nginx Configuration
sudo nginx -t

🔄 11. Restart and Enable Nginx
sudo systemctl restart nginx
sudo systemctl enable nginx

🔥 12. Firewall Rules
sudo ufw allow 80
sudo ufw allow 22
sudo ufw --force enable

🌍 13. Final Test
echo "Open in browser: http://$(curl -s ifconfig.me)"

🎯 Result

You now have:

3 independent Nginx instances

Automatic round-robin load balancing

Clean and production-ready architecture

Health check endpoint: /health
