# Wazuh (certs-tool)

Wazuh certs tool has been used to create certificates, but any other certificates creation method, for example using OpenSSL, can be used.

wazuh.com

<img src="https://upload.wikimedia.org/wikipedia/commons/c/c3/Wazuh-Logo-2022.png?20230817165159" width="60%" height="auto" alt="wazuh logo">

## How to use this Makejail

### Deploy using appjail-director

**.env**:

```
DIRECTOR_PROJECT=wazuh-certs-tool
```

**appjail-director.yml**:

```yaml
options:
  - virtualnet: ':<random> default'
  - nat:
services:
  generator:
    name: wazuh-certs-generator
    volumes:
      - ssl-certs: /certificates
      - config: /config
volumes:
  ssl-certs:
    device: user-files/certs
  config:
    device: user-files/config
```

**user-files/config/certs.yml**:

```yaml
nodes:
  server:
    - name: wazuh-1
      ip: "10.0.0.80"
  indexer:
    - name: node-1
      ip: "10.0.0.81"
  dashboard:
    - name: dashboard
      ip: "10.0.0.82"
```

**Console**:

```console
# appjail-director up
Starting Director (project:wazuh-certs-generator) ...
Stopping generator (wazuh-certs-generator) ... Done.
Destroying generator (wazuh-certs-generator) ... Done.
Creating generator (wazuh-certs-generator) ... Done.
Finished: wazuh-certs-generator
# appjail-director info
wazuh-certs-generator:
 state: DONE
 last log: /root/.director/logs/2025-12-28_13h14m18s
 locked: false
 services:
  + generator (wazuh-certs-generator)
# ls -l user-files/certs/*
-r--------  1 855 855 1704 28 dic.  13:16 user-files/certs/admin-key.pem
-r--------  1 855 855 1220 28 dic.  13:16 user-files/certs/admin.pem
-r--------  1 309 309 1704 28 dic.  13:16 user-files/certs/root-ca-manager.key
-r--------  1 309 309 1204 28 dic.  13:16 user-files/certs/root-ca-manager.pem
-r--------  1 855 855 1704 28 dic.  13:16 user-files/certs/root-ca.key
-r--------  1 855 855 1204 28 dic.  13:16 user-files/certs/root-ca.pem
-r--------  1 309 309 1704 28 dic.  13:16 user-files/certs/wazuh-key.pem
-r--------  1 309 309 1273 28 dic.  13:16 user-files/certs/wazuh.pem
# appjail-director down -d
Starting Director (project:wazuh-certs-generator) ...
Stopping generator (wazuh-certs-generator) ... Done.
Destroying generator (wazuh-certs-generator) ... Done.
Destroying wazuh-certs-generator ... Done.
```

As you may have noticed, the mode, UID, and GID have already been modified. `855` corresponds to opensearch and `309` to wazuh. Check the UIDs and GIDs in your port tree for more details.

## Notes

1. The ideas present in the [Docker image of Wazuh](https://github.com/wazuh/wazuh-docker) are taken into account for users who are familiar with it.
