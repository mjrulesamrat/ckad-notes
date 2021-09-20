[![Software License](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)


# CKAD Notes

CKAD preparations notes for imperative commands

- [Commands Guide](#commands-guide)

## Contents

- [Core Concepts - 13%](#core-concepts)
- [Multi-container pods - 10%](#multi-container-pods)
- [Configuration - 18%](#configuration)
- [Pod design - 20%](#pod-design)
- [Observability - 18%](#observability)
- [Services and networking - 13%](#services-and-networking)
- [State persistence - 8%](#state-persistence)


# Set Alias

It helps!

```
alias k=kubectl
```


# Commands Guide

In a nutshell,
- Docker EntryPoint -> Command (`--command -- here`)

- Command -> Arguments (`-- something`)

Below are few references that I have picked up during the practices.

```
$ --command -it -- echo "Hello World!"  # prints the output to shell directly
$ -- /bin/sh -c 'echo "Hello WorlD!!"'

# print environment variables
$ -it -- printenv
$ -- env # works the same but check logs with
$ k logs resource-name

# Go to shell and check env
$ k exec -it po/nginx -- /bin/sh
$ env

# sleep busybox pox for given time
$ -- sleep 3600 # this will go as arguments
$ --command -- /bin/sh -c 'sleep 3600'
# otherwise just dry run and add
    command: ['sleep', '3600']
    --command -- sleep 3600  # to get above result in yaml

$ k describe po/nginx1 | grep -C 4 -i "Memory" # 4 lines before and after of the match

$ grep -i "error" # case insensitive search
$ grep -C -i error # get 4 lines before and after of the found match
```

# Core Concepts

Creating and Managing Pods

```
$ kubectl run nginx --image=nginx --restart=Never --dry-run=client -o yaml > pod1.yaml

# add --rm command to remove pod once command is ran
$ k run box --image=busybox --rm -it --command -- /bin/sh -c 'wget -O- IP'

$ k run box --image=busybox --restart=Never --rm -it --command -- env

```

# Multi Container Pods

Multiple containers in a single pod. Three design patterns,

  1. Side-car Pattern
  2. Ambassador Pattern
  3. Adapter Pattern

  Create a multi-container pod, each will have diff container name and image. One can be busybox and second can be nginx, each will have diff commands and arguments, ports etc.

  You will be then asked to connect one container and check logs or get logs to some local file on the node.

  ```
  $ k logs po/mypo -c nginx  # logs from nginx container
  $ k logs po/mypo -c box  # logs from box container
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

# Pod Design

Deployments, Rolling Updates, Jobs and Cron Jobs

```
# get all the resources and using label selector
$ k get all --selector env=prod

# create a deployment and then label it
$ k create deploy nginx --image=nginx --replicas=2
$ k label deploy/nginx --overwrite app=foo

```

# Observability

```
# Get all failure events
$ k get events

# Include namespace as well
$ k -n ns get events | grep -i "probe failed"

# Check logs of any resource
$ k logs name

# Top resource utilisations for nodes & pods
$ k top nodes
$ k top pods
$ k top po/po-name
```

Debugging, Logging, Monitoring
# Services and NetworkPolicies

Deployments, Services, NetworkPolicies

```
# Pod can be exposed directly using --expose argument
# deployment can not be exposed directly

# Best way to create a service is using yaml file
$ k create deploy nginx --image=nginx --port=80
$ k expose deploy/name --name=service-name --port=80  # default port & targetport are same. If not same, then specify

# When you apply the Network Policy with labels, test it out
$ kubectl run busybox --image=busybox --rm -it --restart=Never -- wget -O- http://nginx:80 --timeout 2
$ kubectl run busybox --image=busybox --rm -it --restart=Never --labels=access=granted -- wget -O- http://nginx:80 --timeout 2
```

# State Persistence

- Volumes - VolumeMounts
  - name, emptyDir
  - name, mountPath
- Persistent Volume - create using CRD
- Persistent Volume Claims - create using CRD

```bash
$ kubectl run busybox --image=busybox --restart=Never -- sleep 3600

# kubectl copy command
$ kubectl cp busybox:etc/passwd ./passwd

# previous command might report an error
# feel free to ignore it since copy command works
$ cat passwd
```
