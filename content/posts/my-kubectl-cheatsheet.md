+++
title = "My kubectl Cheatsheet"
date = "2023-07-14"
author = "Andrea Cirigliano"
cover = "img/k8s.webp"
description = "A collection of commands and tools I use to play with k8s."
+++

If Kubernetes is the jungle of modern software engineers, then kubectl is our Swiss army knife. It's an essential tool to inspect and troubleshoot our deployments.
In this post I collected the commands and the tools that I find more useful.


## Find a pod by substring
```bash
kubectl get pods | grep my-app
```

You can also filter your app using `kubectl -l app=my-app`, but I find grep more useful because I never remember the full name correctly.


## Show pod logs
```bash
kubectl logs -f my-pod
```
Once I find the name of the pod, I usually inspect its log tail.


## Show logs from multiple pods
```bash
stern pod-query
```
Sometimes you want to see all the logs of an app with multiple replicas. In this case [stern](https://github.com/stern/stern) is a fantastic tool that allows you to tail multiple pods at the same time.
The query can be a regular expression, so for example you can provide `web-\w` to tail `web-backend` and `web-frontend`.


## Restart a deploy
```bash
kubectl rollout restart deploy/my-app
```
If you want to refresh some configuration, or your deployment is having trouble, it may be useful to refresh it spawning new pods and deleting the old ones. There will be no downtime during the restart. I find this very useful when I get paged by a service during the on-call shift and I don't have any clue about what's going on (https://xkcd.com/1495/).


## Port forward to a pod
```bash
kubectl port-forward my-pod 28015:9989
```
This command makes the port 9989 of `my-pod` available at `localhost:28015`. This is super useful to debug an endpoint that is not easily accessible through an ingress.

## Create a job from a cronjob:
```bash
kubectl create job --from=cronjob/my-cronjob job-name
```

Sometimes you have a scheduled cronjob that you want to start manually in that moment. This command create a job from the cronjob.

## Suspend / resume a cronjob
```bash
kubectl patch cronjobs my-cronjob -p '{"spec":{"suspend":true}}'
```
This command let's you suspend or resume the executions of a k8s cronjob.

> ⚠️ **Warning**
>
>Executions that are suspended during their scheduled time count as missed jobs [[1]](https://kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/#suspend).
>
>If there are more than 100 missed schedules, then the jobs will not start, even after restoring `suspend` to `false` [[2]](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/#cron-job-limitations). In that case you need to delete and recreate the cronjob.


## Create a Basic Auth secret
Sometimes you want to protect your ingress with a basic authentication.

```bash
# Create file with secret (type-in your password)
htpasswd -c auth username
# Create k8s secret
kubectl create secret generic my-app-basic-auth --from-file=auth
```

The secret can now be used in the annotation of your nginx ingress:
```yaml
annotations:
  ingress:
    nginx.ingress.kubernetes.io/auth-type: basic
    nginx.ingress.kubernetes.io/auth-secret: my-app-basic-auth
```

## k9s

You can perform many of the above actions using [k9s](https://k9scli.io/), a terminal based UI to interact with your Kubernetes clusters. Nevertheless, I prefer `kubectl` most of the times.

## kubectx
[kubectx](https://github.com/ahmetb/kubectx) is a tool to switch between contexts (clusters) on kubectl faster. Contexts are defined in your `~/.kube/config` file.

```bash
# install
brew install kubectx
# switch to development
kubectx development
# switch to production
kubectx production
```

## Multiple kubectl versions
Your kubectl client version should always match the server version to ensure full compatibility. 
If you have to deal with different clusters with different version, you may want to switch easily between different client version.

To do this I use [asdf](https://asdf-vm.com/), a multiple runtime versions manager:

```bash
# install
brew install asdf
# add kubectl plugin
asdf plugin-add kubectl https://github.com/asdf-community/asdf-kubectl.git
# install a version
asdf install kubectl 1.24.14
# install another version
asdf install kubectl 1.27.3
# select version
asdf global kubectl 1.27.3
```
