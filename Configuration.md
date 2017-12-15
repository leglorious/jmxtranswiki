JmxTrans allows flexibly configure behavior. There are configure properties description.

### Configure execution strategy

JmxTrans implements two kind of executors
* **query** — for metrics import (your applications)
* **result** — for metrics export (e.g. Graphite)

Now JmxTrans supports two excution strategies:
* Common (deprecated, only for backward compatibility) — only one thread pool for all server nodes which metrics you want to import
* Separate — each server node has it own thread pool

So if you use Common strategy and one of your server node is down you probably will lose metrics from another nodes for some time because thread pool filled by dead connections which awaiting for socket/connection timeout.

Common strategy is default now. For use Separate strategy use `--use-separate-executors` application parameter

### Configure executors

JmxTrans provides two application parameters for each executor:
* `--query-processor-executor-pool-size` / `--result-processor-executor-pool-size` (default = 10)
* `--query-processor-executor-work-queue-capacity` / `--result-processor-executor-work-queue-capacity` (default = 100000)
