# Exprimenting with Docker registry in Azure

## How do private endpoints work?
After adding private endpoints, you will see them but still:

```bash
az acr show-endpoints --name crgaraks77acr
{
  "dataEndpoints": [
    {
      "endpoint": "*.blob.core.windows.net",
      "region": "uksouth"
    },
    {
      "endpoint": "*.blob.core.windows.net",
      "region": "westeurope"
    }
  ],
  "loginServer": "crgaraks77acr.azurecr.io"
}
```

Afterwards, you can do

```bash
az acr update --name  crgaraks77acr --data-endpoint-enabled
az acr show-endpoints --name crgaraks77acr
}"dataEndpoints": [
     {
       "endpoint": "crgaraks77acr.uksouth.data.azurecr.io",
       "region": "uksouth"
     },
     {
       "endpoint": "crgaraks77acr.westeurope.data.azurecr.io",
       "region": "westeurope"
     }
   ],
   "loginServer": "crgaraks77acr.azurecr.io"
```

Now data endpoints are enabled.

How are they used? Is docker really using them? 
Not so far...

create `/etc/docker/daemon.json `
```json
{
"debug": true
}
```

Then you can 
https://docs.docker.com/config/daemon/#enable-debugging

```bash
sudo kill -SIGHUP $(pidof dockerd)
journalctl -u docker.service
```

and you will see
```
"Calling GET /info"
"Calling HEAD /_ping"
"Calling POST /v1.41/images/create?fromImage=crgaraks77acr.azurecr.io%2Fhello-world&tag=latest"
"hostDir: /etc/docker/certs.d/crgaraks77acr.azurecr.io"
"Trying to pull crgaraks77acr.azurecr.io/hello-world from https://crgaraks77acr.azurecr.io v2"
"Pulling ref from V2 registry: crgaraks77acr.azurecr.io/hello-world:latest"
"Adding content digest to lease" digest="sha256:f54a58bc1aac5ea1a25d796ae155dc228b3f0e11d046ae276b39c4bf2f13d8c4" lease="moby-image-sha256:feb5d9fea6a5e9606aa995e879d862b825965ba48de054caab5ef356dc6b3412" remote="crgaraks77acr.azurecr.io/hello-world:latest"
"Calling GET /events?since=1645084548&until=1645084578"
"Calling GET /containers/json?all=1"
"Calling GET /containers/json"
"Calling GET /containers/json"
"Calling GET /containers/json?all=1"
"Calling GET /images/json?all=0"
"Calling GET /containers/a31ab56ea4da979a31d4a8361b54f3a22c2b49c98f6c66692e68e94812dd27db/json"
"Calling GET /events?since=1645084578&until=1645084608"
"Calling GET /containers/json?all=1"
"Calling GET /images/json?all=0"
"Calling GET /containers/json?all=1"
"Calling GET /containers/a31ab56ea4da979a31d4a8361b54f3a22c2b49c98f6c66692e68e94812dd27db/json"
"Calling GET /containers/json"
```


so... so far data endpoint was not used :(

