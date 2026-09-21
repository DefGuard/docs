# Performance and deployment sizing

### Hardware recommendations

For a production deployment expected to handle a workload similar to the tested environment, use the following baseline:

* Core: [`m7i.xlarge`](https://aws.amazon.com/ec2/instance-types/m7i/) (4 vCPUs, 16 GiB RAM);
* Edge/Proxy: [`m7i.xlarge`](https://aws.amazon.com/ec2/instance-types/m7i/) (4 vCPUs, 16 GiB RAM);
* Gateway: [`m7i.xlarge`](https://aws.amazon.com/ec2/instance-types/m7i/) (4 vCPUs, 16 GiB RAM);
* PostgreSQL: [`m7i.xlarge`](https://aws.amazon.com/ec2/instance-types/m7i/) (4 vCPUs, 16 GiB RAM);
* OpenLDAP: [`m7i.large`](https://aws.amazon.com/ec2/instance-types/m7i/) (2 vCPUs, 8 GiB RAM).

PostgreSQL and OpenLDAP should run on dedicated instances. Use [`r7i`](https://aws.amazon.com/ec2/instance-types/r7i/) for PostgreSQL when the database working set requires more memory. Use [`c7i`](https://aws.amazon.com/ec2/instance-types/c7i/) for Core or Edge/Proxy only when measurements show that CPU is the main constraint. [`m7i-flex`](https://aws.amazon.com/ec2/instance-types/m7i-flex/) can be considered for lower-average workloads with periodic bursts.

These recommendations are a starting point, not a fixed production limit. Larger deployments should scale Core and PostgreSQL independently and be validated with workload-specific tests.

### Load-testing results

The tested environment sustained approximately:

| Scenario                                |                   Result |
| --------------------------------------- | -----------------------: |
| VPN configuration polling               |           950 requests/s |
| MFA authorization                       |     600 authorizations/s |
| MFA authorization with posture checks   |     550 authorizations/s |
| LDAP import of 20,000 users             |  approximately 4 minutes |
| Create a network with 20,000 devices    |   approximately 1 minute |
| Readdress a network with 20,000 devices | approximately 45 seconds |

### Tested system functions

The tests covered the main operations used during normal system activity:

* periodic retrieval of VPN configuration by connected clients
* MFA authorization with and without posture checks
* creating and readdressing large networks
* importing users from LDAP

The tests measured throughput, execution time, errors, and resource usage under sustained load.

### Test infrastructure and hardware

The load-test environment used dedicated AWS EC2 instances with Ubuntu and private VPC networking. The tested deployment used the following instance types:

* Core: `m7i.xlarge` (4 vCPUs, 16 GiB RAM);
* Edge/Proxy: `m7i.xlarge` (4 vCPUs, 16 GiB RAM);
* PostgreSQL: `m7i.xlarge` (4 vCPUs, 16 GiB RAM, 100 GiB storage);
* Load generator: `m7i.xlarge` (4 vCPUs, 16 GiB RAM);
* OpenLDAP: 2 vCPUs, 2 GiB RAM;
* Prometheus/Grafana: `m7i.large` (2 vCPUs, 8 GiB RAM).

The `m7i` instances use AWS-custom 4th Generation Intel Xeon Scalable processors (Sapphire Rapids). On x86 instances, one vCPU represents one hardware thread, so `m7i.xlarge` provides 4 vCPUs backed by 2 physical cores and 16 GiB RAM.

### Methodology

Tests used seeded, production-like data and controlled request rates. Load was increased in steps while tracking throughput, latency, errors, CPU, memory, database usage, and active connections. Each scenario was run for a fixed period with a bounded number of concurrent requests. Results were compared across repeated runs while changing one infrastructure or configuration parameter at a time.

### Database pool size configuration and scaling

Tune the stack gradually and monitor database wait time, CPU, memory, disk I/O, and request latency after each change.

#### Database pool size

Core uses `DEFGUARD_DB_POOL_SIZE` to set the maximum number of PostgreSQL connections per Core process. The default is 10. Increase it when requests are waiting for a database connection while PostgreSQL still has available CPU and connection capacity.

Set PostgreSQL `max_connections` above that total, with room for administration and monitoring. Increasing the pool without increasing PostgreSQL capacity can make performance worse. More connections do not automatically mean more throughput.

#### Other scaling options

* Scale Core and PostgreSQL independently. PostgreSQL should have sufficient RAM and fast storage for the expected session and statistics workload.
* Use multiple Edge instances when one instance becomes a bottleneck. Keep request concurrency bounded and avoid removing backpressure limits without measurement.
* For wireguard-specific tuning options consult this guide: [https://docs.defguard.net/deployment-strategies/linux-kernel-wireguard-tuning](https://docs.defguard.net/deployment-strategies/linux-kernel-wireguard-tuning)
