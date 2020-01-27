# Inlets Playground

This repository contains all the resources needed to deploy an [inlets](https://github.com/alexellis/inlets) exit node into a free namespace at [k8spin.cloud](https://k8spin.cloud). It is based on the [k8spin inlets example repo](https://gitlab.com/k8spin-open/examples/inlets).

## Serverside:
* Get a [K8Spin.cloud](https://k8spin.cloud) kubernetes configuration file.
* Create a common TOKEN, e.g. with 
  ```bash
  echo $(head -c 16 /dev/urandom | shasum | cut -d" " -f1)
  ```
  Adjust TOKEN in [deploy/kustomization.yaml](deploy/kustomization.yaml)
* Change the [deploy/ingress.yml](deploy/ingress.yml) file. 
  * Modify the `cert-manager.io/issuer` annotation to your issuer name (`kubectl get issuer`).
  * Look for the assigned `ingress domain` with K8Spin. Adjust the domain name under `hosts` and `host:`. (Also adjust clientside below).
* Now deploy with kustomize: `kubectl apply -k deploy`.

## Clientside:
* Adjust below values for TOKEN and ingress domain as adequate.
* Install inlets cli on Mac OS with `brew install inlets`, or see other options at [inlets](https://github.com/alexellis/inlets).
* Run a service in your local machine. For example, run [Python's built-in HTTP server](https://docs.python.org/2/library/simplehttpserver.html):
  ```bash
  python -m SimpleHTTPServer 3000
  ```
* Start the [inlets](https://github.com/alexellis/inlets) client on your local machine with:
  ```bash
  inlets client --remote="wss://inlets.vasu1124.apps.k8spin.cloud" --upstream="localhost:3000" --token=TOKEN --print-token=false
  ```
  where 
  - `--remote` points to the ingress hostname of the serverside.
  - `--upstream` is the local service that will be exposed through the remote exit node. 
  - `--token` is common/secret TOKEN.

## Result

Open [https://inlets.vasu1124.apps.k8spin.cloud](https://inlets.vasu1124.apps.k8spin.cloud) and you will be routed to your local server.