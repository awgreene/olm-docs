---
title: "Continuous Profiling"
weight: 10
description: >
  The goal of this document is to familiarize you with OLM's stance on performance profiling.
---

## Prerequisites 

- [go](https://golang.org/dl/)

## Background

OLM utilizes the [pprof package](https://golang.org/pkg/net/http/pprof/) from the standard go library to expose performance profiles for the OLM Operator, the Catalog Operator, and Registry Servers. Due to the sensitive nature of this data, client requests against the pprof endpoint are rejected unless they are made with the certificate data kept in the `pprof-cert secret` in the `olm namespace`. 
Kubernetes does not provide a native way to prevent pods on cluster from iterating over the list of available ports and retrieving the data exposed. Without authetnicating the requests, OLM could leak customer usage statistics on multitenant clusters. If the aforementioned secret does not exist the pprof data will not be accessable.

### Retrieving PProf Data

#### OLM Operator

```bash
$ go tool pprof http://localhost:8080/debug/pprof/heap #TODO: Replace with actual command
```

#### Catalog Operator

```bash
$ go tool pprof http://localhost:8080/debug/pprof/heap #TODO: Replace with actual command
```

#### Registry Server

```bash
$ go tool pprof http://localhost:8080/debug/pprof/heap #TODO: Replace with actual command
```

<details>
  <summary>Downstream docs, click to expand!</summary>
  
## Continuous Profiling
OLM relies on [pprof-dump]() to periodically collect the pprof data and store it in the contents of a `ConfigMap`. The data in these `ConfigMaps` may be referenced when debugging issues.

### Default PProf-Dump Settings

OLM configures pprof-dump with the `pprof-dump ConfigMap` setting the follow default configurations:

```yaml
kind: ConfigMap
metadata:
  name: prof-dump
  namespace: olm
Data:
  garbageCollection: 60 # Delete configmaps older than 60 minutes
  poll: 15 # interval in minutes that pprof data is collected and dumped into ConfigMaps
```

### How do I disable continuous profiling?

To disable OLM's continuous profiling, apply the following YAML:

```yaml
kind: ConfigMap
metadata:
  name: prof-dump
  namespace: olm
Data:
  garbageCollection: 60
  poll: 0
```
</details>