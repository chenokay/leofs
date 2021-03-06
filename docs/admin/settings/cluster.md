# Cluster Settings

This document outlines the various configuration items to keep in mind when planning your LeoFS cluster, and this document leads you to be able to correctly configure a LeoFS cluster when planning and launching it.


## Prior Knowledge

LeoFS adopts [eventual consistency](https://en.wikipedia.org/wiki/Eventual_consistency) of the consistency model, it takes priority over AP*(Availability and Partition tolerance)* over C*(consistency)* which depends on [CAP theorem](https://en.wikipedia.org/wiki/CAP_theorem).

To keep consistency of objects eventually, LeoFS delivers the automated replication and recovery to automatically fix consistency of objects. You can configure the consistency level of your LeoFS system, and the LeoFS system is affected by the configuration.


## Consistency Level

The consistency level of your LeoFS storage system is configured at <a href="https://github.com/leo-project/leofs/blob/master/apps/leo_manager/priv/leo_manager_0.conf" target="_blank">LeoManager's configuration file - leo_manager_0.conf</a>. You need to carefully configure the consisteny level because it is not able to change some items after starting the system.


There're four configuration items at <a href="https://github.com/leo-project/leofs/blob/master/apps/leo_manager/priv/leo_manager_0.conf" target="_blank">leo_manager_0.conf</a>, items of which have a great impact on **data availability** and **storage performance**.

| Item                              | Abbr | Modifiable | Default | Description |
|-----------------------------------|:----:|------------|---------|---|
| `consistency.num_of_replicas`     | n    | No         | 1       | A number of replicas |
| `consistency.write`               | w    | Yes        | 1       | A number of replicas needed for a successful WRITE operation  |
| `consistency.read`                | r    | Yes        | 1       | A number of replicas needed for a successful READ operation   |
| `consistency.delete`              | d    | Yes        | 1       | A number of replicas needed for a successful DELETE operation |
| `consistency.rack_aware_replicas` |      | No         | 0       | A number of rack-aware replicas |


### Data Availability of Consistency Level

This document delivers the relationship of `data availability` and `configuration level` as below:

| Data Availability | Configuration Level                  | Description |
|-------------------|--------------------------------------|-------------|
| Extremely Low     | n = 2<br/>r = 1<br/>w = 1<br/>d = 1  | Data can not be acquired even if two nodes goes down *(for personal use)*|
| Low               | n = 3<br/>r = 1<br/>w = 1<br/>d = 1  | Low data consistency|
| Middle(1)         | n = 3<br/>r = 1<br/>w = 2<br/>d = 2  | Typical settings |
| Middle(2)         | n = 3<br/>r = 2<br/>w = 2<br/>d = 2  | High data consistency than `Middle(1)` |
| High              | n = 3<br/>r = 2<br/>w = 3<br/>d = 3  | Data can not be input and removed even if one node goes down |
| Extremely High    | n = 3<br/>r = 3<br/>w = 3<br/>d = 3  | Data can not be acquired even if one node goes down *(can not be recommended)*|


### How To Change Consistency Level

You're able to change `consistency.write `, `consistency.read` and `consistency.delete` of the consistency level that you use the `leofs-adm update-consistency-level` command, but you cannot update `num_of_replicas` and `rack_aware_replicas`.

```bash
## Changes the consistency level to [w:2, d:2, r:1]
$ leofs-adm update-consistency-level 2 2 1

```

## Related Links

* [Settings / LeoManager Settings](leo_manager.md)