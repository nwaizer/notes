## Deploy hw-event-proxy using operator
```
# under hw-event-proxy-operator repo:
make deploy

# check operator logs:
oc -n hw-event-proxy-operator-system logs -f hw-event-proxy-operator-controller-manager-fd95dfff4-7cngx -c manager

# find the worker node name
oc get nodes

# under hw-event-proxy repo:
oc label --overwrite node <name of worker node> app=local
make deploy-amq

# deploy hw-event-proxy. Update the amqp host address
#   transportHost: "amqp://router.router.svc.cluster.local"
oc apply -f config/samples/event_v1alpha1_hardwareevent.yaml -n hw-event-proxy-operator-system

oc apply -f tests/e2e/manifest/secret.yaml -n hw-event-proxy-operator-system

# get example consumer.yaml from this repo
oc apply -f consumer.yaml -n hw-event-proxy-operator-system

oc -n hw-event-proxy-operator-system get pods
```