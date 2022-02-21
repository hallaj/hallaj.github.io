---
title: Yet another Greenplum Interconnect error
date: 2014-06-26
tags:
  - greenplum
---

I've faced yet another Greenplum Interconnect error with the following error:

```shell
"ERROR","58M01","Interconnect Error: Could not connect to seqserver.  (seg0 slice1 gpslave1:40000 pid=14231)","Connection refused (connect errno 111)"
```

After doing tons of checks on the `gpslave1` server itself, nothing was found to be problematic. Ping shows reply, telnet to the ports shows active and no errors could be found.

Finally, on the master itself, I tried to ping the configured host of the master and it returned `127.0.0.1`. That's simply according to the `/etc/hosts` file.

```shell
127.0.0.1 localhost gpmaster
192.168.0.10 gpslave1
192.168.0.11 gpslave2
192.168.0.12 gpslave3
```

One more thing that's left to be done; I changed the host `gpmaster` from `127.0.0.1` to the actual LAN IP and it works!

So, a small piece of note, all the nodes (including the master, should resolve to the IP the other nodes needs to access from. Simply assuming `gpmaster` would only need to listen to itself doesn't work.
