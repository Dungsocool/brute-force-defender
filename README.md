**Project Structure**

```text
brute-force-defender/
├── docker-compose.yml
├── README.md
├── nginx-server/
│   ├── Dockerfile
│   ├── nginx.conf
│   └── block_ips.conf
├── security-watcher/
│   ├── watcher.py
│   └── requirements.txt
└── attacker-bot/
    └── attack.sh
