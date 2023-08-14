# Prep

1. [AWS Account, Number3](https://learn.alliances.dynatracelabs.com/codelabs/aws-lab0%20immersion-day/index.html?index=..%2F..aws-immersion-day#2)
1. [CloudShell, Number 4](https://learn.alliances.dynatracelabs.com/codelabs/aws-lab0%20immersion-day/index.html?index=..%2F..aws-immersion-day#3)
1. Clone github repo
   ```bash
   git clone https://github.com/dt-alliances-workshops/aws-modernization-dt-orders-setup.git
   ```
1. [Stand up lab environment, Number 5](https://learn.alliances.dynatracelabs.com/codelabs/aws-lab0%20immersion-day/index.html?index=..%2F..aws-immersion-day#4)

# Hands-on #1

1. Deploy Dynatrace Operator
   ```bash
   kubectl create namespace dynatrace
   kubectl apply -f https://github.com/Dynatrace/dynatrace-operator/releases/download/v0.12.1/kubernetes.yaml
   kubectl -n dynatrace wait pod --for=condition=ready --selector=app.kubernetes.io/name=dynatrace-operator,app.kubernetes.io/component=webhook --timeout=300s
   kubectl apply -f dynakube.yaml
   ```
1. Deploy application
   ```bash
   cd ~/aws-modernization-dt-orders-setup/app-scripts
   ./start-k8.sh
   ```
1. Verify application pods
   ```bash
   kubectl -n staging get pods
   ```
1. Expected output
   ```bash
   NAME                               READY   STATUS    RESTARTS   AGE
browser-traffic-5b9456875d-ks9vw   1/1     Running   0          30h
catalog-7dcf64cc99-hfrpg           1/1     Running   0          2d8h
customer-8464884799-vljdx          1/1     Running   0          2d8h
frontend-7c466b9d69-9ql2g          1/1     Running   0          2d8h
load-traffic-6886649ddf-76pqf      1/1     Running   0          2d8h
order-6d4cd477cb-9bvn4             1/1     Running   0          2d8h
   ```

**What you will do:**
- Deploy Dynatracec Operator
- Deploy pre-configured Custome Resource yaml file in **Classic full stack** deployment mode 

```bash
Commands to copy
```
