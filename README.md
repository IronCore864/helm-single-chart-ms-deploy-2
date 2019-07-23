# A Single Helm Chart to Deploy Multiple Different Microservices with Different ConfigMaps

A different approach.

## Prepare

### Docker

Image used: `ironcore864/hello-world-py`, a simple python helo world with reading one env var.

### K8s

Assuming you have a k8s and helm installed.

## Deploy Service A

```
cd ms-one && helm dependency update && cd .. 
helm install -n ms-one ./ms-one
```

`ms-one` chart only contains a configmap, and it calls a `msgeneric` chart to deploy the service and deployment.

Values are passed to dependency.

## Deploy Service B

```
cd ms-one && helm dependency update && cd .. 
helm install -n ms-two ./ms-two
```

Same as `ms-one`, `ms-two` chart only contains a configmap, and it uses the same dependency chart to deploy the service and the deployment.

## Test

```
kubectl proxy
# open another tab
curl http://127.0.0.1:8001/api/v1/namespaces/default/services/ms-one/proxy/
curl http://127.0.0.1:8001/api/v1/namespaces/default/services/ms-two/proxy/

## For Using Two Values Files

For example, you can put common settings in `values.yaml`, while keep settings that are different bertween different ENVs in separate file, like `values.prod.yaml`.

When deploying:

```
helm install -n ms-one ./ms-one -f ./ms-one/values.prod.yaml
```

Both `values.yaml` and `values.prod.yaml` will be used and combined together, and the `values.prod.yaml` will override `values.yaml` if same keys exist in both files..

