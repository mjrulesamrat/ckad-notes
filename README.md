[![Software License](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)


# CKAD Notes

CKAD preparations notes for commands

## Contents

- [Core Concepts - 13%](#core-concepts)
- [Multi-container pods - 10%](#multi-container-pods)
- [Pod design - 20%](#pod-design)
- [Configuration - 18%](#configuration)
- [Observability - 18%](#observability)
- [Services and networking - 13%](#services-and-networking)
- [State persistence - 8%](#state-persistence)

# Core Concepts

Creating and Managing Pods

Commands:

```
$ kubectl run nginx --image=nginx --restart=Never --dry-run=client -o yaml > pod1.yaml

```

# Multi Container Pods

Multiple containers in a single pod. Three design patterns,

  1. Side-car Pattern
  2. Ambassador Pattern
  3. Adapter Pattern

# Pod Design

Deployments, Rolling Updates, Jobs and Cron Jobs

# Configuration

ConfigMaps, Secrets, Security Contexts, Requests & Limits, Service Accounts

# Observability

# Services and Networking

# State Persistence
