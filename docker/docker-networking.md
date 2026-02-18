# Docker Networking

**Docker Networking** enables containers to communicate with each other, the host system, and external networks. Understanding Docker networking is essential for building multi-container applications.

Documentation: [Docker Networking Documentation](https://docs.docker.com/network/)

---

## Network Drivers

Docker provides several network drivers for different use cases:

### Bridge (Default)

The default network driver. Containers on the same bridge network can communicate with each other.

```bash
# Create bridge network
docker network create my-bridge-network

# Run container on bridge network
docker run -d --name web --network my-bridge-network nginx

# Containers can communicate by name
docker run -d --name app --network my-bridge-network myapp
```

**Use Case:** 
- Standalone containers on single host
- Multiple containers need to communicate
- Default for most applications

### Host

Removes network isolation between container and host. Container uses host's network directly.

```bash
# Run container with host networking
docker run -d --network host nginx
```

**Use Case:**
- Performance-critical applications
- Container needs to handle lots of ports
- Network performance is priority

**Note:** Port mapping doesn't work with host networking

### None

Disables all networking for the container.

```bash
# Run container without network
docker run -d --network none alpine
```

**Use Case:**
- Containers that don't need network access
- Maximum network isolation
- Security-sensitive applications

### Overlay

Enables swarm services to communicate with each other across multiple Docker hosts.

```bash
# Create overlay network (requires swarm mode)
docker network create -d overlay my-overlay-network

# Deploy service on overlay network
docker service create --network my-overlay-network nginx
```

**Use Case:**
- Docker Swarm clusters
- Multi-host container communication
- Distributed applications

### Macvlan

Assigns a MAC address to container, making it appear as a physical device on the network.

```bash
# Create macvlan network
docker network create -d macvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  -o parent=eth0 \
  my-macvlan-network
```

**Use Case:**
- Legacy applications expecting physical network connection
- Container needs to appear as physical host
- Network monitoring applications

---

## Network Management Commands

**List Networks:**
```bash
# List all networks
docker network ls

# List with filter
docker network ls --filter driver=bridge
```

**Inspect Network:**
```bash
# View network details
docker network inspect bridge

# Show connected containers
docker network inspect my-network --format='{{range .Containers}}{{.Name}} {{end}}'
```

**Create Network:**
```bash
# Create basic bridge network
docker network create my-network

# Create with specific subnet
docker network create --subnet=172.18.0.0/16 my-network

# Create with gateway
docker network create \
  --subnet=172.20.0.0/16 \
  --gateway=172.20.0.1 \
  my-network
```

**Connect/Disconnect Containers:**
```bash
# Connect container to network
docker network connect my-network container-name

# Disconnect container from network
docker network disconnect my-network container-name

# Connect with specific IP
docker network connect --ip 172.20.0.10 my-network container-name
```

**Remove Network:**
```bash
# Remove single network
docker network rm my-network

# Remove all unused networks
docker network prune

# Remove specific unused networks
docker network prune --filter "label=my-label"
```

---

## Container Communication

### By Container Name

Containers on the same network can communicate using container names as hostnames:

```bash
# Create network
docker network create my-app-network

# Start database container
docker run -d \
  --name database \
  --network my-app-network \
  postgres

# Start application container (can connect to 'database' hostname)
docker run -d \
  --name app \
  --network my-app-network \
  -e DATABASE_HOST=database \
  myapp
```

### By IP Address

```bash
# Find container IP
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container-name

# Connect using IP (not recommended - use names instead)
docker run --network my-network myapp --db-host=172.18.0.2
```

### Port Publishing

```bash
# Publish single port
docker run -p 8080:80 nginx

# Publish to specific interface
docker run -p 127.0.0.1:8080:80 nginx

# Publish all exposed ports
docker run -P nginx

# Publish multiple ports
docker run -p 8080:80 -p 8443:443 nginx

# Publish UDP port
docker run -p 53:53/udp dns-server
```

---

## Docker Compose Networking

Docker Compose automatically creates a network for your application:

**docker-compose.yml:**
```yaml
version: '3'

services:
  web:
    image: nginx
    ports:
      - "8080:80"
    networks:
      - frontend
      - backend

  app:
    image: myapp
    networks:
      - backend

  db:
    image: postgres
    networks:
      - backend

networks:
  frontend:
    driver: bridge
  backend:
    driver: bridge
```

**Custom Network Configuration:**
```yaml
networks:
  my-network:
    driver: bridge
    ipam:
      config:
        - subnet: 172.28.0.0/16
          gateway: 172.28.0.1

  external-network:
    external: true
    name: existing-network
```

---

## Network Isolation

**Multiple Networks:**
```bash
# Create separate networks
docker network create frontend
docker network create backend

# Web server on both networks
docker run -d \
  --name web \
  --network frontend \
  nginx

docker network connect backend web

# App only on backend
docker run -d \
  --name app \
  --network backend \
  myapp

# Database only on backend
docker run -d \
  --name db \
  --network backend \
  postgres
```

This creates isolation: external clients → frontend → web → backend → app/db

---

## DNS and Service Discovery

Docker provides built-in DNS for container name resolution:

**Automatic DNS:**
```bash
# Containers can resolve each other by name
docker run -d --name db postgres
docker run --name app --link db myapp

# Better approach - use custom network
docker network create mynet
docker run -d --name db --network mynet postgres
docker run --name app --network mynet myapp
# Now 'app' can connect to 'db' hostname
```

**DNS Configuration:**
```bash
# Custom DNS server
docker run --dns 8.8.8.8 alpine

# Multiple DNS servers
docker run --dns 8.8.8.8 --dns 8.8.4.4 alpine

# DNS search domain
docker run --dns-search example.com alpine

# Custom hostname
docker run --hostname myhost alpine
```

---

## Network Troubleshooting

**Inspect Container Network:**
```bash
# View container network settings
docker inspect container-name

# View specific network info
docker inspect -f '{{json .NetworkSettings.Networks}}' container-name | jq

# Check container IP
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container-name

# Check ports
docker port container-name
```

**Test Connectivity:**
```bash
# Ping from one container to another
docker exec container1 ping container2

# Test DNS resolution
docker exec container1 nslookup container2

# Check network from inside container
docker run --network my-network nicolaka/netshoot

# Curl from container
docker exec container1 curl http://container2
```

**Debug Tools Container:**
```bash
# Run network debug container
docker run --name netshoot --rm -it \
  --network container:web \
  nicolaka/netshoot /bin/bash

# Available tools: tcpdump, curl, wget, nslookup, dig, netstat, etc.
```

**Common Issues:**
```bash
# Container can't resolve names
# - Check container is on custom network (not default bridge)
# - Verify DNS settings

# Port not accessible
# - Check port mapping: docker port container-name
# - Check firewall rules
# - Verify service is listening: docker exec container netstat -tulpn

# Can't connect between containers
# - Verify on same network: docker network inspect network-name
# - Check container names are correct
# - Test with IP address first
```

---

## Network Security

**Network Policies:**
```bash
# Create isolated network
docker network create --internal secure-network

# Containers can communicate internally but not externally
docker run -d --network secure-network myapp
```

**Encryption:**
```bash
# Overlay network with encryption (Docker Swarm)
docker network create \
  --driver overlay \
  --opt encrypted \
  secure-overlay
```

**Network Segmentation:**
- Use separate networks for different tiers (frontend, backend, database)
- Limit container network access with `--internal` flag
- Use firewall rules on host
- Implement network policies in Kubernetes

---

## Advanced Configurations

**IP Address Management (IPAM):**
```yaml
networks:
  custom-network:
    driver: bridge
    ipam:
      driver: default
      config:
        - subnet: "172.28.0.0/16"
          ip_range: "172.28.5.0/24"
          gateway: "172.28.0.1"
          aux_addresses:
            host1: "172.28.1.5"
            host2: "172.28.1.6"
```

**Network Options:**
```bash
# MTU size
docker network create -o com.docker.network.driver.mtu=1450 my-network

# Enable ICC (Inter-Container Communication)
docker network create -o com.docker.network.bridge.enable_icc=true my-network

# Bridge name
docker network create -o com.docker.network.bridge.name=my-bridge my-network
```

**IPv6 Support:**
```bash
# Enable IPv6
docker network create --ipv6 \
  --subnet=2001:db8:1::/64 \
  my-ipv6-network
```

---

## Performance Tuning

**Network Performance:**
- Use `--network host` for maximum performance (trade-off: less isolation)
- Use `--net=container:name` to share network namespace
- Optimize MTU size for your network
- Use overlay networks only when needed (multi-host)

**Monitoring:**
```bash
# Network statistics
docker stats container-name

# Network usage
docker inspect container-name | grep -A 20 Networks
```

---

## Related Topics

- [[docker/docker|Docker]] - Container platform
- [[docker/docker-compose|Docker Compose]] - Multi-container applications
- [[kubernetes/kubernetes|Kubernetes]] - Container orchestration
- [[networking/tcp-vs-udp|TCP vs UDP]] - Network protocols
- [[linux/iptables|iptables]] - Linux firewall

---

## Best Practices

✅ Use custom bridge networks instead of default bridge
✅ Use container names for communication (not IPs)
✅ Segment networks by application tier
✅ Use `--internal` for networks that don't need external access
✅ Document network architecture
✅ Use Docker Compose for multi-container networking
✅ Avoid `--link` (deprecated), use custom networks
✅ Enable network encryption for sensitive data
✅ Monitor network performance
✅ Use meaningful network names
✅ Clean up unused networks regularly (`docker network prune`)

---

## Quick Reference

```bash
# Create network
docker network create my-network

# Run container on network
docker run --network my-network nginx

# Connect existing container
docker network connect my-network container-name

# Inspect network
docker network inspect my-network

# List networks
docker network ls

# Remove network
docker network rm my-network

# Remove unused networks
docker network prune
```