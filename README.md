# Coinbase pinger

Coinbase pinger is an application that periodically pings the Coinbase API ( https://api.coinbase.com/v2/prices/BTC-USD/buy ) and updates a status of a custom Kubernetes resource according to a result.

The Kubernetes Custom Resource Definition `CoinbasePinger` has the following schema:

```yaml
apiVersion: dev.org/v1
kind: CoinbasePinger

spec:
  endpoint: "v2/prices/BTC-USD/buy"
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

## Controllers

The `CoinbasePinger` CRD and operators are created using the Kubebuilder (https://book.kubebuilder.io/).

There are two operators: `coinbase-pinger-manager` and `coinbase-pinger`. The `coinbase-pinger-manager` contains the `CoinbasePinger` CRD and a validation webhook for it.
Its responsibility is to validate the `CoinbasePinger` resource and reconcile the state.




