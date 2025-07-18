
# 🔥 vProfile Re-Architected Deployment on AWS (Refactored Setup)

This guide walks through the **Refactored Cloud-Native Deployment** of the vProfile Java web application using AWS managed services like Elastic Beanstalk, RDS, Amazon MQ, ElastiCache, Route 53, and CloudFront.

---

## 🧠 Why Refactor?

### ❌ Before (Lift & Shift):
- Manually installed Tomcat, MySQL, RabbitMQ, Memcache on EC2
- Handled scaling, backups, monitoring manually

### ⚠️ Problems:
- Too much manual work
- Hard to scale & maintain
- Not cost-efficient

### ✅ After (Refactored Architecture):
- Elastic Beanstalk for Tomcat
- RDS for MySQL
- Amazon MQ for RabbitMQ
- ElastiCache for Memcache
- Route 53 for DNS
- CloudFront for CDN
- AWS handles scaling, backups, monitoring

---

## 🧱 AWS Services Used

| Purpose              | AWS Service           | Why Use It                                    |
|----------------------|-----------------------|-----------------------------------------------|
| App Hosting          | Elastic Beanstalk     | PaaS for Java apps without EC2 management     |
| Storage              | S3                    | Store WAR/config files                        |
| Load Balancing       | Auto via Beanstalk    | Distributes traffic                           |
| Auto Scaling         | Auto via Beanstalk    | Dynamic EC2 scaling                           |
| Database             | Amazon RDS (MySQL)    | Fully managed DB                              |
| Caching              | Amazon ElastiCache    | Managed Memcache                              |
| Messaging            | Amazon MQ             | Replaces RabbitMQ                             |
| Monitoring           | CloudWatch            | Log, metrics, auto-scaling triggers           |
| DNS                  | Route 53              | Domain & endpoint management                  |
| CDN                  | CloudFront            | Global low-latency access                     |

---

## 🚀 Architecture Flow

```plaintext
User
 ↓
Route 53 (DNS)
 ↓
CloudFront (CDN)
 ↓
Elastic Load Balancer (via Beanstalk)
 ↓
EC2 (Tomcat app via Beanstalk)
 ↓
↳ RDS (MySQL)
↳ Amazon MQ (RabbitMQ)
↳ ElastiCache (Memcached)
↳ S3 (WAR files)
```

---

## 🎯 Security & Key Pairs Setup

- Create **vprofile-rearch-backend-sg** for RDS, ElastiCache, MQ.
- Allow **internal-only traffic** using self-referencing inbound rules.
- Create a **key pair** (e.g., `vprofile-rearch-key`) for EC2 SSH access.

---

## 🗄️ RDS (MySQL) Setup

1. **Parameter Group**: `vprofile-rds-rearch-paragrp` (MySQL 8.0)
2. **Subnet Group**: `vprofile-rds-rearch-subgrp` (2+ AZs)
3. **Instance**: `vprofile-rds-rearch` (t3.micro, private, port 3306)
4. Save credentials securely
5. DB name = `accounts`

---

## ⚡ ElastiCache (Memcached)

1. **Parameter Group**: `vprofile-rearch-paragrp`
2. **Subnet Group**: `vprofile-rearch-subgrp`
3. **Cluster**: `vprofile-rearch-cache` (cache.t2.micro, port 11211)

---

## 📬 Amazon MQ (RabbitMQ)

1. **Instance**: `vprofile-rearch-rabbitmq` (mq.t3.micro)
2. **Engine**: RabbitMQ 3.x
3. **Access**: Private only (VPC)
4. **Port**: 5671
5. Save credentials securely

---

## 💽 DB Initialization (via EC2)

- Launch temp EC2 (Ubuntu) in same VPC
- Install: `mysql-client`, `git`
- Connect to RDS:
  ```bash
  mysql -h <rds-endpoint> -u admin -p accounts
  ```
- Clone & checkout code:
  ```bash
  git clone https://github.com/hkhcoder/vprofile-project.git
  git checkout awsrefactor
  ```
- Initialize DB:
  ```bash
  mysql -h <rds-endpoint> -u admin -p accounts < src/main/resources/db_backup.sql
  ```

---

## ✅ Elastic Beanstalk Setup

1. **IAM Role**: `vprofile-rearch-bean-role` with required Beanstalk permissions
2. **Platform**: Tomcat 10 + Java 17
3. **Config**:
   - Public EC2 with ALB
   - Tags: `project: vprofile`
   - GP3 volume type
4. **Auto Scaling**: Min 2, Max 4 (based on CPU/network)
5. **Deployment**: Rolling with 50%
6. **Environment URL**: https://<env>.elasticbeanstalk.com

---

## 🔐 Allow Beanstalk App Access to Backend

- Get Beanstalk EC2 SG ID
- Add inbound rule to `vprofile-rearch-backend-sg`:
  - Type: All Traffic
  - Source: Beanstalk SG ID

---

## 🚀 WAR Deployment

1. **Build**:
   ```bash
   mvn install
   ```
   → Output: `target/vprofile-v2.war`

2. **Upload to Beanstalk**:
   - Go to environment → Upload & Deploy WAR
   - Choose Rolling Deployment

3. **Test Login**:
   - Username: `admin_vp`
   - Password: `admin_vp`

---

## 🔒 HTTPS Setup with ACM

1. **Add HTTPS listener (443)** in Beanstalk Load Balancer config
2. **Attach ACM SSL certificate**
3. **(Optional)** Add CNAME in GoDaddy

---

## 🌍 CloudFront Setup

1. Create Distribution pointing to Beanstalk ALB
2. Use your domain as Alternate CNAME
3. Add CNAME in DNS Provider (e.g., GoDaddy)

**Test**: Open developer tools → Network tab → Check for `via: CloudFront` header

---

## 🏗️ Final Architecture Recap

- ✅ Cloud-native deployment using Elastic Beanstalk
- ✅ Managed RDS, ElastiCache, MQ
- ✅ Scalable, monitored, secure app with HTTPS & CDN
- ✅ Rolling deployment strategy (safe, zero downtime)
- ✅ Fast global access via CloudFront
