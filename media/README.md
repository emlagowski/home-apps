# Media Stack on RaspberryPi - Plex, Sonarr, Radarr, Bazarr, Prowlarr and Transmission over VPN.

Based on: https://greg.jeanmart.me/2020/04/13/self-host-your-media-center-on-kubernetes-wi/#Media-Server-Plex

Render chart locally:
```
helm template --dry-run -f values.yaml . > local.yaml
```

Encrypt local secret file:
```
kubeseal --scope namespace-wide --namespace media --format=yaml < media/templates/secrets.yml > media/templates/sealed-secrets.yml
```

Secret:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: {{ .Values.vpn.secret.name }}
data:
  {{ .Values.vpn.secret.usernameKey }}: <base64_vpn_username>
  {{ .Values.vpn.secret.passwordKey }}: <base64_vpn_password>
  {{ .Values.plex.secret.claimTokenKey }}: <base64_plex_claim_token>
stringData:
  {{ .Values.vpn.secret.credentialsFile }}: |-
    <plain_text_vpn_username>
    <plain_text_vpn_password>
```