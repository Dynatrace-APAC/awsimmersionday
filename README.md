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
1. Dynakube resource samples for various [Dynatrace Operator deployment methods](https://github.com/Dynatrace/dynatrace-operator/tree/v0.12.1/assets/samples)
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
1. Access sample app from browser
   ```bash
   kubectl -n staging get svc
   ```
1. Expected output
   ```bash
   NAME       TYPE           CLUSTER-IP       EXTERNAL-IP
   catalog    ClusterIP      10.100.181.171   <none>                                                                  
   customer   ClusterIP      10.100.147.216   <none>
   frontend   LoadBalancer   10.100.58.2      a6ebaa4a370e0468093167462c3aeab2-115097342.us-west-2.elb.amazonaws.com
   order      ClusterIP      10.100.228.17    <none>
   ```
1. From the output, copy the entire value from EXTERNAL-IP and open it in a browser. This would be `http://1a6ebaa4a370e0468093167462c3aeab2-115097342.us-west-2.elb.amazonaws.com` for the example above.
1. Release view - k8s label and annotations: `orders` [application yaml file](https://github.com/dt-alliances-workshops/aws-modernization-dt-orders-setup/blob/main/app-scripts/manifests/order-service.yml)

# Hands-on #2

Automation scripts
- [CloudFormation template](https://github.com/dt-alliances-workshops/aws-modernization-dt-orders-setup/blob/main/provision-scripts/cloud-formation/workshopMonolith.yaml#L142) for EC2 with OneAgent
- CloudWatch integgration (one-time setup)
  - [ActiveGate role](https://dt-url.net/pv0306t)
  - [IAM role and polices](https://dt-url.net/f30301j)
  - Automating the [Dynatrace configuration, line 139](https://github.com/dt-alliances-workshops/aws-modernization-dt-orders-setup/blob/main/provision-scripts/provision-workshop.sh#L139) 

# Hands-on #3

Copy and paste following nodejs code. Ensure that you replace the placeholder of `REPLACE with your EKS application URL` with the URL of the k8s micro-service app.

```js
/**
 * This is a very simply lambda function that simply executes random HTTP Requests to a randomly selected group of URLs
 * With the Dynatrace OneAgent injected you can end-to-end trace these calls
 * 
 */ 
exports.handler = (event, context, callback) => {
    // TODO implement
    executeRequest("http://<REPLACE with your EKS application URL>/order");
  };

var executeRequest = function(url, callback) {
    var https = require("http");
    var fullUrl = require("url").parse(url);

    var request_options = {
      host: fullUrl.host,
      path: fullUrl.path,
      method: 'GET',
    };

    // Set up the request
    var get_req = https.request(request_options, function(res) {
        var responseBody = "";
        res.setEncoding('utf8');
        res.on('data', function (chunk) {
            responseBody += chunk;
        });
        res.on('end', function() {
            console.log(url + ": StatusCode = " + res.statusCode + " ContentLength: " + responseBody.length);
            //callback(null, null);
        });
    });

    // post the data
    get_req.end();   
}   
```

# Hands-on #4

1. Deploy version 3 of micro-services app
   ```bash
   cd ~/aws-modernization-dt-orders-setup/learner-scripts/
   ./set-version.sh k8-order 3
   ```
1. Deploy version 1
   ```bash
   cd ~/aws-modernization-dt-orders-setup/learner-scripts/
   ./set-version.sh k8-order 1
   ```

# Hands-on #5

1. Deploy new app
   ```bash
   cd ~/aws-modernization-dt-orders-setup/app-scripts
   kubectl create ns boom-app
   kubectl apply -f boom-app.yaml -n boom-app
   ```
1. Validate running pods
   ```bash
   kubectl -n boom-app get pods -l app=boom-app -w
   ```
1. Expected output
   ```bash
   NAME                        READY   STATUS              RESTARTS   AGE
   boom-app-867f995854-mwcsg   0/1     ContainerCreating   0          16s
   boom-app-867f995854-mwcsg   1/1     Running             0          18s
   boom-app-867f995854-mwcsg   0/1     Error               0          60s
   boom-app-867f995854-mwcsg   1/1     Running             1 (2s ago)   62s
   boom-app-867f995854-mwcsg   0/1     Error               1 (45s ago)   105s
   boom-app-867f995854-mwcsg   0/1     CrashLoopBackOff    1 (13s ago)   117s
   boom-app-867f995854-mwcsg   1/1     Running             2 (15s ago)   119s
   ```
1. CTRL+C to get out of the kubectl command
1. Clean up the Boom-App
   ```bash
   kubectl delete ns boom-app
   kubectl get ns
   ```
