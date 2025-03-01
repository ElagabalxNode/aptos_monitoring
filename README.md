# Grafana Dashboard for Monitoring APTOS Validator Operations

This is a work-in-progress Grafana dashboard for monitoring Aptos Validators. Setting up Prometheus/Grafana is outside the scope of this repo.

This repository is a fusion of two ideas!
Grafana dashboard from the RhinoStake team and a modified docker-compose file of jiangydev.

## A few assumptions and instructions for setup:

- This is in-progress and will have significant updates in 2022 as I (and other operators) better understand the important metrics and alerts for proper node operations.
- This requires a Prometheus data source (obviously), pulling data from the Aptos node's metric port at `9101`
- This also requires the [Infinity Plugin](https://grafana.com/grafana/plugins/yesoreyeram-infinity-datasource/) (for pulling API/JSON information). Install this plugin by executing `grafana-cli plugins install yesoreyeram-infinity-datasource` on the grafana server, or you can install for [grafana.net cloud installs](https://grafana.com/grafana/plugins/yesoreyeram-infinity-datasource/?tab=installation).
- After you install the Infinity Plugin, go to your Datasources and add a new Datasource. Choose type Infinity and hit save. This requires no other configuration.
- Ensure that node-exporter is installed on your Validator to pull machine metrics: `apt install prometheus-node-exporter`.
- This dashboard assumes you have a `chain` tag that is used in the prometheus metrics capture. I use this to flip between testnet/mainnet monitoring. This dashboard assumes you have this `chain` label set and will not show data without it. Setup your `prometheus.yml` file like this:

```yaml
global:
  scrape_interval:     120s # By default, scrape targets every 15 seconds.
  evaluation_interval: 120s # By default, scrape targets every 15 seconds.

  # Attach these labels to any time series or alerts when communicating with
  # external systems (federation, remote storage, Alertmanager).
  external_labels:
    monitor: 'aptos-node-monitor'

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  - job_name: 'aptos'
    scrape_interval: 120s
    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.
    static_configs:
      - targets: ['validator:9101','node-exporter:9100']
        labels:
          chain: 'mainnet'
          instance: 'validator'
      - targets: ['VFN.SERVER.IP:9101','VFN.SERVER.IP:9100']
        labels:
          chain: 'mainnet'
          instance: 'VFN'    

# 9100 is prometheus-node-exporter, 9101 is the default APTOS metrics port.  Ensure these are available from the prometheus server
# Add both your Validator and Validator Full Node IP addresses.  No need for additional tags.
```

**Note:** These exact prometheus chain labels (`llt` and `mainnet`) are used in variables in the dashboard for pulling metrics from the API. If you choose NOT use these chain names in your `prometheus.yml` file, you will need to change these names in the Variables section within the dashboard itself.

### Additional Functionality

- Dashboard can determine validator vs. full-node metrics from data, no need to tag
- PeerID is determined in variable from data
- If you utilize annotations, use tag `restart` and annotation will occur on all graphs

## How to use this file

- Download the `.json` file from the Grafana folder in this repository
- Hit Import in Grafana

![grafana import](https://grabup.teamhim.com/unalimentative-winterage-lucently-pharyngotonsillitis.png?raw=true)

- Click Upload JSON and select the file downloaded
- Select your Prometheus datasource from the dropdown & import!

![grafana import](https://grabup.teamhim.com/tabescence-jamshid-tiou-stinkier.png?raw-true)

Have ideas/changes/additions? Great! Feel free to push a PR to this repo or reach out to [me on Discord](https://discord.gg/SGhQzj5tyz)!

![aptos monitoring dashboard](https://grabup.teamhim.com/palaeogaea-calycozoic-liquidatorship-crosshatcher.png?raw=true)
