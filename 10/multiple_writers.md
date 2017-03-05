# Goal

LMDB allows a single writer, which mean concurrent writes are not possible. The
goal of this document is to come up with a way to have multiple writers operate
on a subset of the PumpkinDB database using multiple LMDB volumes.

# Backstory and Reasoning

As things stand, PumpkinDB is backed by a single LMDB volume. This approach is
understandably to optimize for the development of PumpkinScript and other
important features such as locking, support for multiple writers etc. We
anticipate a time when a single writer will be a bottleneck to the throughput
of a PumpkinDB installation, hence the importance of coming up with approaches
to support multiple writers.

# Architecture

Instead of a single LMDB volume, a PumpkinDB installation can be configured to
be backed by multiple volumes, and writes sharded between them using a consistent
hash function (where keys are consistently hashed to the same volume). This would
make it known where a key should be looked up and where a key should be written,
the two most common operations in PumpkinDB.