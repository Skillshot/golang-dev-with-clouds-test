# Coinbase pinger

Coinbase pinger is an application that periodically pings the Coinbase API and updates a status of a custom Kubernetes resource according to a result.

The Kubernetes Custom Resource Definition `CoinbasePinger` has the following schema:

```yaml
apiVersion: dev.org/v1
kind: CoinbasePinger

spec:
  endpoint: "prices/BTC-USD/buy"
  interval: "60s"

status:
  conditions:
  - type: "ServiceOnline"
    
    # True or False
    status: True
    
    # PingFailed or PingSucceeded
    reason: PingSucceeded
    
    # Reply message or error message
    message: "{"data":{"base":"BTC","currency":"USD","amount":"58516.67"}}"
    
    lastUpdateTime: ""
    lastTransitionTime: ""
```

## Operator

The `CoinbasePinger` CRD and the operator are created using the Kubebuilder (https://book.kubebuilder.io/).

Whenever `CoinbasePinger` CR is created or updated, the operator validates the `spec` fields and a controller reconciles the state: it creates or updates a Kubernetes `CronJob` that runs every `spec.interval`. The application executed by the `CronJob` pings the Coinbase API endpoint: `https://api.coinbase.com/v2/{spec.endpoint}` and updates the `CoinbasePinger` CRD `status` according to a result.

When `CoinbasePinger` CR is deleted, the `CronJob` is deleted as well.

## Application

The application that is executed by the `CronJob` is implemented as a separate Go module and is built as a Docker image, that is used by the `CronJob`.

## Overall project structure

```
project-root:
  - operator/
      <kubebuilder operator here>
  - app/
      <Conbase API pinger application>
      Dockerfile
```

As a build tool, it is better to use Gradle. It should have the following task:
```bass
$ ./gradlew installLocal
```
which builds docker images, creates a KIND cluster (if not exists), loads images to a cluster and installs the project.

## Definition of done

- The project can be installed on a local KIND cluster using the command `$ ./gradlew installLocal`.
- The `CoinbasePinger` can be applied using `$ kubectl create -f ./operator/config/samples/dev.org_v1_coinbasepinger.yaml`.
- The `CoinbasePinger` shows the status and is updated at interval: `$ kubectl get coinbasepingers -o yaml -w`.
  




