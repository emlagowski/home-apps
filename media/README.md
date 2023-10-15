# m8k - MediaStack on Kubernetes - Plex, Overseerr, Sonarr, Radarr, Bazarr, Prowlarr and Transmission over VPN.

Goal of this chart is to get complete Media Plex Stack as Helm chart, ready to deploy on you Kubernetes instance. Another objective is to get all configuration as a code, configurable from chart level, so after installation you do not need to go to every application and set it up manually. Below you can find listed configuration available to set from chart in each application.

## Architecture

High level architecture diagram.

![architecture-diagram](m8k-media-stack-architecture.drawio.svg)

| Interface | Description |
|-|-|
|IT1| Mounted library directory to access Movies and TV Series |
|IT2| x | 
|IT3| x | 
|IT4| x |
| ... | |

## Configuration

### Plex

| Feature | Status | How to set? | Description |
|-|-|-|-|
| Plex Account | :white_check_mark: | Set via `PLEX_CLAIM` environment variable, which is set from `plex.secret.claimTokenKey` secret value. | Binding between Plex instance and main Plex account. |

Configuration directory path under `plex.configPath` parameter.

### Sonarr

| Feature | Status | How to set? | Description |
|-|-|-|-|
| Base Url | :white_check_mark: | | Base Url for service used when exposed on context path behind reverse-proxy. |
| API key | :white_check_mark: | | API key used to integration via REST API. |
| Library directory  | :white_check_mark: | | Directory where TV Series will be read, renamed and organized by Sonarr. |
| Download client | :white_check_mark: | | Integration to show download status in Sonarr. |
| Indexer's | :white_check_mark: | | Indexer's to look for download link for selected TV Series. Indexer's are automatically configured by `Prowlarr`. |
| Quality Profiles | :x: | | Setting quality profiles for downloaded content. Currently defaults are used. |
| Import library | :x: | | Trigger auto-import library after clean reboot. |

Configuration directory path under `sonarr.configPath` parameter.

### Radarr

| Feature | Status | How to set? | Description |
|-|-|-|-|
| Base Url | :white_check_mark: | | Base Url for service used when exposed on context path behind reverse-proxy. |
| API key | :white_check_mark: | | API key used to integration via REST API. |
| Library directory  | :white_check_mark: | | Directory where TV Series will be read, renamed and organized by Radarr. |
| Download client | :white_check_mark: | | Integration to show download status in Radarr. |
| Indexer's | :white_check_mark: | | Indexer's to look for download link for selected TV Series. Indexer's are automatically configured by `Prowlarr`. |
| Quality Profiles | :x: | | Setting quality profiles for downloaded content. Currently defaults are used. |
| Import library | :x: | | Trigger auto-import library after clean reboot. |

Configuration directory path under `radarr.configPath` parameter.

### Overseerr

| Feature | Status | How to set? | Description |
|-|-|-|-|
| Plex integration | :white_check_mark: | | Integration with Plex to synchronize content library, users and watchlists. |
| API key | :white_check_mark: | | API key used to integration via REST API. |
| Import library | :white_check_mark: | | Set Plex libraries to scan and show in Overseerr. |
| Radarr integration | :white_check_mark: | | Integration with service to manage downloading Movies. |
| Radarr Quality Profiles | :x: | | Set which quality profile should be used when downloading content. |
| Sonarr integration | :white_check_mark: | | Integration with service to manage downloading TV Series. |
| Sonarr Quality Profiles | :x: | | Set which quality profile should be used when downloading content. |
| Sonarr / Radarr 4k | :x: | | Additional setting to enable 4k content download. |
| Lunasea mobile notifications| :white_check_mark: | | Enable notification on mobile device using Lunasea webhook. |
| Default user settings | :x: | | Configure default user settings (like auto request acceptance) that should be used for new users.  |

Configuration directory path under `overseerr.configPath` parameter.

### Prowalarr

| Feature | Status | How to set? | Description |
|-|-|-|-|
| Base Url | :white_check_mark: | | Base Url for service used when exposed on context path behind reverse-proxy. |
| API key | :white_check_mark: | | API key used to integration via REST API. |
| Download client | :white_check_mark: | | Enable possibility to search and download content from Prowlarr. |
| Radarr integration | :white_check_mark: | | Configure all Indexer's in Radarr, to use them for content searching. |
| Sonarr integration | :white_check_mark: | | Configure all Indexer's in Sonarr, to use them for content searching. |
| Indexer's | :white_check_mark: | | Define indexer's that should be used for content searching. |

Configuration directory path under `prowlarr.configPath` parameter.

### Bazarr

| Feature | Status | How to set? | Description |
|-|-|-|-|
| Base Url | :white_check_mark: | | Base Url for service used when exposed on context path behind reverse-proxy. |
| Radarr integration | :white_check_mark: | | |
| Sonarr integration | :white_check_mark: | | |
| Subtitle providers | :white_check_mark: | | Providers that will be used to search for subtitles for downloaded content. |
| Library directory  | :white_check_mark: | | Directory where Bazarr will look for new Movies and TV Series to download subtitles. |
| Default language profile | :x: | | Configure default profiles that will be used for all downloaded content to automate subtitles searching. |

### Transmission

| Feature | Status | How to set? | Description |
|-|-|-|-|
| Open VPN config | :white_check_mark: | | Configure VPN connection. |
| Open VPN country | :white_check_mark: | | Configure VPN Country. |
| Download directory | :white_check_mark: | | Directory used as temporary place for dowloaded content. |
| Library directory | :white_check_mark: | | Directory where successfully downloaded content will be moved. |

## Local development

Render chart locally

```sh
helm template --dry-run -f values.yaml . > local.yaml
```

## Secrets

This chart assume you will provide secret to your deployment. Those values won't be set from chart as it assume that it will be used as gitops and could be saved in repository.

-- todo

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

Encrypt local secret file:
```sh 
kubeseal --scope namespace-wide --namespace media --format=yaml < media/templates/secrets.yml > media/templates/sealed-secrets.yml
```

## Sources

Based on: https://greg.jeanmart.me/2020/04/13/self-host-your-media-center-on-kubernetes-wi/#Media-Server-Plex

todo ad more sources