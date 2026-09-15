# Amazon ElastiCache

Amazon ElastiCache는 AWS에서 **Redis 또는 Memcached를 Managed Service로 제공**하는 서비스이다.

RDS가 Managed Relational Database를 제공하는 것처럼, ElastiCache는 Managed In-Memory Cache를 제공한다.

```text
Amazon ElastiCache
       │
       ├─ Redis
       │
       └─ Memcached
```

ElastiCache는 데이터를 Memory에 저장하여 **매우 낮은 Latency와 높은 Performance**를 제공한다.

주요 목적은 다음과 같다.

- Read-intensive Workload에서 Database 부하 감소
- 자주 조회되는 데이터를 빠르게 반환
- User Session 저장
- Application을 Stateless하게 구성

---

# 1. Caching

Application이 같은 데이터를 반복해서 Database에서 조회하면 Database에 불필요한 Read 부하가 발생할 수 있다.

ElastiCache를 사용하면 자주 사용하는 데이터를 Cache에 저장하여 Database 조회를 줄일 수 있다.

```text
Application
     ↓
ElastiCache
     ↓
    RDS
```

Application은 Database를 조회하기 전에 Cache를 먼저 확인한다.

---

# 2. Cache Hit

요청한 데이터가 이미 Cache에 존재하는 경우를 **Cache Hit**라고 한다.

```text
Application
     ↓
ElastiCache
     ↓
Data 존재 ⭕

→ Cache Hit
→ Cache에서 바로 반환
→ Database 조회 불필요
```

Database까지 요청이 전달되지 않으므로 빠른 응답과 Database 부하 감소가 가능하다.

---

# 3. Cache Miss

요청한 데이터가 Cache에 존재하지 않는 경우를 **Cache Miss**라고 한다.

```text
Application
     ↓
ElastiCache
     ↓
Data 없음 ❌
     ↓
    RDS
     ↓
Data 조회
     ↓
Cache에 저장
     ↓
Application에 반환
```

이후 동일한 데이터가 다시 요청되면 Cache Hit가 발생할 수 있다.

```text
Cache 확인
   │
   ├─ Data 있음
   │      ↓
   │   Cache Hit
   │      ↓
   │   바로 반환
   │
   └─ Data 없음
          ↓
       Cache Miss
          ↓
       Database
          ↓
       Cache 저장
```

### 시험 핵심

`Read-intensive Workload + Reduce Database Load + Low Latency → ElastiCache`

---

# 4. Cache Invalidation

Database의 원본 데이터가 변경되어도 Cache에는 이전 데이터가 남아 있을 수 있다.

```text
Cache
Price = 10,000

RDS
Price = 8,000
```

따라서 Cache를 사용할 때는 오래된 데이터를 제거하거나 갱신하는 **Cache Invalidation Strategy**가 필요하다.

```text
Caching
→ Database Load 감소

하지만

Stale Data 방지
→ Cache Invalidation Strategy 필요
```

---

# 5. Session Store

ElastiCache는 User Session을 저장하는 용도로도 사용할 수 있다.

Session을 각각의 Application Instance 내부에 저장하면 사용자가 다른 Instance로 이동했을 때 Session 정보를 찾지 못할 수 있다.

```text
User
 ↓
EC2 A

Session
→ EC2 A에만 존재
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

따라서 사용자가 다른 Application Instance로 연결되어도 로그인 상태 등을 유지할 수 있다.

```text
User
 ↓
EC2 A
 ↓
Session → ElastiCache

다음 요청

User
 ↓
EC2 B
 ↓
Session ← ElastiCache
```

이를 통해 Application Instance 자체가 Session State를 가지고 있을 필요가 없어져 **Stateless Application**을 구성할 수 있다.

### 시험 핵심

`Shared Session + Stateless Application → ElastiCache`

---

# 6. Application Code Changes

ElastiCache는 단순히 활성화한다고 Application이 자동으로 Cache를 사용하는 것이 아니다.

Application이 직접 다음과 같은 Caching Logic을 구현해야 한다.

```text
1. Cache 조회

2. Cache Hit
   → Cache Data 사용

3. Cache Miss
   → Database 조회

4. 조회 결과
   → Cache에 저장
```

따라서 ElastiCache를 도입할 때는 **Application Code 변경이 필요할 수 있다.**

---

# 7. Redis

Redis는 강의에서 **Replication과 High Availability를 지원하는 Cache Engine**으로 설명한다.

```text
Redis Main
     │
     │ Replication
     ▼
Redis Replica
```

같은 데이터를 Replica에 복제할 수 있으므로 장애 대응과 Read Scaling에 활용할 수 있다.

### 주요 특징

- Multi-AZ
- Automatic Failover
- Read Replicas
- High Availability
- AOF Persistence
- Backup / Restore
- Sets / Sorted Sets

```text
Main 💥
 ↓
Replica
 ↓
Failover
```

## Sorted Sets

Redis의 Sorted Sets는 데이터를 Score에 따라 정렬할 수 있다.

따라서 Leaderboard와 같은 기능에 유용하다.

```text
Player      Score

Player A     9500
Player B     8200
Player C     7100
```

### 시험 핵심

```text
Replication
High Availability
Read Replicas
Persistence
Leaderboard / Sorted Sets

→ Redis
```

---

# 8. Memcached

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

강의에서 설명하는 전통적인 Memcached 구조에서는 Redis와 같은 Replication 구조가 없다.

따라서 특정 Node에 장애가 발생하면 해당 Node가 가지고 있던 Cache Data를 잃을 수 있다.

```text
Node A       Node B 💥       Node C
Data A       Data B ❌       Data C
```

하지만 Cache Data의 원본이 Database에 존재한다면 다시 조회하여 Cache를 채울 수 있다.

```text
Cache Data 유실
      ↓
Cache Miss
      ↓
Database 조회
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

강의에서는 Backup / Restore가 Memcached Serverless에서 지원된다고 설명한다.

### 시험 핵심

```text
Simple Distributed Cache
Sharding
Multi-threaded

→ Memcached
```

---

# 9. Redis vs Memcached

강의에서 사용하는 핵심적인 비교 이미지는 다음과 같다.

```text
Redis
→ 같은 데이터를 다른 Node에 Replication

Main
🍎🍌🍇
   │
   ▼
Replica
🍎🍌🍇


Memcached
→ 데이터를 여러 Node에 Sharding

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
| Automatic Failover | No HA |
| Read Replicas | No Replication |
| Persistence | Non-Persistent |
| Backup / Restore | Serverless에서 Backup / Restore |
| Sets / Sorted Sets | Multi-threaded |
| Leaderboard에 적합 | Simple Distributed Cache |

> 위 비교는 강의에서 시험 이해를 위해 단순화한 구조이다.

---

# 10. 시험 핵심 정리

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
→ Database 조회 X

Cache Miss
→ Cache에 Data 없음
→ Database 조회
→ 결과를 Cache에 저장

Cache 사용
→ Cache Invalidation Strategy 필요

Session Store
→ ElastiCache
→ Stateless Application

Redis
→ Replication
→ Multi-AZ / Auto Failover
→ Read Replicas
→ Persistence
→ Sorted Sets / Leaderboard

Memcached
→ Sharding
→ Multi-node
→ No Replication
→ Non-Persistent
→ Multi-threaded
```

---

# 日本語まとめ

## Amazon ElastiCache

Amazon ElastiCacheはRedisまたはMemcachedをManaged Serviceとして提供するIn-Memory Cacheサービスである。

Read-intensiveなWorkloadでDatabaseへのアクセスを減らし、低Latencyでデータを取得できる。

### Cache Hit / Cache Miss

```text
Cache Hit
→ CacheにDataが存在
→ Databaseへのアクセス不要

Cache Miss
→ CacheにDataが存在しない
→ Databaseから取得
→ Cacheに保存
```

Cacheを使用する場合、古いデータを防ぐためにCache Invalidation Strategyが必要になる。

### Session Store

User SessionをElastiCacheに保存することで、複数のApplication Instanceが同じSessionを利用できる。

これによりStateless Applicationを構成できる。

### Redis

- Replication
- Multi-AZ
- Automatic Failover
- Read Replicas
- Persistence
- Backup / Restore
- Sets / Sorted Sets

### Memcached

- Multi-node
- Sharding
- No Replication
- Non-Persistent
- Multi-threaded

---

# English Summary

## Amazon ElastiCache

Amazon ElastiCache provides managed Redis and Memcached as high-performance, low-latency in-memory caching services.

### Caching

A Cache Hit returns data directly from the cache without querying the database.

A Cache Miss requires the application to retrieve data from the database and optionally store it in the cache for future requests.

A Cache Invalidation Strategy is required to prevent stale data.

### Session Store

ElastiCache can store shared user sessions, allowing application instances to remain stateless.

### Redis

Redis supports replication, Multi-AZ, automatic failover, read replicas, persistence, backup/restore, and data structures such as Sorted Sets.

### Memcached

Memcached provides a simple distributed cache using multiple nodes and sharding. In the course comparison, it does not provide replication or persistence and uses a multi-threaded architecture.

---

# Vocabulary

| English | 日本語 | 한국어 |
|---|---|---|
| Cache | キャッシュ | 캐시 |
| In-Memory | インメモリ | 인메모리 |
| Cache Hit | キャッシュヒット | 캐시 히트 |
| Cache Miss | キャッシュミス | 캐시 미스 |
| Cache Invalidation | キャッシュ無効化 | 캐시 무효화 |
| Session Store | セッションストア | 세션 저장소 |
| Stateless | ステートレス | 무상태 |
| Replication | レプリケーション | 복제 |
| Sharding | シャーディング | 샤딩 / 데이터 분할 |
| Persistence | 永続化 | 영속성 |
| Automatic Failover | 自動フェイルオーバー | 자동 장애 조치 |
| Read Replica | リードレプリカ | 읽기 전용 복제본 |
| Sorted Set | ソート済みセット | 정렬된 집합 |
| Leaderboard | リーダーボード | 순위표 |

---

# Review Questions

1. ElastiCache를 사용하면 Read-intensive Workload에서 Database 부하를 줄일 수 있는 이유는 무엇인가?
2. Cache Hit와 Cache Miss의 차이는 무엇인가?
3. Cache Invalidation Strategy가 필요한 이유는 무엇인가?
4. User Session을 ElastiCache에 저장하면 Application을 Stateless하게 만들 수 있는 이유는 무엇인가?
5. Redis의 Replication이 High Availability에 도움이 되는 이유는 무엇인가?
6. 강의에서 Redis와 Memcached를 구분할 때 사용하는 `Replication`과 `Sharding`의 차이는 무엇인가?
7. Leaderboard를 구현해야 한다면 Redis의 어떤 기능을 사용할 수 있는가?
