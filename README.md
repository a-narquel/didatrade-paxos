# DidaTrade - Paxos-replicated trading service

A fault-tolerant stock-trading service where every operation is agreed on by a group of replicas using **Paxos**, so all replicas stay consistent even when some crash, freeze or get slow.

Built in **Java + gRPC** for the *Design and Implementation of Distributed Applications* course at IST (2026/27). **Grade: 19/20.**

## What we implemented

- **Multi-Paxos**: a stable leader skips Phase 1 and decides many log slots in parallel.
- **Fast Paxos**: replicas accept client requests directly and decide in one round trip. Collisions are recovered by the next leader.
- **Vertical Paxos**: the set of replicas can change at runtime, with state transfer to the new configuration.
- **Leader recovery**: a new leader re-proposes accepted values and fills gaps with no-ops.
- **Causal reads**: a client's balance read waits until the replica has seen that client's latest writes.
- **Fault injection**: replicas can be crashed, frozen or slowed down at runtime to test the protocol.

## Structure

| Module | Role |
|---|---|
| `server` | Replica: Paxos leader, acceptor, learner, log and state machine |
| `app` | Client issuing buy / sell / balance operations |
| `console` | Admin tool: change leader/ballot, reconfigure, inject faults |
| `contract` | gRPC / Protobuf definitions |
| `configs` | Replica configurations (quorums, leaders, fast ballots) |

## Run

Requires Java 22, Maven and protoc. Copy `contract/intel-pom.xml` (or `arm-pom.xml` on Apple Silicon) to `contract/pom.xml`, then:

```bash
mvn clean install

cd server  && mvn exec:java -Dexec.args="8000 0 A"   # start replicas 0, 1, 2
cd app     && mvn exec:java -Dexec.args="1 localhost 8000 A"
cd console && mvn exec:java -Dexec.args="localhost 8000 A"
```

## Authors

André Narquel, Tomás Silva and Paulo do Espírito Santo. Project skeleton by the course staff.
