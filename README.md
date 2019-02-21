moov-io/ofac
===

[![GoDoc](https://godoc.org/github.com/moov-io/ofac?status.svg)](https://godoc.org/github.com/moov-io/ofac)
[![Build Status](https://travis-ci.com/moov-io/ofac.svg?branch=master)](https://travis-ci.com/moov-io/ofac)
[![Coverage Status](https://codecov.io/gh/moov-io/ofac/branch/master/graph/badge.svg)](https://codecov.io/gh/moov-io/ofac)
[![Go Report Card](https://goreportcard.com/badge/github.com/moov-io/ofac)](https://goreportcard.com/report/github.com/moov-io/ofac)
[![Apache 2 licensed](https://img.shields.io/badge/license-Apache2-blue.svg)](https://raw.githubusercontent.com/moov-io/ofac/master/LICENSE)

Office of Foreign Asset Control (OFAC) is an HTTP API and Go library to download, [parse and serve United States OFAC sanction data](https://docs.moov.io/en/latest/ofac/file-structure/) for applications and humans. Also supported is an async webhook notification service to initiate processes on remote systems connected with OFAC.

To get started using OFAC download [the latest release](https://github.com/moov-io/ofac/releases) or our [Docker image](https://hub.docker.com/r/moov/ofac/tags).

```
$ docker run -p 8084:8084 -p 9094:9094 -it moov/ofac:v0.5.0
ts=2019-02-05T00:03:31.9583844Z caller=main.go:42 startup="Starting ofac server version v0.5.0"
...

$ curl -s localhost:8084/search?name=...
{
  "SDNs": [
    {
      "entityID": "...",
      "sdnName": "...",
      "sdnType": "...",
      "program": "...",
      "title": "...",
      "callSign": "...",
      "vesselType": "...",
      "tonnage": "...",
      "grossRegisteredTonnage": "...",
      "vesselFlag": "...",
      "vesselOwner": "...",
      "remarks": "..."
    }
  ],
  "altNames": null,
  "addresses": null
}
```

We offer [hosted api docs as part of Moov's tools](https://api.moov.io/#tag/OFAC) and an [OpenAPI specification](https://github.com/moov-io/ofac/blob/master/openapi.yaml) for use with generated clients.

### Configuration

| Environmental Variable | Description | Default |
|-----|-----|-----|
| `OFAC_DATA_REFRESH` | Interval for OFAC data redownload and reparse. | 12h |
| `OFAC_DOWNLOAD_TEMPLATE` | HTTP address for downloading raw OFAC files. | (OFAC website) |
| `SQLITE_DB_PATH`| Local filepath location for the paygate SQLite database. | `ofac.db` |
| `WEBHOOK_BATCH_SIZE` | How many watches to read from database per batch of async searches. | 100 |

### Features

- Download OFAC data on startup
  - Admin endpoint to [manually refresh OFAC data](docs/runbook.md#force-ofac-data-refresh)
- Index data for searches
- Async searches and notifications (webhooks)
- Manual overrides to mark a `Company` or `Customer` as `unsafe` (blocked) or `exception` (never blocked).
- Library to download and parse OFAC files

#### Webhook Notifications

When OFAC sends a [webhook](https://en.wikipedia.org/wiki/Webhook) to your application the body will contain a JSON representation of the [Company](https://godoc.org/github.com/moov-io/ofac/client#Company) or [Customer](https://godoc.org/github.com/moov-io/ofac/client#Customer) model as the body to a POST request. You can see an [example in Go](examples/webhook/webhook.go).

An `Authorization` header will also be sent with the `authToken` provided when setting up the watch. Clients should verify this token to ensure authenticated communicated.

## Links

- [Sanctions Search Page](https://sanctionssearch.ofac.treas.gov/)
- [Subscribe for OFAC updates](https://service.govdelivery.com/accounts/USTREAS/subscriber/new)
