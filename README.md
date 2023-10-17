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

## Results
- `plugins.security.allow_default_init_securityindex`
- `action.auto_create_index`

- Both `true`
  - Success
- `allow_default_init_securityindex=false`
  - `Failure no such index`
- `auto_create_index=false
  - Success
- Both `false`
  - `Failure no such index`

## With auto index creation

```sh
docker run \
  -p 9201:9200 -p 9600:9600 \
  -e "discovery.type=single-node" \
  -e "DISABLE_INSTALL_DEMO_CONFIG=true" \
  -e "action.auto_create_index=true" \
  -e "plugins.security.system_indices.enabled=true" \
  -v $(pwd):/usr/share/opensearch/config \
  opensearchproject/opensearch:latest
```

  -e "plugins.security.allow_default_init_securityindex=true" \