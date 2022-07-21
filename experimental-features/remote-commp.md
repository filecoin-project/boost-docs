# Remote CommP

Boost is introducing a new feature that allows computing commP during the deal on a lotus worker node. This should alleviate any chokepoints in the market due to the commP calculation and reduce the overall resource utilisation on the market nodes.

To build the branch with remote commP feature please follow the below steps.

1\. Clone the main branch from the boost repo

```
git clone https://github.com/filecoin-project/boost.git
```

2\. Checkout the feature branch

&#x20;    For lotus v1.17.0-rc2 or lower version

```
git checkout feat/commp-sealer
```

&#x20;    For lotus v1.17.0-rc3 or higher version

```
git checkout feat/remote-commp
```

3\. Build the boost binaries

```
make boost
```

4\. Enable remote commP in the config

```
[Dealmaking]
   RemoteCommp = true
```

5\. Start the boost node
