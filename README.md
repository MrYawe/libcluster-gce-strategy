# Google Compute Engine Cluster Strategy

[![Hex.pm](https://img.shields.io/hexpm/v/libcluster_gce_strategy.svg)](https://hex.pm/packages/libcluster_gce_strategy)

This Cluster Strategy can be used with [libcluster](https://github.com/bitwalker/libcluster) to automatically connect nodes running on GCE instances within the same Instance Group.

It uses [GCP internal DNS](https://cloud.google.com/compute/docs/internal-dns).

You can follow the [GCP tutorial](https://cloud.google.com/community/tutorials/elixir-phoenix-on-google-compute-engine) for setting up an instance group for Elixir. Once up and running, this strategy uses the instance metadata HTTP endpoint and the Google Cloud API to discover other instances within the instance group and will automatically connect with any it finds.

## Usage

Add the `libcluster_gce_strategy` package to your dependencies:

```elixir
  defp deps do
    [
      {:libcluster_gce_strategy, "~> 0.1"}
    ]
  end
```

and add the Cluster Supervisor to your application's supervision tree.

```elixir
defmodule MyApp.Application do
  use Application
  require Logger

  def start(_type, _args) do
    topologies = [
      gce: [
        strategy: Cluster.Strategy.GoogleComputeEngine,
        config: [
          release_name: :myapp,
          gcp_region: "europe-west1-c",
          gcp_project_id: "MY_PROJECT_ID"
        ]
      ]
    ]

    children = [
      MyAppWeb.Endpoint,
      {Cluster.Supervisor, [topologies, [name: Ding.ClusterSupervisor]]}
    ]

    Supervisor.start_link(children, [strategy: :one_for_one, name: MyApp.Supervisor])
  end
end
```

## Notes

This implementation was heavily influenced by the [GAE Implementation](https://github.com/alexgaribay/libcluster_gae) by [@alexgaribay](https://github.com/alexgaribay).
