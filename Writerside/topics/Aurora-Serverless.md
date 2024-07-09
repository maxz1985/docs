# Aurora Serverless 

## Question

A company wants to free its teams from time-consuming database tasks like server provisioning, patching, backups, and so on,
and hence decided to migrate their on-premises **MariaDB** database to AWS.

The company wants its AWS database to have built-in security, continuous backups, **serverless compute**, 
multiple read replicas, **automated multi-Region replication**, **less expensive** compared to other commercial-grade databases, 
and integrations with other AWS services.

The company is asking for your advice and help as a solution architect to migrate databases to AWS quickly and securely.

They have conditions that the source database remains fully operational during the migration and downtime should be
minimized to applications that rely on the database. 
Which combination of architectural changes should you make to meet the above requirements?

## Suggested Answers

|   |                                                                                                                                                                                                                                                                                            |
|---|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| A | Provision Amazon Aurora Mysql as the target DB instance in AWS. <br/>Provision Database Migration Service (DMS) replication instance and create DMS endpoints.<br/> Create DMS task, migrate data from the source database as on-premises MariaDB, and perform validation.                 |
| B | Provision DynamoDB as target DB instance in AWS.  <br/> Provision Database Migration Service (DMS) replication instance and create DMS endpoints.  <br/> Create DMS task, migrate data from the source database as on-premises MariaDB, and perform validation.                            |
| C | Provision Amazon Aurora Serverless MySQL as target DB instance in AWS.  <br/> Provision Database Migration Service (DMS) replication instance and create DMS endpoints.  <br/> Create DMS task, migrate data from source database as on-premises MariaDB and perform validation.           |
| D | Provision Amazon Aurora Serverless PostgreSQL as target DB instance in AWS.  <br/>• Provision Database Migration Service (DMS) replication instance and create DMS  endpoints.  <br/>Create DMS task, migrate data from the source database as on-premises MariaDB and perform validation. |

## Working through the problem

The following sentences:
* Provision Database Migration Service (DMS) replication instance and create DMS endpoints.
* Create a DMS task, migrate data from the source database as on-premises MariaDB and perform validation.

Are the same in all four answers, so let us focus on the database choice.

|   |                                     |
|---|-------------------------------------|
| A | Amazon Aurora Mysql                 |
| B | DynamoDB                            |
| C | Amazon Aurora Serverless MySQL      |
| D | Amazon Aurora Serverless PostgreSQL |

B – Discard. DynamoDB is a NoSQL database. We're migrating MariaDB, which is a fork of MySQL.

D – Discard. MariaDB is not fully compatible with PostgreSQL  

We're left with the following choices:

|   |                                |
|---|--------------------------------|
| A | Amazon Aurora Mysql            |
| C | Amazon Aurora Serverless MySQL |

When creating a database in AWS RDS Console, we're presented with the following:

![aurora-serverless-01.png](aurora-serverless-01.png)

> Note that among the suggested answers, there are no `MariaDB` or `MySQL` choices.
> `MariaDB` and `MySQL` are _RDS proper_ and don't satisfy two of the presented requirements: 
> * Only `Aurora` has the "**automated multi-Region replication**"
> * `MariaDB` and `MySQL` are not _serverless_
> 
{style="note"}

What are `Aurora MySQL` and `Aurora Serverless MySQL` in A and C? 

On the RDS Screen, there is only a single choice – `Aurora (MySQL Compatible)` 

Proceed to create an Aurora MySQL Database, to see the following:

![aurora-serverless-02.png](aurora-serverless-02.png)

> Note that `Memory Optimized` and `Burstable` choices require you to pick an **instance**.
> 
{style="note"}

Therefore:
* `Aurora MySQL` is what is meant by `Memory Optimized` or `Burstable` choices. They have instance type and aren't serverless.
* `Aurora Serverless MySQL` is what is meant by `Serverless v2` choice

The difference is COST.

For steady workloads, EC2-based Aurora is more cost-effective while for up/down seesaw workloads serverless is cheaper.

The correct answer is C- "Amazon Aurora Serverless MySQL"