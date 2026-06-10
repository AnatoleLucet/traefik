---
title: "HTTP/2 Bomb"
description: "Learn how Traefik mitigates HTTP/2 Bomb attacks, and how to further limit memory expansion from HPACK decompression."
---


An HTTP/2 Bomb exploits HPACK header compression by sending a request that references many entries from the server's header table.
When decompressed, memory usage can expand dramatically, potentially exhausting resources and causing a Denial of Service.

By default, Traefik limits the size of incoming headers,
preventing attackers from sending requests with an excessive number of table references.

## Further mitigation

### Lower headers buffer size

One simple way to make it even harder for attackers to bomb Traefik,
is to lower [http.maxHeaderBytes](traefik/reference/install-configuration/entrypoints/#opt-http-maxHeaderBytes):

```yaml
http:
  maxHeaderBytes: 16 << 10 # 16 KiB
```

Lowering the headers buffer size will force attackers to make more requests with fewer references to cause any harm,
making it easier to detect and block suspicious traffic.


### Rate Limiting and DDoS mitigation

After [lowering the buffer size](#lower-headers-buffer-size), the suspicious clients will be more detectable.

Make sure you have a Rate Limiting and DDoS mitigation service in front of Traefik to filter-out
any client that might be making an unusual amount of requests.

### Limit the number of connections

Another mitigation that goes with [lowering the buffer size](#lower-headers-buffer-size), is to limit the number of
connections a single peer can keep alive with Traefik.



