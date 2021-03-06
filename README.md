# Hands on 1

**Objective:** Rapid time to observability

**What you will do:**
- Deploy Dynatracec Operator
- Deploy pre-configured Custome Resource yaml file in **Classic full stack** deployment mode 

### Commands to Deploy Dynatrace Operator

Reference:
- [Set up Dynatrace on Red Hat OpenShift](https://www.dynatrace.com/support/help/setup-and-configuration/setup-on-container-platforms/openshift)
- [Classic Full Stack installation](https://www.dynatrace.com/support/help/setup-and-configuration/setup-on-container-platforms/openshift/set-up-ocp-monitoring)

Create a dynatrace namespace

```bash
kubectl create namespace dynatrace
```

Install Dynatrace Operator

```bash
kubectl apply -f https://github.com/Dynatrace/dynatrace-operator/releases/latest/download/kubernetes.yaml
```

Wait until Dynatrace Operator components finish initialization.

```bash
kubectl -n dynatrace wait pod --for=condition=ready -l internal.dynatrace.com/app=webhook --timeout=300s
```

Apply the DynaKube custom resource. [Sample files here](https://dt-url.net/dynakube-samples)

Change to the direcotry where dynakube.yaml is saved in, then run

```bash
kubectl apply -f dynakube.yaml
```

### Commands to restart the Sockshop application

Dev namespace

```bash
kubectl delete pods --all -n dev
```

Production namespace

```bash
kubectl delete pods --all -n production
```

# Hands on 2

**Objective:** Bring context of kubernetes events, prometheus metrics in a click of a button

**What you will do:**
- Configure Dynatrace to collect kubernetes events (restart the pods in the **dev** namespace to see the events coming in)
- Annotate pods to scrape metrics from Prometheus exporters 

### Kubernetes events

Reference: [Kubernetes events](https://www.dynatrace.com/support/help/how-to-use-dynatrace/infrastructure-monitoring/container-platform-monitoring/kubernetes-monitoring/monitor-events-kubernetes)

Command to get all kubernetes events

```bash
kubectl get events --all-namespaces
```

Command to get kubernetes events in dev namespace only

```bash
kubectl get events --all-namespaces --field-selector involvedObject.namespace=dev
```

Command to get kubernetes Warning events only

```bash
kubectl get events --all-namespaces --field-selector type=Warning
```

### Commands to Annotate Prometheus exporter pods

Reference: [Annotate Prometheus exporter pods](https://www.dynatrace.com/support/help/how-to-use-dynatrace/infrastructure-monitoring/container-platform-monitoring/kubernetes-monitoring/monitor-prometheus-metrics#annotate-prometheus-exporter-pods)

```bash
kubectl annotate po -n production --all --overwrite metrics.dynatrace.com/scrape=true
kubectl annotate po -n production --all --overwrite metrics.dynatrace.com/port=8080
```

# Hands on 3

**Objective:** Bring intelligence to use cases

**What you will do:**
- Configure Dynatrace for a custom alert
- Trigger a deployment
- Watch how DAVIS reduces Mean-time-to-**Detect** (MTTD) for you

### Scenario
- OCP platform administrators set a resource quota that your project (namespace) needs to comply with
- Application team's responsibility to assign given resources to different microservices

Apply this deployment by typing in this command:

```bash
kubectl apply -f ~/sockshop/manifests/sockshop-app/newbuilds/newbuild-quota.yml
```

To recover from the problem, apply a new yaml file:

```bash
kubectl apply -f ~/sockshop/manifests/sockshop-app/newbuilds/newbuild-quota-fix.yml
```
