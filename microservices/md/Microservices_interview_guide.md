## **PART 1: CORE COMMUNICATION PATTERNS** 

## **Sync vs Async Communica�on** 

## **Core Idea** 

In microservices, services communicate either: 

- **Synchronous (Sync):** Immediate request-response 

- **Asynchronous (Async):** Fire-and-forget via events/messages 

|**� Comparison Table**|||
|---|---|---|
|Factor|Sync|Async|
|**Latency**|Low (immediate)|Higher (event delay)|
|**Coupling**|Tight|Loose|
|**Scalability**|Limited|High|
|**Consistency**|Strong|Eventual|
|**Complexity**|Simple|Complex|
|**Use when**|Immediate response needed|Processing can wait|
|**� Decision Tree**|||
|text|||



Step 1: Immediate response required? YES → Sync NO → Step 2 

Step 2: User-facing cri�cal path? YES → Sync NO → Step 3 

Step 3: Can processing be delayed? 

YES → Async 

NO → Sync 

Step 4: High load/scalability needs? 

YES → Async preferred 

NO → Sync acceptable 

Step 5: Strong consistency required? 

YES → Sync 

NO → Async 

## **� Mental Model** 

- **Sync** = Phone call (wait for reply) 

- **Async** = Message/postbox (no wai�ng) 

## **� Hybrid Example (Order Flow)** 

text 

┌─────────────┐     Sync      ┌─────────────┐ │   User      │ ─────────── ▶ │  Validate   │ │  Places     │   (wait)      │   Order     │ │   Order     │               └─────────────┘ └─────────────┘                      │ 

│ Async Event ▼ 

┌─────────────┐     Async      ┌─────────────┐ 

│ No�fica�on│ ◀ ───────────── │   Payment   │ │   Service   │   (event)      │   Service   │ └─────────────┘                 └─────────────┘ 

## **PART 2: DATA MANAGEMENT** 

## **Data Decomposi�on** 

## **Core Principle** 

**Database per service** — each microservice owns its own database. 

## **Why No Shared Database?** 

- Creates �ght coupling 

- Limits scalability 

- Prevents independent deployment 

## **Data Ownership Examples** 

Data Owned 

Service Data Owned Order order_id, items, order_status Customer customer_id, name, address Payment payment_id, order_id, payment_status Shipping shipment_id, tracking_status 

## **� Communica�on Without Shared DB** 

text 

┌──────────┐      API/Sync      ┌──────────┐ │ Service A│ ───────────────── ▶ │ Service B│ └──────────┘                     └──────────┘ │                                │ └────────── Event/Async ─────────┘ 

## **Key Challenge: Eventual Consistency** 

## **Solu�on Pa�erns:** 

- **Saga Pa�ern** for distributed transac�ons 

- **CQRS** for separa�ng read/write models 

- **Event Sourcing** for maintaining consistency 

## **� Mental Model** 

- **Monolith** = one shared brain 

- **Microservices** = mul�ple independent brains 

## **PART 3: RESILIENCE PATTERNS** 

## **The Resilience Stack (Order of Protec�on)** 

text 

Level 1: TIMEOUT ────── ▶ "Don't wait forever" 

│ 

▼ 

Level 2: RETRY ──────── ▶ "Try again if temporary" 

│ 

▼ 

Level 3: CIRCUIT BREAKER ── ▶ "Stop calling failing service" 

│ 

▼ 

Level 4: FALLBACK ────── ▶ "Provide alterna�ve response" 

## **1. Timeout** 

Aspect Detail **What** Limits max wait �me for a call **Rule** EVERY remote call needs �meout **Why** Prevents thread blocking, resource exhaus�on 

## **2. Retry Pa�ern** 

Aspect Detail **When** Network glitches, temporary overload, 5xx errors **When NOT** 4xx errors, non-idempotent opera�ons **Best Prac�ces** Exponen�al backoff + ji�er + limited a�empts **Retry Backoff Diagram** 

text 

A�empt 1: Wait 100ms ── ▶ Fail A�empt 2: Wait 200ms ── ▶ Fail A�empt 3: Wait 400ms ── ▶ Success ✓ 

## **3. Circuit Breaker** 

## **States Flow** 

text 

┌─────────────────────────────────────────┐ │                                         │ ▼                                         │ ┌──────────┐    Failures exceed    ┌──────────┐ │  CLOSED  │ ───────────────────── ▶ │   OPEN   │ │ (Normal) │     threshold         │ (Blocked)│ └──────────┘                        └──────────┘ ▲                                   │ │                                   │ Timeout │        Success                    │ passes │    ┌──────────────────────────────┘ │    ▼ │ ┌────────────┐ └─│ HALF-OPEN  │ │ (Tes�ng)  │ └────────────┘ 

## **4. Fallback Pa�ern** 

Type 

Example 

**Cached data** Serve stale cache when live API fails **Default response** "Service temporarily unavailable" **Secondary provider** Switch to backup service 

## **� Real-World Example (Payment Flow)** 

text 

┌─────────────────────────────────────────────────────────┐ 

│                    PAYMENT FLOW                         │ 

───────────────────────────────────────────────────────── `├ ┤` 

- │  1. Timeout: 2 seconds max wait                         │ 

- │  3. Circuit Breaker: Opens a�er 5 failures in 10 sec   │ 

- │  4. Fallback: Mark order as "payment pending"           │ 

└─────────────────────────────────────────────────────────┘ 

## **PART 4: RATE LIMITING** 

## **What It Does** 

Controls number of requests a client/user/service can make within a �me window. 

## **Algorithms Comparison** 

|Algorithm|Burst Allowed?|Complexity|Best For|
|---|---|---|---|
|**Token Bucket**|✓Yes|Medium|Most produc�on systems|
|**Fixed Window**|✗No|Simple|Basic thro�ling|
|**Sliding Window**|Limited|High|Accurate rate limi�ng|
|**Leaky Bucket**|✗No|Medium|Smooth output trafc|



## **Token Bucket Explained** 

text 

Tokens added at steady rate (10/sec) 

┌─────────────────────┐ 

- │   Token Bucket      │ 

- │   Capacity: 50      │ 

- │ **����������** │ 

└─────────────────────┘ 

Each request consumes 1 token 

↓ 

Burst: 50 requests can come instantly! 

## **HTTP Status Code** 

**429 Too Many Requests** → Rate limit exceeded 

## **� Mental Model** 

## **PART 5: SERVICE DISCOVERY** 

## 

## **How It Works** 

text 

**==> picture [279 x 388] intentionally omitted <==**

**----- Start of picture text -----**<br>
┌─────────────┐    1. Register      ┌─────────────────┐<br>│  Service B  │ ────────────────── ▶  │ Service Registry│<br>│  (Instance) │   (IP, Port, Health)│    (Eureka)     │<br>└─────────────┘                      └─────────────────┘<br>                                            │<br>                                            │ 2. Lookup<br>▼<br>┌─────────────┐    3. Return healthy ┌─────────────────┐<br>│  Service A  │  ◀ ───────────────────── │   Registry      │<br>│  (Caller)   │      instances        │                 │<br>└─────────────┘                        └─────────────────┘<br>      │<br>      │ 4. Direct call<br>▼<br>┌─────────────┐<br>│  Service B  │<br>│  Instance   │<br>└─────────────┘<br>**----- End of picture text -----**<br>


## **Types** 

Type Who Decides Rou�ng **Client-Side** Client **Server-Side** Load Balancer 

Example Tools Eureka + Ribbon 

Kubernetes, AWS ALB 

## **� Mental Model** 

- Service Discovery = "Google Maps for microservices" 

- Registry = Directory of all services 

- 

## **PART 6: SAGA PATTERN** 

## 

Manages distributed transac�ons across mul�ple microservices without using a single ACID transac�on. 

## **Why Needed** 

- No shared database 

- No global ACID transac�on support 

- Failures are common 

## **Types of Saga** 

## **1. Choreography (Event-Driven)** 

text 

Order Created ──event── ▶ Payment Service ──event── ▶ Inventory ──event── ▶ Shipping 

- │                        │                    │ 

`┴` ──────────────────────── `┴` 

No central controller 

## **2. Orchestra�on (Central Controller)** 

text 

┌─────────────────────┐ 

│  Saga Orchestrator   │ │   (Central Brain)    │ 

└─────────────────────┘ │      │      │ ▼ ▼ ▼ ┌────────┐ ┌────────┐ ┌────────┐ │ Order  │ │Payment │ │Inventory│ │Service │ │Service │ │Service │ └────────┘ └────────┘ └────────┘ 

## **Saga vs ACID** 

Feature ACID Transac�on Saga Pa�ern Scope Single DB Mul�ple services Consistency Strong Eventual Locking Yes No Scalability Limited High Failure handling Rollback Compensa�ng ac�ons 

## **Compensa�ng Transac�ons Example** 

text 

Forward Ac�ons:          Compensa�ng Ac�ons: 

1. Create Order     →     Cancel Order 

2. Deduct Payment   →     Refund Payment 

3. Reserve Stock    →     Release Stock 

4. Create Shipment  →     Cancel Shipment 

## **� Mental Model** 

- **ACID** = Single chef in one kitchen 

- **Saga** 

## **PART 7: CQRS & DATA DUPLICATION** 

## 

**CQRS** = Command Query Responsibility Segrega�on 

## **Core Architecture** 

text 

**==> picture [386 x 607] intentionally omitted <==**

**----- Start of picture text -----**<br>
                    ┌─────────────────────────────────────┐<br>                    │            USER REQUEST              │<br>                    └─────────────── ┬ ─────────────────────┘<br>                                    │<br>┴<br>            ┌─────────────────────── ───────────────────────┐<br>            │                                               │<br>▼ ▼<br>    ┌───────────────┐                               ┌───────────────┐<br>    │   COMMAND     │                               │    QUERY      │<br>    │   (Write)     │                               │   (Read)      │<br>    └─────── ┬ ───────┘                               └─────── ┬ ───────┘<br>            │                                               │<br>▼ ▼<br>    ┌───────────────┐      Event        ┌───────────────────────┐<br>    │  Write Model  │ ─────────────── ▶   │    Read Model         │<br>    │  (Source of   │    (Ka�a)        │   (Projec�on DB)     │<br>    │   Truth DB)   │                   │   (Denormalized)      │<br>    └───────────────┘                   └───────────────────────┘<br>Inten�onal Data Duplica�on<br>Write Model Read Model<br>Normalized data Denormalized data<br>Transac�onal focus Query-op�mized focus<br>Single source of truth Mul�ple projec�ons possible<br>Example: Order Data<br>Write Model (Normalized):<br>**----- End of picture text -----**<br>


## sql 

orders: order_id, customer_id, status 

order_items: order_id, product_id, quan�ty 

## **Read Model (Denormalized):** 

sql 

order_view: order_id, customer_name, product_names, order_status, total_amount 

## **� Mental Model** 

- 

- Read Model = Op�mized display copy 

- _Users never query the ledger directly_ 

## **PART 8: IDEMPOTENCY** 

## 

Execu�ng the same opera�on mul�ple �mes produces the same result as execu�ng it once. 

## **Implementa�on Strategies** 

text 

┌─────────────────────────────────────────────────────────────────┐ 

│                    IDEMPOTENCY IMPLEMENTATIONS                   │ 

───────────────────────────────────────────────────────────────── `├ ┤` 

│                                                                  │ 

- │  1. Idempotency Key                                             │ 

- │     Client: POST /orders + Header: Idempotency-Key: abc123      │ 

- │     Server: Store processed keys in Redis/DB                    │ 

│                                                                  │ 

│  2. Database Constraints                                        │ │     UNIQUE constraint on order_id, payment_id                   │ │                                                                  │ │  3. State-based Updates                                         │ │     ✓ SET balance = 1000    (idempotent)                        │ 

│     ✗ ADD 100 to balance    (NOT idempotent)                    │ │                                                                  │ 

- │  4. Message Deduplica�on                                       │ 

- │     Ka�a consumer tracks message IDs in deduplica�on store    │ 

- │                                                                  │ 

└─────────────────────────────────────────────────────────────────┘ 

## **Where Cri�cal** 

- Payment systems 

- Order crea�on 

- Inventory updates 

- Event processing systems 

## **� Mental Model** 

- **Idempotent** = Light switch (ON → ON → ON = same state) 

- **Non-idempotent** = Adding coins (each ac�on changes state) 

## **PART 9: OBSERVABILITY (3 Pillars)** 

**==> picture [380 x 373] intentionally omitted <==**

**----- Start of picture text -----**<br>
text<br>┌─────────────────────────────────────────────────────────────────┐<br>│                    THREE PILLARS OF OBSERVABILITY                │<br>─────────────────────────────────────────────────────────────────<br>├ ┤<br>│                                                                  │<br>│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐              │<br>│  │    LOGS     │  │   METRICS   │  │   TRACES    │              │<br>───────────── ───────────── ─────────────<br>│   ├ ┤ ├ ┤ ├ ┤               │<br>│  │ "What       │  │ "How many/  │  │ "End-to-end │              │<br>│  │  happened?" │  │  how fast?" │  │  journey"   │              │<br>───────────── ───────────── ─────────────<br>│   ├ ┤ ├ ┤ ├ ┤               │<br>│  │ Events      │  │ Numeric     │  │ Request ID  │              │<br>│  │ Timestamps  │  │ �me-series │  │ Span context│              │<br>───────────── ───────────── ─────────────<br>│   ├ ┤ ├ ┤ ├ ┤               │<br>│  │ ELK Stack   │  │ Prometheus  │  │ OpenTelemetry│             │<br>│  │            │  │ Grafana     │  │ Jaeger      │              │<br>│  └─────────────┘  └─────────────┘  └─────────────┘              │<br>**----- End of picture text -----**<br>


│                                                                  │ └─────────────────────────────────────────────────────────────────┘ 

## **How They Work Together** 

text Trace ID: abc-123 ─────────────────────────────────────────────────┐ │ ┌──────────────┐    ┌──────────────┐    ┌──────────────┐       │ │ API Gateway  │─── ▶ │ Order Service│─── ▶ │Payment Service│       │ │ Latency: 5ms │    │ Latency: 50ms│    │ Latency: 200ms│       │ │ Log: "200 OK"│    │ Log: "DB query"  │ Log: "�meout" │       │ └──────────────┘    └──────────────┘    └──────────────┘       │ │ 

Metrics: OrderService.error_rate = 15% at 20:30:05             │ 

└─────────────────────────────────────────────────────────────────┘ 

## **� Mental Model** 

- **Logs** = Diary entries 

- **Metrics** = Heartbeat monitor 

- **Traces** = GPS route map 

## **PART 10: CACHING STRATEGIES** 

## **Cache Aside (Lazy Loading) — MOST COMMON** 

text 

Request ── ▶ Cache ──Miss── ▶ Database ── ▶ Update Cache ── ▶ Response 

│ 

└──Hit── ▶ Response (fast path) 

## **Write-Through** 

text 

Write ── ▶ Cache ──Sync write── ▶ Database ── ▶ Response 

## **Write-Back (Write-Behind)** 

text 

Write ── ▶ Cache ── ▶ Response (immediate) 

│ 

└──Async── ▶ Database (eventual) 

## **Cache Invalida�on Strategies** 

Strategy How It Works Best For **TTL** Data expires a�er �me Product catalogs **Event-based** Update cache on data change User profiles **Manual** Explicit invalida�on API Admin-triggered updates 

## **Cache Stampede Problem & Solu�ons** 

text 

Problem: Cache expires → 1000 requests hit DB simultaneously 

Solu�ons: 

1. Locking (only 1 request fetches) 

3. Random TTL ji�er (stagger expira�on �mes) 

## **� Mental Model** 

- **Cache** = Fast memory desk 

- **Database** = Slow library 

- _You keep notes on your desk instead of going to the library every �me_ 

## **PART 11: DEPLOYMENT MODELS** 

## **Quick Comparison Matrix** 

|Model|Down�me|Risk|Cost|Complexity|Use Case|
|---|---|---|---|---|---|
|**Monolithic**|High|High|Low|Low|Legacy systems|
|**Rolling**|None|Medium|Medium|Medium|Standard<br>deployments|



|Model|Down�me|Risk|Cost|Complexity|Use Case|
|---|---|---|---|---|---|
|**Blue-Green**|None|Low|High|Medium|Cri�cal apps|
|**Canary**|None|Very<br>Low|Medium|High|Large-scale systems|
|**Ac�ve-**<br>**Passive**|Minimal|Low|Medium|Low|DR systems|
|**Ac�ve-Ac�ve**|None|Low|High|Very High|Global high-scale|
||||||apps|



## **Visual Summary** 

text 

Rolling:     [▓▓▓▓] → [▓▓▓▒] → [▓▓▒▒] → [▓▒▒▒] → [▒▒▒▒] (Gradual instance replacement) 

Blue-Green:  [████] BLUE     [████] GREEN \              / └──Switch────┘ (Instant traffic cutover) 

Canary:      [████████████]     [▒▒] 5% traffic 

Main version        New version 

Ac�ve-Ac�ve: **�** US ┐ 

`├` ── All ac�ve, load balanced 

**�** EU ┘ 

## **� ULTRA CHEAT SHEET (1-Page Revision)** 

## **CORE PATTERNS MAP** 

text 

┌─────────────────────────────────────────────────────────────────────┐ │                    MICROSERVICES PATTERN MAP                         │ ───────────────────────────────────────────────────────────────────── `├ ┤` │                                                                      │ │  Data Ownership ── ▶ Data Decomposi�on ── ▶ Database per Service     │ │                                                                      │ │  Traffic Control ── ▶ Rate Limiter ── ▶ Token Bucket                  │ │                                                                      │ │  Service Lookup ── ▶ Service Discovery ── ▶ Eureka/Kubernetes         │ │                                                                      │ │  Communica�on ── ▶ Sync (REST/gRPC) + Async (Ka�a/RabbitMQ)        │ │                                                                      │ │  Consistency ── ▶ Saga Pa�ern (Choreography/Orchestra�on)          │ │                                                                      │ │  Scalability ── ▶ Async + Event-Driven Architecture                  │ │                                                                      │ │  Resilience ── ▶ Timeout → Retry → Circuit Breaker → Fallback        │ │                                                                      │ │  Performance ── ▶ CQRS + Caching (Cache Aside)                       │ │                                                                      │ │  Safety ── ▶ Idempotency (Idempotency Key + Unique Constraints)      │ │                                                                      │ │  Visibility ── ▶ Observability (Logs + Metrics + Traces)             │ │                                                                      │ │  Deployment ── ▶ Blue-Green / Canary / Ac�ve-Ac�ve                 │ │                                                                      │ └─────────────────────────────────────────────────────────────────────┘ 

## **QUICK REFERENCE CARD** 

|Pa�ern|One-Line Summary|
|---|---|
|**Sync**|Immediate response, �ght coupling, user-facing ops|
|**Async**|Event-driven, loose coupling, scalable processing|
|**Saga**|Distributed transac�ons via compensa�ng ac�ons|
|**CQRS**|Separate read/write models with inten�onal duplica�on|
|**Circuit Breaker**|Stop calling failing services, prevent cascading failures|
|**Retry**|Handle transient failures with exponen�al backof|
|**Timeout**|Never wait indefnitely for any remote call|
|**Idempotency**|Same opera�on mul�ple �mes = same result|
|**Rate Limiter**|Control request fow, return 429 when exceeded|



## **MENTAL MODEL CHEAT SHEET** 

text 

API Gateway      = Front door of the system Rate Limiter     = Security guard at entrance Service Registry = Phone directory of services Ka�a            = Postal system (async messages) Circuit Breaker  = Emergency electrical switch Saga             = Workflow conductor/orchestrator Cache            = S�cky notes on your desk Trace            = GPS tracker of a request 

Idempotency Key  = Unique receipt ID for each opera�on 

## **INTERVIEW POWER PHRASES** 

_"Microservices are independently deployable services, each owning its own data model aligned to business domains."_ 

_and decoupling."_ 

_"Saga replaces ACID with eventual consistency using compensa�ng transac�ons."_ 

_"Idempotency is essen�al for safe retries in distributed systems."_ 

This document now serves as a complete interview prepara�on guide with: 

- **�** Clean forma�ng with emojis and visual hierarchy 

- **�** Text-based diagrams for easy recall 

- **�** Comparison tables for quick reference 

- **�** Mental models for intui�ve understanding 

- **�** One-line summaries for interview delivery 

