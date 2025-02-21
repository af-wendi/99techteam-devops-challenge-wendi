Provide your solution here:
## Architecture Diagram
                    +-------------------+
                    |    Route 53       |
                    +-------------------+
                             |
                             v
                    +-------------------+
                    |  CloudFront (CDN) |
                    +-------------------+
                             |
                             v
                    +-------------------+
                    | Application Load  |
                    |    Balancer       |
                    +-------------------+
                             |
            +----------------+----------------+
            |                                 |
    +---------------+                 +---------------+
    | EC2 Auto-Scaling|               | EC2 Auto-Scaling|
    | (Trading API)   |               | (Web Frontend)  |
    +---------------+                 +---------------+
            |                                 |
            v                                 v
    +-------------------+           +-------------------+
    | Amazon RDS (Aurora)|          | Amazon ElastiCache|
    |  (Relational DB)   |          |   (Redis)         |
    +-------------------+           +-------------------+
            |
            v
    +-------------------+
    | Amazon S3 (Storage)|
    +-------------------+

    
## Key AWS Services and Their Roles

### 1. Route 53 (DNS Management)
- **Role**: Provides domain name resolution and traffic routing to the CloudFront distribution.
- **Why**: Ensures low-latency DNS resolution and supports failover routing for high availability.

### 2. CloudFront (Content Delivery Network)
- **Role**: Distributes static assets globally to reduce latency and improve response times.
- **Why**: Ensures low-latency delivery of static content and reduces the load on backend servers.

### 3. Application Load Balancer (ALB)
- **Role**: Distributes incoming traffic across multiple EC2 instances running the trading API and web frontend.
- **Why**: Provides automatic failover and ensures high availability.

### 4. EC2 Auto-Scaling Groups
- **Role**: Hosts the trading API and web frontend, scaling up or down based on traffic.
- **Why**: Ensures scalability and cost-effectiveness by dynamically adjusting the number of instances.

### 5. Amazon RDS (Aurora)
- **Role**: Stores relational data such as user accounts, orders, and transactions.
- **Why**: Aurora is highly available, supports read replicas for scaling, and provides low-latency access to relational data.

### 6. Amazon ElastiCache (Redis)
- **Role**: Caches frequently accessed data (e.g., order books, user sessions) to reduce database load and improve response times.
- **Why**: Redis is in-memory, ensuring sub-millisecond latency for caching.

### 7. Amazon S3
- **Role**: Stores static assets (e.g., logs, backups, and historical trading data).
- **Why**: S3 is cost-effective, durable, and highly available for object storage.

## Why These Services Were Chosen

1. **High Availability**: Route 53, ALB, and Auto-Scaling ensure no single point of failure.
2. **Scalability**: Auto-Scaling Groups dynamically adjust compute resources. (My expertise also in that, in k8s i just need to learn more)
3. **Cost-Effectiveness**: S3 provides low-cost storage for static assets.
4. **Performance**: ElastiCache ensures sub-millisecond latency for cached data.

## Scaling Plans

### 1. Scaling Compute Resources
- **Current Setup**: EC2 Auto-Scaling Groups handle up to 500 requests per second.
- **Future Growth**: Increase the instance types or number of instances in the Auto-Scaling Groups.

### 2. Scaling the Database
- **Current Setup**: Aurora with a primary instance and read replicas.
- **Future Growth**: Add more read replicas to handle increased read traffic.

### 3. Scaling the Cache
- **Current Setup**: ElastiCache (Redis) for caching order books and user sessions.
- **Future Growth**: Scale Redis clusters vertically or horizontally.

### 4. Scaling Static Content Delivery
- **Current Setup**: CloudFront for global content delivery.
- **Future Growth**: Enable additional edge locations for faster delivery in new regions.