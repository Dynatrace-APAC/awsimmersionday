# Hands on 1

**Objective:** Rapid time to observability

**What you will do:**
- Deploy Dynatracec Operator
- Deploy pre-configured Custome Resoruce yaml file in **Classic full stack** deployment mode 

### Commands to Deploy Dynatrace Operator

Reference: [Set up Dynatrace on Red Hat OpenShift](https://www.dynatrace.com/support/help/setup-and-configuration/setup-on-container-platforms/openshift)

[Classic Full Stack installation](https://www.dynatrace.com/support/help/setup-and-configuration/setup-on-container-platforms/openshift/set-up-ocp-monitoring)

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

Apply the DynaKube custom resource

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
- Configure Dynatrace to collect kubernetes events
- Annotate pods to scrape metrics from Prometheus exporters 

### Commands to Annotate Prometheus exporter pods

```bash
kubectl annotate po -n production --all --overwrite metrics.dynatrace.com/scrape=true
kubectl annotate po -n production --all --overwrite metrics.dynatrace.com/port=8080
```

# Hands on 3

**Objective:** Bring intelligence to use cases

**What you will do:**
- Configure Dynatrace for a custom alert
- Trigger a deployment
- Watch how DAVIS reduces MTTR for you

### Scenario
- OCP platform administrators set a resource quota that your project (namespace) needs to comply with
- Application team's responsibility to assign given resources to different microservices

Apply this deployment by typing in this command:

```bash
kubectl apply -f ~/sockshop/manifests/sockshop-app/newbuilds/newbuild-quota.yml
```

To apply the fix:

```bash
kubectl apply -f ~/sockshop/manifests/sockshop-app/newbuilds/newbuild-quota-fix.yml
```
