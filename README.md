# P2P File Sharing System with Blockchain Logging

This project demonstrates a peer-to-peer file sharing system with a blockchain-based logging mechanism for upload/download events using Docker, Flask, Truffle, and Web3.py.

---

## Setup Instructions

### 1. Clean Docker Environment

```
docker-compose down --volumes --remove-orphans
```

(Optional, if you're cleaning up completely):

```
docker rm -f $(docker ps -aq)        # For Linux/macOS
```

(Windows CMD doesn't support `$(...)`, so skip this on Windows or use PowerShell)

---

### 2. Build and Start Services

```
docker-compose up -d --build
```

This starts:

* MySQL (db)
* Tracker server (Flask)
* Peer1 and Peer2
* Ganache (Ethereum test blockchain)

---

### 3. Run Smart Contract Migrations

```
truffle migrate --reset --network development
```

Copy the deployed contract address from the output and update it in `client.py`:

```python
contract_address = "0x..."  # Replace with actual deployed address
```

---

### 4. Rebuild Peers (if `client.py` changed)

```
docker-compose build peer1
```

```
docker-compose up -d peer1
```

```
docker-compose build peer2
```

```
docker-compose up -d peer2
```

---

### 5. Upload a File from Peer1

```bash
docker exec -it p2p-peer1-1 bash
python3 client.py
```

```
Enter choice: 1
Enter file path to upload: /app/files/test.txt
```

> Make sure `./files1/test.txt` exists on the host machine.

---

### 6. Download from Peer2

```bash
docker exec -it p2p-peer2-1 bash
python3 client.py
```

```
Enter choice: 2
Enter file hash to download: a6ed0c78...  # paste the hash from Peer1's upload
```

> The file will download to `downloads/test.txt` in Peer2's container.

---

### 7. View Blockchain Logs

In any peer:

```
Enter choice: 5
```

You will see all logged upload/download events recorded on the Ethereum blockchain via Ganache.

---

### 8. Check Port Status (Optional)

To confirm if peer's server is running:

```bash
apt update && apt install -y net-tools
netstat -tuln | grep 6000
```

---

## Additional Notes

* Peers use internal Docker IPs and must all listen on **port 6000 internally**.
* Host ports (`6001`, `6002`, etc.) are only for manual testing.
* All file paths inside containers should be `/app/files/...`.
* Blockchain logs are stored via `FileShareLogger.sol`.

---

## Summary

| Task                | Command/Path                                     |
| ------------------- | ------------------------------------------------ |
| Clean environment   | `docker-compose down --volumes --remove-orphans` |
| Start services      | `docker-compose up -d --build`                   |
| Migrate contract    | `truffle migrate --reset --network development`  |
| Upload file (Peer1) | `/app/files/test.txt` (inside container)         |
| Download (Peer2)    | Use file hash from Peer1                         |
| View logs           | Choice `5` in menu                               |

<sub>CREATED BY: RESHMA MERIN THOMAS
