# OpenSearch Scripts

## Notes
- Configs can be read from both the `opensearch.yml` file, and from environment variables.
  - When the same variable is set in both places, the config file takes precedence.

## Copy config files
```sh
docker cp $(docker ps | grep opensearch | cut -d" " -f1):/usr/share/opensearch/config/<filename> ./<filename>
```

E.g.

```sh
docker cp $(docker ps | grep opensearch | cut -d" " -f1):/usr/share/opensearch/config/opensearch-security/config.yml ./opensearch-security/config.yml
```

## Quick setup

```sh
docker run \
  -p 9201:9200 -p 9600:9600 \
  -e "discovery.type=single-node" \
  -e "action.auto_create_index=false" \
  -e "plugins.security.allow_default_init_securityindex=false" \
  -e "plugins.security.disabled=true" \
  opensearchproject/opensearch:latest
```

## Disable default security index

```sh
docker run \
  -p 9201:9200 -p 9600:9600 \
  -e "discovery.type=single-node" \
  -e "action.auto_create_index=false" \
  -e "plugins.security.allow_default_init_securityindex=false" \
  opensearchproject/opensearch:latest
```

Error:
```
[ERROR][o.o.s.c.ConfigurationLoaderSecurity7] [747c16e70b90] Failure no such index [.opendistro_security] retrieving configuration for [INTERNALUSERS, ACTIONGROUPS, CONFIG, ROLES, ROLESMAPPING, TENANTS, NODESDN, WHITELIST, ALLOWLIST, AUDIT] (index=.opendistro_security)
```

## Launch with certs

```sh
docker run \
  -p 9201:9200 -p 9600:9600 \
  -e "discovery.type=single-node" \
  -e "action.auto_create_index=false" \
  -e "DISABLE_INSTALL_DEMO_CONFIG=true" \
  -e "plugins.security.disabled=true" \
  -v $(pwd):/usr/share/opensearch/config \
  opensearchproject/opensearch:latest
```

## Launch with security plugin

```sh
docker run \
  -p 9201:9200 -p 9600:9600 \
  -e "discovery.type=single-node" \
  -e "action.auto_create_index=false" \
  -e "DISABLE_INSTALL_DEMO_CONFIG=true" \
  -v $(pwd):/usr/share/opensearch/config \
  opensearchproject/opensearch:latest
```

Error:

```
[ERROR][o.o.s.c.ConfigurationLoaderSecurity7] [node-1] Failure no such index [.opendistro_security] retrieving configuration for [INTERNALUSERS, ACTIONGROUPS, CONFIG, ROLES, ROLESMAPPING, TENANTS, NODESDN, WHITELIST, ALLOWLIST, AUDIT] (index=.opendistro_security)
```

## Experiments

Combination of `action.auto_create_index` and `plugins.security.allow_default_init_securityindex`:

| | `init_securityindex: true` | `init_securityindex: false` |
| --- | --- | --- |
| **`auto_create_index: true`**  | ✅ | ❌ `Failure no such index` |
| **`auto_create_index: false`** | ✅ | ❌ `Failure no such index` |

So the key variable is `allow_default_init_securityindex`, not `auto_create_index`.

```sh
docker run \
  -p 9201:9200 -p 9600:9600 \
  -e "discovery.type=single-node" \
  -e "DISABLE_INSTALL_DEMO_CONFIG=true" \
  -e "action.auto_create_index=<value>" \
  -e "plugins.security.allow_default_init_securityindex=<value>" \
  -v $(pwd):/usr/share/opensearch/config \
  opensearchproject/opensearch:latest
```
