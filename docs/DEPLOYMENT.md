# Deployment Guide

## Overview

This guide covers deployment options for the QuikTalk Chat App, including development, staging, and production environments.

## Prerequisites

- Node.js 16+ installed
- MongoDB database (local or cloud)
- Domain name (for production)
- SSL certificate (for production)

## Environment Setup

### Development Environment

```bash
# Local development with hot reload
cd server && npm run dev
cd client && npm run dev
```

### Production Environment Variables

**Server (.env.production):**

```env
NODE_ENV=production
PORT=3001

# Database
DATABASE_URL=mongodb+srv://username:password@cluster.mongodb.net/quiktalk

# Security
JWT_KEY=your-super-secure-production-jwt-key

# CORS
ORIGIN=https://your-domain.com

# Optional: File upload limits
MAX_FILE_SIZE=10485760  # 10MB
```

**Client (.env.production):**

```env
VITE_SERVER_URL=https://your-api-domain.com
```

## Deployment Options

### Option 1: Traditional VPS/Server Deployment

#### 1. Server Setup

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Node.js
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# Install PM2 for process management
sudo npm install -g pm2

# Install Nginx for reverse proxy
sudo apt install nginx -y
```

#### 2. Application Deployment

```bash
# Clone repository
git clone https://github.com/Explore13/QuikTalk-Chat-App.git
cd QuikTalk-Chat-App

# Install dependencies
cd server && npm ci --production
cd ../client && npm ci

# Build client
cd client && npm run build

# Copy build files to web server
sudo cp -r dist/* /var/www/html/

# Start server with PM2
cd ../server
pm2 start index.js --name "quiktalk-server"
pm2 startup
pm2 save
```

#### 3. Nginx Configuration

```nginx
# /etc/nginx/sites-available/quiktalk
server {
    listen 80;
    server_name your-domain.com;

    # Serve static client files
    location / {
        root /var/www/html;
        try_files $uri $uri/ /index.html;
    }

    # Proxy API requests to backend
    location /api {
        proxy_pass http://localhost:3001;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }

    # Socket.io proxy
    location /socket.io/ {
        proxy_pass http://localhost:3001;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

```bash
# Enable site
sudo ln -s /etc/nginx/sites-available/quiktalk /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

#### 4. SSL Certificate (Let's Encrypt)

```bash
# Install Certbot
sudo apt install certbot python3-certbot-nginx -y

# Get SSL certificate
sudo certbot --nginx -d your-domain.com

# Auto-renewal
sudo crontab -e
# Add: 0 12 * * * /usr/bin/certbot renew --quiet
```

### Option 2: Docker Deployment

#### 1. Docker Files

**Dockerfile (Server):**

```dockerfile
FROM node:18-alpine

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm ci --only=production

# Copy source code
COPY . .

# Create uploads directory
RUN mkdir -p uploads/profiles uploads/files

# Expose port
EXPOSE 3001

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:3001/health || exit 1

# Start application
CMD ["node", "index.js"]
```

**Dockerfile (Client):**

```dockerfile
FROM node:18-alpine as build

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm ci

# Copy source code
COPY . .

# Build application
RUN npm run build

# Production stage
FROM nginx:alpine

# Copy built files
COPY --from=build /app/dist /usr/share/nginx/html

# Copy nginx configuration
COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

**docker-compose.yml:**

```yaml
version: "3.8"

services:
  mongodb:
    image: mongo:7
    container_name: quiktalk-mongo
    restart: always
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: password
    ports:
      - "27017:27017"
    volumes:
      - mongo_data:/data/db

  server:
    build:
      context: ./server
      dockerfile: Dockerfile
    container_name: quiktalk-server
    restart: always
    environment:
      NODE_ENV: production
      DATABASE_URL: mongodb://admin:password@mongodb:27017/quiktalk?authSource=admin
      JWT_KEY: your-production-jwt-key
      ORIGIN: http://localhost:3000
      PORT: 3001
    ports:
      - "3001:3001"
    depends_on:
      - mongodb
    volumes:
      - ./server/uploads:/app/uploads

  client:
    build:
      context: ./client
      dockerfile: Dockerfile
    container_name: quiktalk-client
    restart: always
    ports:
      - "3000:80"
    depends_on:
      - server

volumes:
  mongo_data:
```

#### 2. Deploy with Docker

```bash
# Build and start all services
docker-compose up -d

# View logs
docker-compose logs -f

# Stop services
docker-compose down
```

### Option 3: Cloud Platform Deployment

#### Heroku Deployment

**1. Prepare for Heroku:**

```json
// package.json (root)
{
  "name": "quiktalk-chat-app",
  "version": "1.0.0",
  "scripts": {
    "build": "cd client && npm install && npm run build",
    "start": "cd server && npm start",
    "heroku-postbuild": "npm run build"
  },
  "engines": {
    "node": "18.x"
  }
}
```

**2. Heroku Configuration:**

```bash
# Install Heroku CLI
# Create Heroku app
heroku create quiktalk-app

# Set environment variables
heroku config:set NODE_ENV=production
heroku config:set JWT_KEY=your-jwt-key
heroku config:set DATABASE_URL=mongodb+srv://...

# Deploy
git push heroku main
```

#### Vercel Deployment (Client Only)

**vercel.json:**

```json
{
  "version": 2,
  "builds": [
    {
      "src": "package.json",
      "use": "@vercel/static-build",
      "config": { "distDir": "dist" }
    }
  ],
  "routes": [
    { "handle": "filesystem" },
    { "src": "/(.*)", "dest": "/index.html" }
  ]
}
```

#### Railway Deployment

```bash
# Install Railway CLI
npm install -g @railway/cli

# Login and deploy
railway login
railway init
railway up
```

## Database Deployment

### MongoDB Atlas (Recommended)

1. **Create Cluster:**

   - Sign up at https://cloud.mongodb.com
   - Create a new cluster
   - Set up database user and password
   - Configure network access

2. **Connection String:**

   ```
   mongodb+srv://username:password@cluster.mongodb.net/quiktalk?retryWrites=true&w=majority
   ```

3. **Security:**
   - Enable authentication
   - Configure IP whitelist
   - Use strong passwords
   - Enable audit logging

### Self-hosted MongoDB

```bash
# Install MongoDB
sudo apt-get install gnupg
wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add -
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
sudo apt-get update
sudo apt-get install -y mongodb-org

# Start MongoDB
sudo systemctl start mongod
sudo systemctl enable mongod

# Secure MongoDB
mongo
use admin
db.createUser({
  user: "admin",
  pwd: "secure_password",
  roles: ["userAdminAnyDatabase"]
})
```

## Load Balancing and Scaling

### Nginx Load Balancer

```nginx
upstream quiktalk_backend {
    server localhost:3001;
    server localhost:3002;
    server localhost:3003;
}

server {
    location /api {
        proxy_pass http://quiktalk_backend;
    }
}
```

### PM2 Cluster Mode

```javascript
// ecosystem.config.js
module.exports = {
  apps: [
    {
      name: "quiktalk-server",
      script: "index.js",
      instances: "max",
      exec_mode: "cluster",
      env: {
        NODE_ENV: "production",
        PORT: 3001,
      },
    },
  ],
};
```

```bash
pm2 start ecosystem.config.js
```

## Monitoring and Maintenance

### Health Checks

```javascript
// Add to server/index.js
app.get("/health", (req, res) => {
  res.status(200).json({
    status: "OK",
    timestamp: new Date().toISOString(),
    uptime: process.uptime(),
    memory: process.memoryUsage(),
    environment: process.env.NODE_ENV,
  });
});
```

### Log Management

```bash
# PM2 logs
pm2 logs quiktalk-server

# Nginx logs
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log

# System logs
journalctl -u nginx -f
```

### Backup Strategy

```bash
#!/bin/bash
# backup.sh
DATE=$(date +%Y%m%d_%H%M%S)
mongodump --uri="mongodb+srv://..." --out="/backups/backup_$DATE"
tar -czf "/backups/backup_$DATE.tar.gz" "/backups/backup_$DATE"
rm -rf "/backups/backup_$DATE"

# Add to crontab for daily backups
# 0 2 * * * /path/to/backup.sh
```

## Security Hardening

### Server Security

```bash
# Firewall setup
sudo ufw allow ssh
sudo ufw allow 80
sudo ufw allow 443
sudo ufw enable

# Fail2ban for SSH protection
sudo apt install fail2ban
sudo systemctl enable fail2ban

# Regular updates
sudo apt update && sudo apt upgrade -y
```

### Application Security

```javascript
// Add security middleware
import helmet from "helmet";
import rateLimit from "express-rate-limit";

// Security headers
app.use(
  helmet({
    contentSecurityPolicy: {
      directives: {
        defaultSrc: ["'self'"],
        connectSrc: ["'self'", "wss:", "ws:"],
      },
    },
  })
);

// Rate limiting
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // limit each IP to 100 requests per windowMs
});
app.use("/api", limiter);
```

## Troubleshooting

### Common Issues

1. **Socket.io Connection Issues:**

   ```javascript
   // Check CORS configuration
   // Verify WebSocket support
   // Check firewall settings
   ```

2. **File Upload Issues:**

   ```bash
   # Check directory permissions
   sudo chown -R www-data:www-data /var/www/html/uploads
   sudo chmod -R 755 /var/www/html/uploads
   ```

3. **Database Connection:**
   ```javascript
   // Check connection string
   // Verify network access
   // Check authentication credentials
   ```

### Performance Monitoring

```bash
# Monitor server resources
htop
iotop
netstat -an | grep :3001

# Monitor application performance
pm2 monit
```

## Rollback Strategy

```bash
# Quick rollback with PM2
pm2 stop quiktalk-server
pm2 start previous_version/index.js --name quiktalk-server

# Database rollback (if needed)
mongorestore --uri="mongodb+srv://..." /backups/previous_backup
```

This deployment guide provides comprehensive instructions for deploying QuikTalk Chat App in various environments, from development to production-scale deployments.
