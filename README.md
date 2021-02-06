# Camel Source Examples

This repository contains some examples of Camel Sources that can be used to send data to any Knative endpoint.

In these examples we'll use the default Knative eventing broker to send events to.

## Environment Setup

Make sure you're connected to an OpenShift or Kubernetes cluster.

- OpenShift Serverless or Knative Eventing

I created a local cluster and install Knative using https://konk.dev
```bash
curl -sfL get.konk.dev | sh
```

- [Camel K](https://github.com/apache/camel-k)

I installed this way
```bash
REGISTRY_NAMESPACE=csantanapr
kamel install --registry-auth-username=${REGISTRY_NAMESPACE} --registry-auth-password=<dockerpassword> --registry docker.io --organization ${REGISTRY_NAMESPACE}
```

- [Knative Apache Camel Sources](https://github.com/knative-sandbox/eventing-camel)

I installed this way
```
kubectl apply -f https://github.com/knative-sandbox/eventing-camel/releases/download/v0.20.0/camel.yaml
```


After you've installed the required operators, you can create a sample project with the command:

```
oc new-project sources-example
```

To install the Knative default broker, edit the namespace labels to inject it:

```
oc label namespace/sources-example knative-eventing-injection=enabled
```

We'll use a dumper service to verify the messages that Camel Sources will produce.
Use Camel K CLI to install the service:

```
kamel run dumper.groovy
```

The dumper is using a generic `knative:endpoint` uri, so the trigger to receive all events should be created manually:

```
oc apply -f trigger.yaml
```

## Creating Sources

We can now start to create various kind of sources.

### 01. Hello World Camel Source

This source just send a fixed string to the broker as cloud event with type `hello.world`.

To create it:

```
oc apply -f 01-hello-world.yaml
```

### 02. Chuck Camel Source

This source sends random nerdy quotes about chuck norris as cloud event with type `chuck.norris`.

To create it:

```
oc apply -f 02-chuck.yaml
```

### 03. Webhook Camel Source

This source transforms a call to a webhook into a cloud event with type `webhook`.

To create it:

```
oc apply -f 03-webhook.yaml
```

### 04. MQTT Camel Source

This source forwards forwards all MQTT messages sent to a mosquitto broker to the serverless broker as cloud events with type `mqtt`.

You first need to create a simple mosquitto broker and a sample message producer:

```
oc apply -f mqtt-broker/mosquitto.yaml
kamel run mqtt-broker/sample-sender.groovy
```

Them, to create the source:

```
oc apply -f 04-mqtt.yaml
```
