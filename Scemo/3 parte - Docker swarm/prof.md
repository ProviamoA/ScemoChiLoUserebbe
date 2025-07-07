# Swarm

## Prerequisites

- Docker Engine (version 20+ recommended)
- Docker Swarm initialized (single node)
- Network access to [pypi.org](https://pypi.org) (for building Flask image)

---

## 1. Initialize Swarm and Create Overlay Network

```bash
docker swarm init
docker network create --driver=overlay lbnetwork
```

## 2. Build Local Images

### Build Go Image
```bash
cd swarm-golang
docker build -t swarm-golang:latest .
```

### Build no Image
```bash
cd swarm-node
docker build -t swarm-node:latest .
```

## 3. Deploy the Stacks
```bash
# Deploy Traefik
docker stack deploy -c ./swarm-traefik/docker-compose.yml traefik

# Deploy Flask
docker stack deploy -c ./swarm-golang/docker-compose.yml go

# Deploy .NET
docker stack deploy -c ./swarm-node/docker-compose.yml node
```

## 4. Update /etc/hosts File
Add the following line to your /etc/hosts (or C:\Windows\System32\drivers\etc\hosts on Windows):
```
127.0.0.1 swarm-golang.localhost swarm-node.localhost
```

## 5. Test the Services
Visit http://swarm-golang.localhost:8888

Visit http://swarm-node.localhost:8888

Refresh several times: you should see the container IP change (demonstrating load balancing across 5 replicas).

Traefik dashboard: http://localhost:8080

## 6. Clean Up
```bash
docker stack rm traefik
docker stack rm go
docker stack rm node
```


## License
This project is for educational purposes.

## Su trafeik mettere manager
