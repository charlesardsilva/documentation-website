---
layout: default
title: Troubleshoot
nav_order: 62
has_children: true
has_toc: false
---

# Troubleshoot

This section contains a list of issues and workarounds.


## Java error during startup

You might see `[ERROR][c.a.o.s.s.t.OpenSearchSecuritySSLNettyTransport] [opensearch-node1] SSL Problem Insufficient buffer remaining for AEAD cipher fragment (2). Needs to be more than tag size (16)` when starting OpenSearch. This problem is a [known issue with Java](https://bugs.openjdk.java.net/browse/JDK-8221218) and doesn't affect the operation of the cluster.


## OpenSearch Dashboards fails to start

If you encounter the error `FATAL  Error: Request Timeout after 30000ms` during startup, try running OpenSearch Dashboards on a more powerful machine. We recommend four CPU cores and 8 GB of RAM.


## Can't open OpenSearch Dashboards on Windows

OpenSearch Dashboards doesn't support Microsoft Edge and many versions of Internet Explorer. We recommend Firefox or Chrome.


## Beats

If you encounter compatibility issues when attempting to connect Beats to OpenSearch, make sure you're using the Apache 2.0 distribution of Beats, not the default distribution, which uses a proprietary license.

Try this minimal output configuration for using Beats with the security plugin:

```yml
output.elasticsearch:
  hosts: ["localhost:9200"]
  protocol: https
  username: "admin"
  password: "admin"
  ssl.certificate_authorities:
    - /full/path/to/root-ca.pem
  ssl.certificate: "/full/path/to/client.pem"
  ssl.key: "/full/path/to/client-key.pem"
```

Even if you use the OSS version, Beats might check for a proprietary plugin on the OpenSearch server and throw an error during startup. To disable the check, try adding these settings:

```yml
setup.ilm.enabled: false
setup.ilm.check_exists: false
```


## Logstash

If you have trouble connecting Logstash to OpenSearch, try this minimal output configuration, which works with the security plugin:

```conf
output {
  elasticsearch {
    hosts => ["localhost:9200"]
    index => "logstash-index-test"
    user => "admin"
    password => "admin"
    ssl => true
    cacert => "/full/path/to/root-ca.pem"
    ilm_enabled => false
  }
}
```


## Can't update by script when FLS, DLS, or field masking is active

The security plugin blocks the update by script operation (`POST <index>/_update/<id>`) when field-level security, document-level security, or field masking are active. You can still update documents using the standard index operation (`PUT <index>/_doc/<id>`).


## Illegal reflective access operation in logs

This is a [known issue](https://github.com/opensearch-project/performance-analyzer/issues/21) with Performance Analyzer that shouldn't affect functionality.


## Multi-tenancy issues in OpenSearch Dashboards

If you're testing multiple users in OpenSearch Dashboards and encounter unexpected changes in tenant, use Google Chrome in an Incognito window or Firefox in a Private window.