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

```bash
alias k=kubectl
```

# Shortcuts/Aliases

Saves time!

1. **po** for Pods
2. **rs** for ReplicaSets
3. **deploy** for Deployments
4. **svc** for Services
5. **ns** for Namespaces
6. **netpol** for Network policies
7. **pv** for PersistentVolume
8. **pvc** for PersistentVolumeClaims
9. **sa** for Service accounts

```bash
# get all resource
$ k get all -A

# get api-resources
$ k api-resources
```

# Tips

1. Start with first one and solve if it's easy
2. Hard ones. Skip and take note
3. Moderate One. You know it. Attempt it. If error, don't spend too much time. Note and Move on!
4. Don't spend too much time editing YAML. Get your YAML basics together!
5. Use aliases and shortcuts

# Commands Guide

In a nutshell,
- Docker EntryPoint -> Command (`--command -- here`)

- Command -> Arguments (`-- something`)

Below are few references that I have picked up during the practices.

```bash
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

```bash
$ kubectl run nginx --image=nginx --restart=Never --dry-run=client -o yaml > pod1.yaml

# create namespace
$ k create ns myns

# check current namespace
$ k config view --minify | grep namespace

# set current namespace
$ k config set-context --current --namespace=myns

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

  ```bash
  $ k logs po/mypo -c nginx  # logs from nginx container
  $ k logs po/mypo -c box  # logs from box container
  ```

# Configuration

ConfigMaps, Secrets, Security Contexts, Requests & Limits, Service Accounts

- use --from-literal command to create cm, env and secret
- Have bookmarks ready for complex example like configmap with volume, set env variable from configmap, use secret as volume, have all env from file etc.
- Get used to setting Tolerations & NodeAffinity

```bash
# decode the secret
$ echo -n "encoded" | base64 --decode

# Taints & Tolerations
# Tains are set on Nodes
# Tolerations are set on Pods
$ k taint node node-name key=valu:Effect

# Node Selector | use the same for container to schedule pod with given labeled node
$ k label node nodename key=value

# Node affinity
# it's for Pod only. On which pod we want to schedule the pod.
# Get used to adding diff NodeAffinity config for given Pod.
```

# Pod Design

- Labels, Annotations, Selectors

- Deployments, Rolling Updates, Jobs and Cron Jobs

```bash
# get all the resources and using label selector
$ k get all --selector env=prod

# display labels
$ k get po --show-labels

# get particular labeled po resources
$ k get po -l app=v2

# create a deployment and then label it
$ k create deploy nginx --image=nginx --replicas=2
$ k label deploy/nginx --overwrite app=foo

# remove label or annotation
$ k label deploy/nginx app-
$ k annotate po/nginx description-

```

# Observability

- It's the art of knowing what's wrong with the given resource. Make sure you're in the right namespace.

- Check events with below command:
    ```
    $ k describe po po-name
    ```
- Figure out what is wrong. Check Image, Port, readinessProbe, LivenessProbe

```bash

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

Services

- Two Types: Cluster IP & NodePort

Network polices

- Ingress and Egress
- Have pod selector understood correctly

Ingress

- check services port before creating ingress resource. CKAD does not ask you to create ingress controller. Don't worry about the 300XX Ports.
- Check if you need to add ingress resource for host name or just re-write the path

```bash
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
