# Requirements

Take note of the requirements already gathered in the
advanced executor issue here: https://github.com/LiberTEM/LiberTEM/issues/473
and see also the discussions in the linked issues.

From the GPU and Background/Service/...-Task support, we can see that we need
to spawn multiple worker processes on each node, tagged with "resource tags".
For the very first iteration, we may get away with not supporting this, as
it may require a messaging pattern that is a bit more complicated.

For live processing, shared memory, caching etc. we can derive the need for
some persistent state or persistent services running together with each worker
process.

## Data Topologies

Especially for live processing, it is clear that we need to not only support
the classical "request-response" pattern, but also a fan-out from the data
source, to distribute the data to more than one CPU core (or even multiple
nodes).

We may need to support a bit more complicated topologies. For example, if we do
need the full frames from the K2IS, we need to join the data streams at
a single computer, and possibly distribute them again to multiple processing
nodes.

Possibly, there should be support for building more general "data pipelines"
for data sources. Then, we can connect this data pipeline to a DataSet and
run our UDFs on it.

## Reliability / failure tolerance

Question: how to build this in a failure-tolerant way? For live acquisition,
I don't think we can have much failure tolerance - at least at the data sources.
If we have a fan-out to more processing nodes, we could tolerate some failure,
as long as the data rate can be processed fast enough.

For offline processing, we need to make sure that for every partition of the
data, there is always a worker node that "feels responsible" for processing it.
If a node fails, another node should keep working on the tasks and the whole
job should eventually finish. This is in conflict with node affinity, that is,
having a stable mapping of partitions onto nodes. While this can't be helped
completely, we can try to reduce the impact of non-strict affinity, for example
by having multiple levels of affinity instead.
