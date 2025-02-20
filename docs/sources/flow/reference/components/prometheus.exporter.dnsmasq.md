---
title: prometheus.exporter.dnsmasq
---

# prometheus.exporter.dnsmasq
The `prometheus.exporter.dnsmasq` component embeds
[dnsmasq_exporter](https://github.com/google/dnsmasq_exporter) for collecting statistics from a dnsmasq server.

## Usage

```river
prometheus.exporter.dnsmasq "LABEL" {
}
```

## Arguments
The following arguments can be used to configure the exporter's behavior.
All arguments are optional. Omitted fields take their default values.

Name          | Type     | Description                          | Default                          | Required
------------- | -------- | ------------------------------------ | -------------------------------- | --------
`address`     | `string` | The address of the dnsmasq server.   | `"localhost:53"`                 | no
`leases_file` | `string` | The path to the dnsmasq leases file. | `"/var/lib/misc/dnsmasq.leases"` | no

## Exported fields
The following fields are exported and can be referenced by other components.

Name      | Type                | Description
--------- | ------------------- | -----------
`targets` | `list(map(string))` | The targets that can be used to collect `dnsmasq` metrics.

For example, the `targets` can either be passed to a `prometheus.relabel`
component to rewrite the metric's label set, or to a `prometheus.scrape`
component that collects the exposed metrics.

The exported targets will use the configured [in-memory traffic][] address
specified by the [run command][].

[in-memory traffic]: {{< relref "../../concepts/component_controller.md#in-memory-traffic" >}}
[run command]: {{< relref "../cli/run.md" >}}

## Component health

`prometheus.exporter.dnsmasq` is only reported as unhealthy if given
an invalid configuration. In those cases, exported fields retain their last
healthy values.

## Debug information

`prometheus.exporter.dnsmasq` does not expose any component-specific
debug information.

## Debug metrics

`prometheus.exporter.dnsmasq` does not expose any component-specific
debug metrics.

## Example

This example uses a [`prometheus.scrape` component][scrape] to collect metrics
from `prometheus.exporter.dnsmasq`:

```river
prometheus.exporter.dnsmasq "example" {
  address     = "localhost:53"
}

// Configure a prometheus.scrape component to collect github metrics.
prometheus.scrape "demo" {
  targets    = prometheus.exporter.dnsmasq.example.targets
  forward_to = [ prometheus.remote_write.example.receiver ]
}

prometheus.remote_write "example" {
  endpoint {
    url = "http://mimir:9090/api/v1/write"

    basic_auth {
      username = "sample-username"
      password = "sample-password"
    }
  }
}
```

[scrape]: {{< relref "./prometheus.scrape.md" >}}
