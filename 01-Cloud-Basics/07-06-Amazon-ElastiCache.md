# Amazon ElastiCache

Amazon ElastiCache는 AWS에서 **Redis 또는 Memcached를 Managed Service로 제공**하는 서비스이다.

RDS가 Managed Relational Database를 제공하는 것처럼, ElastiCache는 **Managed In-Memory Cache**를 제공한다.

```text
Amazon ElastiCache
       │
       ├─ Redis
       └─ Memcached
```

Cache는 데이터를 Memory에 저장하여 **매우 높은 Performance와 낮은 Latency**를 제공한다.

주요 사용 목적은 다음과 같다.

- Read-intensive Workload에서 Database 부하 감소
- 자주 조회되는 데이터의 빠른 반환
- User Session 저장
- Application을 Stateless하게 구성

AWS는 ElastiCache의 OS 유지 관리, Patch, 설정, Monitoring, 장애 복구 및 Backup 등을 관리한다.

단, ElastiCache를 활성화한다고 Application이 자동으로 Cache를 사용하는 것은 아니다.

Application이 Cache를 조회하고 관리하도록 **Application Code 변경이 필요하다.**

---

# 1. Cache Hit & Cache Miss

Application은 Database를 조회하기 전에 ElastiCache를 먼저 확인할 수 있다.

## Cache Hit

요청한 데이터가 이미 Cache에 존재하는 경우이다.

```text
Application
     ↓
ElastiCache
     ↓
Data 있음
     ↓
Cache Hit
     ↓
바로 반환
```

Database까지 요청할 필요가 없으므로 **Latency와 Database Read 부하를 줄일 수 있다.**

## Cache Miss

요청한 데이터가 Cache에 존재하지 않는 경우이다.

```text
Application
     ↓
ElastiCache
     ↓
Data 없음
     ↓
Cache Miss
     ↓
Database
     ↓
Data Read
     ↓
Cache에 저장
     ↓
Application
```

이후 같은 데이터가 요청되면 Cache Hit가 발생할 수 있다.

### 핵심

```text
Cache Hit
→ Cache에 Data 있음
→ Database 조회 X

Cache Miss
→ Cache에 Data 없음
→ Database 조회
→ 결과를 Cache에 저장
```

---

# 2. Cache Invalidation

Database의 원본 데이터가 변경되어도 Cache에 이전 데이터가 남아 있을 수 있다.

```text
Cache
Price = 10,000

Database
Price = 8,000
```

이처럼 Cache에 남아 있는 오래된 데이터를 **Stale Data**라고 한다.

따라서 Cache를 사용할 때는 오래된 데이터를 제거하거나 갱신하기 위한 **Cache Invalidation Strategy**가 필요하다.

```text
Caching
→ Database Load 감소

하지만

Stale Data 방지
→ Cache Invalidation 필요
```

---

# 3. Lazy Loading

Lazy Loading은 **Cache Miss가 발생했을 때만 Database에서 데이터를 읽어 Cache에 저장**하는 방식이다.

```text
Application
     ↓
   Cache
     │
     ├─ Hit
     │   ↓
     │ Cache에서 반환
     │
     └─ Miss
         ↓
      Database
         ↓
      Data Read
         ↓
      Cache 저장
```

즉, 실제로 요청된 데이터만 필요할 때 Cache에 들어간다.

```text
Cache Miss
→ Database Read
→ Cache에 Load
```

단, Database의 데이터가 변경된 후에도 Cache에 이전 데이터가 남아 **Stale Data가 발생할 수 있다.**

### 시험 핵심

`Lazy Loading → Cache Miss일 때 Load → Stale Data 가능`

---

# 4. Write Through

Write Through는 **Database에 데이터가 기록될 때마다 Cache에도 데이터를 추가하거나 갱신**하는 방식이다.

```text
       Data Write
        /      \
       ▼        ▼
    Cache    Database
    Update    Update
```

Database의 변경과 함께 Cache도 갱신하므로 강의에서는 Stale Data를 방지하는 방식으로 설명한다.

### Lazy Loading vs Write Through

```text
Lazy Loading
→ Read할 때 Cache Miss
→ DB에서 가져와 Cache에 저장

Write Through
→ DB에 Write
→ Cache도 Add / Update
```

### 시험 핵심

```text
Lazy Loading
→ 필요할 때 Cache에 Load

Write Through
→ DB Write 시 Cache도 Update
```

---

# 5. Session Store

ElastiCache는 User Session을 저장하는 **Session Store**로 사용할 수 있다.

각 Application Instance가 자신의 Memory에 Session을 저장하면 다른 Instance로 요청이 전달되었을 때 Session 정보를 사용할 수 없는 문제가 생길 수 있다.

```text
User
 ↓
EC2 A
 ↓
Session이 EC2 A에만 존재
```

Session을 ElastiCache에 저장하면 여러 Application Instance가 같은 Session을 사용할 수 있다.

```text
              ElastiCache
             Shared Session
                  ↑
          ┌───────┴───────┐
          │               │
        EC2 A           EC2 B
```

따라서 사용자가 다른 Instance로 연결되어도 Session을 가져올 수 있다.

이를 통해 Application Instance가 직접 Session State를 저장할 필요가 없어져 **Stateless Application**을 구성할 수 있다.

## TTL

Session Data에는 **TTL (Time To Live)**을 설정하여 일정 시간이 지나면 자동으로 만료되도록 할 수 있다.

```text
User Session
     ↓
ElastiCache
     ↓
TTL
     ↓
일정 시간 후 Expire
```

### 시험 핵심

`Shared Session + Stateless Application + TTL → ElastiCache`

---

# 6. Redis

Redis는 강의에서 **Replication과 High Availability를 지원하는 Cache Engine**으로 설명한다.

```text
Redis Main
     │
     │ Replication
     ▼
Redis Replica
```

같은 데이터를 다른 Node에 복제할 수 있으므로 장애 대응과 Read Scaling에 활용할 수 있다.

### 주요 특징

- Multi-AZ
- Automatic Failover
- Read Replicas
- High Availability
- AOF Persistence
- Backup / Restore
- Sets / Sorted Sets

```text
Redis Main 💥
      ↓
Redis Replica
      ↓
Failover
```

### 시험 핵심

```text
Replication
High Availability
Read Replicas
Persistence

→ Redis
```

---

# 7. Memcached

Memcached는 강의에서 여러 Node에 Cache 데이터를 **분할하여 저장하는 구조**로 설명한다.

```text
             Cache Data
                 │
          ┌──────┼──────┐
          ▼      ▼      ▼
       Node A  Node B  Node C

       Data A  Data B  Data C
```

이처럼 데이터를 여러 Node에 나누어 저장하는 것을 **Sharding**이라고 한다.

```text
Sharding
→ 데이터를 여러 Node에 분할하여 저장
```

강의에서 설명하는 일반적인 Memcached 구조에서는 Redis와 같은 Replication 구조가 없다.

따라서 특정 Node에 장애가 발생하면 해당 Node가 가지고 있던 Cache Data를 잃을 수 있다.

```text
Node A       Node B 💥       Node C
Data A       Data B ❌       Data C
```

Cache의 원본 데이터가 Database에 존재한다면 Cache Miss를 통해 다시 가져올 수 있다.

```text
Cache Data 유실
      ↓
Cache Miss
      ↓
Database Read
      ↓
Cache 다시 생성
```

### 주요 특징

- Multi-node
- Data Partitioning / Sharding
- No Replication
- No High Availability
- Non-Persistent
- Multi-threaded Architecture

강의에서는 Backup / Restore가 **Memcached Serverless**에서 지원된다고 설명한다.

---

# 8. Redis vs Memcached

강의에서 사용하는 핵심 비교 이미지는 다음과 같다.

```text
Redis
→ 같은 Data를 다른 Node에 Replication

Main
🍎🍌🍇
   │
   ▼
Replica
🍎🍌🍇


Memcached
→ Data를 여러 Node에 Sharding

전체 Data
🍎🍌🍇🍉🍓🥝
       │
   ┌───┼───┐
   ▼   ▼   ▼

Node A  Node B  Node C
🍎🍌    🍇🍉    🍓🥝
```

| Redis | Memcached |
|---|---|
| Replication | Sharding |
| Multi-AZ | Multi-node |
| Automatic Failover | No High Availability |
| Read Replicas | No Replication |
| Persistence | Non-Persistent |
| Backup / Restore | Serverless에서 Backup / Restore |
| Sets / Sorted Sets | Multi-threaded |

> 이 비교는 강의에서 Redis와 Memcached의 차이를 이해하기 위해 단순화한 구조이다.

### 시험 핵심

```text
Redis
→ Replication
→ HA
→ Persistence

Memcached
→ Sharding
→ Multi-node
→ No Replication
```

---

# 9. ElastiCache Security

## Redis Security

강의에서는 Redis에 다음 보안 기능을 설명한다.

- Security Groups
- Redis AUTH
- IAM Authentication
- SSL Encryption in Transit

```text
Client
  ↓
Security Group
  ↓
SSL
  ↓
Redis AUTH / IAM Authentication
  ↓
Redis
```

### Redis AUTH

Redis AUTH를 사용하여 Redis Cluster에 **Password / Token**을 설정할 수 있다.

Security Group에 추가적인 보안 계층을 제공한다.

```text
Security Group
→ Network Access

Redis AUTH
→ Password / Token
```

Redis에서는 **IAM Authentication**도 사용할 수 있다.

ElastiCache에 정의하는 IAM Policy는 **AWS API-level Security**에 사용된다.

### Encryption in Transit

Redis는 SSL을 사용한 **Encryption in Transit**을 지원한다.

```text
Client
   │
   │ SSL
   ▼
Redis
```

---

## Memcached Security

Memcached는 **SASL-based Authentication**을 지원한다.

SAA에서는 세부 구조보다 다음 연결을 기억한다.

```text
Memcached
→ SASL Authentication
```

### Security 핵심

```text
Redis
→ Redis AUTH
→ IAM Authentication
→ SSL

Memcached
→ SASL
```

---

# 10. Redis Sorted Sets & Leaderboards

Redis의 **Sorted Sets**는 요소의 Unique함과 Ordering을 보장한다.

요소가 추가될 때 Score를 기반으로 순위를 구성할 수 있으므로 **Real-time Leaderboard** 구현에 유용하다.

```text
Player       Score

Player A      9500
Player B      8200
Player C      7100

        ↓

Leaderboard

1. Player A
2. Player B
3. Player C
```

Application이 직접 복잡한 Ranking Logic을 구현하는 대신 Redis Sorted Sets를 활용할 수 있다.

### 시험 핵심

`Real-time Gaming Leaderboard → Redis Sorted Sets`

---

# 11. 시험 핵심 정리

```text
Amazon ElastiCache
→ Managed Redis / Memcached
→ In-Memory
→ High Performance
→ Low Latency

Read-intensive Workload
→ ElastiCache
→ Database Load 감소


Cache Hit
→ Cache에 Data 있음
→ DB 조회 X

Cache Miss
→ Cache에 Data 없음
→ DB 조회
→ Cache에 저장


Lazy Loading
→ Cache Miss일 때 Load
→ Stale Data 가능

Write Through
→ DB Write 시 Cache도 Update


Session Store
→ Stateless Application
→ TTL로 Session 만료


Redis
→ Replication
→ Multi-AZ / Auto Failover
→ Read Replicas
→ Persistence
→ Sorted Sets

Memcached
→ Sharding
→ Multi-node
→ No Replication
→ Non-Persistent
→ Multi-threaded


Redis Security
→ Redis AUTH
→ IAM Authentication
→ SSL

Memcached Authentication
→ SASL


Real-time Leaderboard
→ Redis Sorted Sets
```

---

# 日本語まとめ

## Amazon ElastiCache

Amazon ElastiCacheは、RedisまたはMemcachedをManaged Serviceとして提供するIn-Memory Cacheサービスである。

Read-intensiveなWorkloadでDatabaseへのアクセスを減らし、低Latencyでデータを取得できる。

## Cache Hit / Cache Miss

```text
Cache Hit
→ CacheにDataが存在
→ Databaseへのアクセス不要

Cache Miss
→ CacheにDataが存在しない
→ Databaseから取得
→ Cacheに保存
```

## Caching Strategy

**Lazy Loading**はCache Missが発生した時にDatabaseからデータを取得してCacheに保存する。Cache内のDataが古くなる可能性がある。

**Write Through**はDatabaseへの書き込み時にCacheも追加・更新する。

## Session Store

User SessionをElastiCacheに保存することで、複数のApplication Instanceが同じSessionを利用できる。

これによりStateless Applicationを構成でき、TTLを使用してSessionを期限切れにできる。

## Redis

- Replication
- Multi-AZ
- Automatic Failover
- Read Replicas
- Persistence
- Backup / Restore
- Sorted Sets

RedisではRedis AUTH、IAM Authentication、SSLによる通信中の暗号化を利用できる。

## Memcached

- Multi-node
- Sharding
- No Replication
- Non-Persistent
- Multi-threaded
- SASL Authentication

## Leaderboard

Real-time Gaming Leaderboardには**Redis Sorted Sets**を利用できる。

---

# English Summary

## Amazon ElastiCache

Amazon ElastiCache provides managed Redis and Memcached as high-performance, low-latency in-memory caching services.

## Caching

A **Cache Hit** returns data directly from the cache.

A **Cache Miss** requires the application to retrieve data from the database and store it in the cache.

**Lazy Loading** loads data after a Cache Miss and can result in stale cached data.

**Write Through** updates the cache whenever data is written to the database.

## Session Store

ElastiCache can store shared user sessions, allowing application instances to remain stateless. TTL can be used to expire session data.

## Redis

Redis supports replication, Multi-AZ, automatic failover, read replicas, persistence, backup/restore, and Sorted Sets.

Redis security includes Redis AUTH, IAM Authentication, and SSL encryption in transit.

## Memcached

Memcached uses multiple nodes and sharding. In the course comparison, it does not provide replication or persistence and supports a multi-threaded architecture.

Memcached supports SASL-based authentication.

## Leaderboard

Redis Sorted Sets can be used to build real-time gaming leaderboards.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Cache | キャッシュ | 캐시 |
| In-Memory | インメモリ | 인메모리 |
| Cache Hit | キャッシュヒット | 캐시 히트 |
| Cache Miss | キャッシュミス | 캐시 미스 |
| Cache Invalidation | キャッシュ無効化 | 캐시 무효화 |
| Stale Data | 古いデータ | 오래된 데이터 |
| Lazy Loading | 遅延読み込み | 지연 로딩 |
| Write Through | ライトスルー | 라이트 스루 |
| Session Store | セッションストア | 세션 저장소 |
| Stateless | ステートレス | 무상태 |
| TTL (Time To Live) | 有効期限 | 데이터 유효 시간 |
| Replication | レプリケーション | 복제 |
| Sharding | シャーディング | 샤딩 / 데이터 분할 |
| Persistence | 永続化 | 영속성 |
| Automatic Failover | 自動フェイルオーバー | 자동 장애 조치 |
| Read Replica | リードレプリカ | 읽기 전용 복제본 |
| Redis AUTH | Redis AUTH | Redis 인증 |
| SASL | SASL | SASL 인증 |
| Sorted Set | ソート済みセット | 정렬된 집합 |
| Leaderboard | リーダーボード | 순위표 |

---

# Review Questions

1. ElastiCache가 Read-intensive Workload에서 Database 부하를 줄일 수 있는 이유는 무엇인가?
2. Cache Hit와 Cache Miss의 차이는 무엇인가?
3. Lazy Loading과 Write Through는 Cache를 언제 갱신하는가?
4. ElastiCache에 User Session을 저장하면 Application을 Stateless하게 만들 수 있는 이유는 무엇인가?
5. 강의에서 Redis의 Replication과 Memcached의 Sharding을 어떻게 구분하는가?
6. Redis와 Memcached에서 사용하는 Authentication 방식은 각각 무엇인가?
7. Real-time Gaming Leaderboard를 구현할 때 Redis의 어떤 기능을 사용할 수 있는가?
