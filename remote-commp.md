# Remote CommP

Boost is introducing a new feature that allows computing commP during the deal on a lotus worker node. This should alleviate any chokepoints in the market due to the commP calculation and reduce the overall resource utilisation on the market nodes.

To build `boostd` with remote commP feature please follow the below steps.

1\. Clone the main branch from the boost repo

```
git clone https://github.com/filecoin-project/boost.git
```

2\. Checkout the v1.4.0 or higher release

```
git checkout v1.4.0
```

3\. Build the boost binaries

```
make build
```

4\. Enable remote commP in the config

```
[Dealmaking]
   RemoteCommp = true
```

5\. Start the boost node
