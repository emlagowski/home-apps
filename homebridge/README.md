# Homebridge

## Sealing secrets

Create secrets.yml file

run 

```sh
kubeseal --scope namespace-wide --namespace homebridge --format=yaml < homebridge/secrets.yml > homebridge/sealed-secrets.yml
```