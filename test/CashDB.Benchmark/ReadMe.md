﻿# TCashDBBenchmark

This app is intended to perform stress-test over a locally instantiated
CashDB instance.

Usage:

    dotnet CashDB.Benchmark.dll init --layer1Path "/path/to/dir" --layer1 256
	dotnet CashDB.Benchmark.dll run --layer1Path "/path/to/dir"
	dotnet CashDB.Benchmark.dll rrun --ipAddress "127.0.0.1" --port 8338

The `init` command initializes a pre-allocated of storage for CashDB. The
size in gigabytes is passed through the `--layer1` argument.

The `run` command performs a local benchmark against the storage previously
allocated through `init`.

The `rrun` commmand performs a benchmark against a remote instance,
independently setup through `CashDB.Server run`.

## Stress test scenario

The benchmark generates a stream of coin events where each coin undergo
three events:

1. a production
2. a UTXO read
3. a consumption

The read operation is placed "close" to the consumption, but not next to
it. This behavior is intended to reflect the actual usage pattern for a
full Bitcoin participant that validates all incoming transactions.

Considering a "vanilla" Bitcoin transaction with 2 inputs and 2 outputs,
processing the transaction takes 6 IOs from the CashDB perspective.

The UTXO queries sent to CashDB are packed by batches - 512 IOs being the 
default, which represents about 85 transactions.
