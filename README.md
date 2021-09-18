[![Software License](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)


# CKAD Notes

CKAD preparations notes for imperative commands

- [Commands Guide](#commands-guide)

## Contents

- [Core Concepts - 13%](#core-concepts)
- [Multi-container pods - 10%](#multi-container-pods)
- [Pod design - 20%](#pod-design)
- [Configuration - 18%](#configuration)
- [Observability - 18%](#observability)
- [Services and networking - 13%](#services-and-networking)
- [State persistence - 8%](#state-persistence)


# Set Alias

It helps!

```
alias k=kubectl
```


# Commands Guide

These are basic commands we can run inside a pod, a container in a pod, as a startup command, as arguments to a pod etc.

Use these arguments to perform some commands for given pod.

```
$ --command -it -- echo "Hello World!"  # prints the output to shell directly
$ -- /bin/sh -c 'echo "Hello WorlD!!"'

# print environment variables
$ -it -- printenv
$ -- env # works the same but check logs with k logs resource-name
$ k exec -it po/nginx -- /bin/sh
$ env

$ -- sleep 3600 # this will go as arguments
$ k describe po/nginx1 | grep -C 4 -i "Memory" # 4 lines before and after of the match

$ grep -i "error" # case insensitive search
$ grep -C -i error # get 4 lines before and after of the found match
```

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

```
# get all the resources and using label selector
$ k get all --selector env=prod
```

# Configuration

ConfigMaps, Secrets, Security Contexts, Requests & Limits, Service Accounts

```
# decode the secret
$ echo -n "encoded" | base64 --decode

# Taints & Tolerations
# Tains are set on Nodes
# Tolerations are set on Pods
$ k taint node node-name key=valu:Effect

# Node Selector
$ k label node nodename key=value

# Node affinity
# it's for Pod only. On which pod we want to schedule the pod.
# Get used to adding diff NodeAffinity config for given Pod.
```

# Observability

Debugging, Logging, Monitoring
# Services and NetworkPolicies

Deployments, Services, NetworkPolicies

# State Persistence

Persistence Volume and Persistence Volume Claims
