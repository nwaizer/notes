## Deploy hw-event-proxy using operator
```
#Apply workarond for workload bug 2052586
patch config.manager.manager.patch

# under hw-event-proxy-operator repo:
make deploy

# Get pod name
oc get pods -n hw-event-proxy-operator-system
# check operator logs:
oc -n hw-event-proxy-operator-system logs -f hw-event-proxy-operator-controller-manager-fd95dfff4-7cngx -c manager

# find the worker node name
oc get nodes

# under hw-event-proxy repo:
oc label --overwrite node <name of worker node> app=local
make deploy-amq

# deploy hw-event-proxy. Update the amqp host address
#   transportHost: "amqp://router.router.svc.cluster.local"
# cd to the hw-event-proxy-operator directory
# Also can run this sed -i 's#amqp://amq-router-service-name.amq-namespace.svc.cluster.local#amqp://router.router.svc.cluster.local#' config/samples/event_v1alpha1_hardwareevent.yaml
oc apply -f config/samples/event_v1alpha1_hardwareevent.yaml -n hw-event-proxy-operator-system

#Fix the redfish address to the system under test, for example my helix28 is 10.46.61.213
sed -i 's/10.19.28.42/10.46.61.213/' tests/e2e/manifest/secret.yaml
#Fix redfish username and password
#echo -n "root"|base64
#-> cm9vdA==

oc apply -f tests/e2e/manifest/secret.yaml -n hw-event-proxy-operator-system

# get example consumer.yaml from this repo
oc apply -f consumer.yaml -n hw-event-proxy-operator-system

oc -n hw-event-proxy-operator-system get pods
```
