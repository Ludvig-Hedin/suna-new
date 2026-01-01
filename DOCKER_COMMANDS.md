# Docker Commands for Kortix/Suna

## Quick Commands

### Stop Services
```bash
cd new-agent/suna
docker compose down
```

### Start Services
```bash
cd new-agent/suna
docker compose up -d
```

### Restart Services
```bash
cd new-agent/suna
docker compose restart
```

### Restart Specific Service
```bash
cd new-agent/suna
docker compose restart backend    # or frontend, redis, worker
```

---

## Using the Start Script (Recommended)

The project includes a `start.py` script that makes it easier:

### Stop Services
```bash
cd new-agent/suna
python start.py
# Then choose to stop when prompted
```

### Start Services
```bash
cd new-agent/suna
python start.py
# Then choose to start when prompted
```

Or force start/stop without prompts:
```bash
python start.py -f
```

---

## Other Useful Commands

### View Logs
```bash
# All services
docker compose logs -f

# Specific service
docker compose logs -f backend
docker compose logs -f frontend
```

### Check Status
```bash
docker compose ps
```

### Rebuild Containers (after code changes)
```bash
docker compose up -d --build
```

### View Resource Usage
```bash
docker stats
```

### Stop Everything
```bash
docker compose down
```

### Stop and Remove Volumes (⚠️ deletes data)
```bash
docker compose down -v
```

---

## Services

- **backend** - API server (port 8000)
- **frontend** - Next.js app (port 3000)
- **redis** - Cache/queue (port 6379)
- **worker** - Background tasks

