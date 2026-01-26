## 👋 Welcome to redis 🚀

Redis - In-memory data structure store (cache, message broker, database)

## 📋 Description

Redis is an open-source, in-memory data structure store used as a database, cache, message broker, and streaming engine. Known for blazing-fast performance and versatile data structures.

## 🚀 Services

- **db**: Redis 7 Alpine (`redis:7-alpine`)

## 📦 Installation

### Using curl
```shell
curl -q -LSsf "https://raw.githubusercontent.com/composemgr/redis/main/docker-compose.yaml" -o compose.yml
```

### Using git
```shell
git clone "https://github.com/composemgr/redis" ~/.local/srv/docker/redis
cd ~/.local/srv/docker/redis
docker compose up -d
```

### Using composemgr
```shell
composemgr install redis
```

## 🔧 Configuration

### Environment Variables

```shell
TZ=America/New_York
REDIS_PASSWORD=changeme_admin_password
```

### Redis Configuration

Edit `rootfs/config/redis/redis.conf` for advanced settings:
```conf
# Memory
maxmemory 256mb
maxmemory-policy allkeys-lru

# Persistence
save 900 1
save 300 10
save 60 10000

# Security
requirepass your_password_here

# Networking
bind 0.0.0.0
protected-mode yes
```

## 🌐 Access

- **Redis**: localhost:6379 (from Docker host)
- **Connection**: `redis://localhost:6379` or `redis://:password@localhost:6379`

### Connect from another container
```yaml
services:
  app:
    environment:
      REDIS_URL: redis://:password@redis-db:6379/0
    depends_on:
      - redis
```

## 📂 Volumes

- `./rootfs/db/redis/redis` - RDB snapshots and AOF logs
- `./rootfs/config/redis` - Redis configuration

## 🔐 Security

- **Set password**: Use `requirepass` in config
- **Bind to localhost**: Or use firewall rules
- **Rename dangerous commands**: CONFIG, FLUSHALL, etc.
- **Use ACLs**: Redis 6+ access control lists
- **Regular backups**: Enable RDB/AOF persistence

### Example ACL Configuration
```
user default on nopass ~* &* +@all
user app on >password ~app:* &* +@all -@dangerous
```

## 🔍 Logging

```shell
docker compose logs -f db
```

## 🛠️ Management

### Connect to Redis CLI
```shell
docker compose exec db redis-cli -a password
```

### Common Commands
```shell
# Test connection
docker compose exec db redis-cli -a password PING

# Get info
docker compose exec db redis-cli -a password INFO

# Monitor commands
docker compose exec db redis-cli -a password MONITOR

# Check memory usage
docker compose exec db redis-cli -a password INFO memory
```

## 🔄 Backup & Restore

### Backup
```shell
# Trigger RDB snapshot
docker compose exec db redis-cli -a password BGSAVE

# Copy RDB file
cp rootfs/db/redis/redis/dump.rdb backup-$(date +%Y%m%d).rdb
```

### Restore
```shell
# Stop Redis
docker compose down

# Replace RDB file
cp backup-YYYYMMDD.rdb rootfs/db/redis/redis/dump.rdb

# Start Redis
docker compose up -d
```

## 📊 Performance

### Memory Usage
```shell
docker compose exec db redis-cli -a password INFO memory | grep used_memory_human
```

### Connected Clients
```shell
docker compose exec db redis-cli -a password CLIENT LIST
```

### Slow Log
```shell
docker compose exec db redis-cli -a password SLOWLOG GET 10
```

## 📋 Requirements

- Docker Engine 20.10+
- Docker Compose V2+
- 128MB+ RAM minimum (depends on dataset)

## 🆘 Troubleshooting

### Out of memory
```shell
# Check memory usage
docker compose exec db redis-cli -a password INFO memory

# Set eviction policy
docker compose exec db redis-cli -a password CONFIG SET maxmemory-policy allkeys-lru
```

### Connection refused
```shell
# Check if running
docker compose ps

# Test connection
docker compose exec db redis-cli -a password PING
```

## 🤝 Author

🤖 casjay: [Github](https://github.com/casjay) 🤖  
🦄 composemgr: [Github](https://github.com/composemgr) 🦄
