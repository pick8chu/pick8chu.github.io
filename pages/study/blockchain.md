---
title: blockchain and crypto
last_updated: Feb 14, 2026
keywords: study
sidebar: mydoc_sidebar
comments: true
permalink: blockchain.html

---

## How Blockchain Reaches Agreement (Big Picture)

At its core, a blockchain is a system where many independent computers maintain a shared history of transactions. There is no central authority. So the key question is:

How do all these computers agree on one version of history?

⸻

### 1. Transactions Spread First

When someone sends cryptocurrency, the transaction is broadcast across the network.

Other computers verify basic rules:
- Is the signature correct?
- Does the sender actually own the coin?
- it already been spent?

If it passes these checks, it is considered valid — but not yet final.

At this stage, it is simply a candidate to be included in the official history.

⸻

### 2. Mining Adds Weight to History

Miners collect recent valid transactions and group them into a block.

To add that block to the chain, they must solve a computational puzzle. Solving it requires real processing power and electricity. This is called proof-of-work.

Every solved block adds measurable computational weight to the chain it extends.

This is the key idea:

The chain with the most total computational work behind it is considered the valid history.

⸻

### 3. What Happens When There Is a Conflict?

Suppose Node 0 tries to spend the same coin twice:
- Once to Node 1
- Once to Node 2

Now two possible histories exist:
```
- Chain A: 0 → 2 → 5 → 10
- Chain B: 0 → 1 → 4 → 8 → 9 → 10
```
At first, Node 10 may only see Chain A, so it treats that as the current history.

Later, Chain B arrives.

If Chain B has more total computational work behind it, Node 10 switches to Chain B — even if Chain A arrived earlier.

Nothing special is announced.
Node 10 simply follows the rule:

Always follow the chain with the most accumulated work.

Every other node follows the same rule independently.

Because everyone uses the same rule, the network naturally converges on one shared history.

⸻

### 4. What Mining Really Does

Mining does not compare chains to decide which is true.

Instead, mining:
- Extends a chain by adding a new block
- Adds computational weight to that chain

Over time, one chain accumulates more work than any competing chain. That chain becomes the accepted version of events.

⸻

### 5. Why This Works

The system does not require everyone to agree at the same moment.

Temporary disagreements can exist. Different nodes may briefly see different versions.

But as more blocks are added, the chain with greater total work pulls ahead. Eventually, almost all nodes adopt it. Thus mining is essential to make recent transaction more trueful.

**Finality is therefore not absolute — it becomes stronger as more blocks are built on top.**

⸻

In summary:
1. Transactions spread across the network.
2. Miners package them into blocks.
3. Blocks require real computational effort.
4. Nodes always follow the history with the most total work.
5. Over time, the network converges on a single version of events.

Consensus emerges not from voting, but from accumulated computational effort.