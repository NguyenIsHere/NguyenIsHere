# Hi, I'm Nguyen!  

**Software Engineering Student**  

I'm Nguyen, a third-year **Software Engineering** student at the **University of Information Technology**, Ho Chi Minh City, Vietnam. 

Currently learning SpringBoot and NodeJS. I'm also trying to integrate AI into my projects.

## GitHub Journey  

### Time Spent  
[![WakaTime stats](https://github-readme-stats.vercel.app/api/wakatime?username=NguyenIsHere&layout=compact)](https://github.com/anuraghazra/github-readme-stats)  

### Languages  
![Top Langs](https://github-readme-stats.vercel.app/api/top-langs/?username=NguyenIsHere&langs_count=20&hide_progress=false&layout=compact)  

flowchart TD
  %% Client kết nối và xác thực
  subgraph "Client"
    C[User Client]
  end

  subgraph "Edge & LB"
    DNS["DNS"]
    LB["Load Balancer\n(TCP mode)"]
  end

  subgraph "WebSocket Layer"
    WS1["WebSocket Pod<br/>(Socket.io + Kafka Adapter)"]
    Auth["Auth Service<br/>(gRPC)"]
    Registry["Connection Registry<br/>(in-memory)"]
  end

  subgraph "Messaging Bus"
    Kafka["Kafka Cluster\n(topics & partitions)"]
  end

  subgraph "Persistence"
    DBWriter["DB-Writer Consumer\n(batch insert)"]
    Mongo["MongoDB Sharded Cluster"]
  end

  subgraph "Offline/Resync"
    ClientOffline["Client Offline"]
    ClientReconnect["Client Reconnect"]
    FetchHistoric["Fetch from MongoDB\nor Replay Kafka"]
  end

  %% Luồng kết nối
  C -->|1. DNS lookup| DNS
  DNS -->|2. TCP WebSocket handshake| LB
  LB -->|3. Upgrade → WebSocket| WS1
  WS1 -->|4. Auth token| Auth
  Auth -->|5. OK / userId| WS1
  WS1 -->|6. register connection| Registry

  %% Luồng nhắn tin real-time
  C -->|7. Send message| WS1
  WS1 -->|8. Publish| Kafka
  Kafka -->|9. Consume & broadcast| WS1
  WS1 -->|10. Push to client| C

  %% Luồng ghi dữ liệu
  Kafka -->|11. Batch consume| DBWriter
  DBWriter -->|12. insertMany| Mongo

  %% Luồng offline & resync
  C -->|13. disconnect| ClientOffline
  ClientOffline -->|14. unsubscribe| Registry
  C -->|15. reconnect| ClientReconnect
  ClientReconnect -->|16. WebSocket + Auth| WS1
  WS1 -->|17. read lastTimestamp| FetchHistoric
  FetchHistoric -->|18. replay missing| C

