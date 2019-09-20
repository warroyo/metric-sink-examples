# Prometheus Forwarder

## Description
This example sets up a cluster metric sink(CMS) and metric sink(MS). This will setup the MS to capture prometheus annotated pods in the namespace and forward those metrics to the CMS. The CMS will then expose all aggregated metrics on a single prometheus `/metrics` enpoint. This makes it use the built in discovery to aggregate metrics that can be scraped by an external prometheus instance.

**NOTE: this has not been tested at scale and may require tuning of the cluster size and/or Cluster Metric Sink Daemonset.**

## How It Works
This works by setting up a CMS that has a [influxdb_listener](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/influxdb_listener) input. The MS in the namespace then can have a [influxdb](https://github.com/influxdata/telegraf/tree/master/plugins/outputs/influxdb) output that send metric to the listener. It also requires a `service`  to be set up on the CMS so that the MS can use k8s dns to resolve the listener. The last piece is to enable a [prometheus](https://github.com/influxdata/telegraf/tree/master/plugins/outputs/prometheus_client) output on the CMS.

## Install

1. install the cluster metric sink and service

```
kubectl apply -f cms.yml
```

2. install the metric sink into the default namepsace

```
kubectl apply -f ms.yml
```

3. run the example app pod and expose it on a nodeport

```
kubectl apply -f app.yml

4. visit the app on the node port that was exposed on `/metrics` the counter will increase as you refresh

5. visit the prometheus client running on the worker node on port `9978` and you should see the metrics from the example app. this can now be scraped by any prometheus instance.

## Adding in PCF Reliability view
