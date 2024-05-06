# ELB vs. Route53 Routing

| Feature                 | ELB                                                           | Route53                             |
|-------------------------|---------------------------------------------------------------|-------------------------------------|
| Request distribution    | Equal. Next request goes to an entity with least connections. | Weighted.                           |
| Health checks           | YES                                                           | YES                                 |
| Unhealthy node eviction | Immediate                                                     | When client's TTL/cache expires     |
| Region                  | Balance traffic between multiple AZ in a **single region**    | Multiple regions                    |
| Routing method          | Route IP Traffic                                              | DNS resolution                      |
| Intended use            | Balance EC2 instances or containers, stateless connections    | RDS Instances, stateful connections |
