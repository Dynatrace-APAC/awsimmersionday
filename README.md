# Hands on 1

## Commands to Deploy Dynatrace Operator

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

```bash
kubectl apply -f DynaKube.yaml
```

## Commands to restart the Sockshop application

Dev namespace

```bash
kubectl delete pods --all -n dev
```

Production namespace

```bash
kubectl delete pods --all -n production
```

#Hands on 2

## Commands to Annotate Prometheus exporter pods

```bash
kubectl annotate po -n production --all --overwrite metrics.dynatrace.com/scrape=true
kubectl annotate po -n production --all --overwrite metrics.dynatrace.com/port=8080
```

# Hands on 3

## Scenario
- OCP platform administrators set a resource quota that your project (namespace) needs to comply with
- Application team's responsibility to assign given resources to different microservices

Apply this deployment by typing in this command:

```bash
kubectl apply -f ~/sockshop/manifests/sockshop-app/newbuilds/newbuild-quota.yml
```

```bash
kubectl apply -f ~/sockshop/manifests/sockshop-app/newbuilds/newbuild-quota-fix.yml
```
