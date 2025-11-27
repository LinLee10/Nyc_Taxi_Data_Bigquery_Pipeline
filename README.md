<h1 align="center">NYC Taxi Data BigQuery Pipeline</h1>

<p align="center">
  <b>Domain</b> urban mobility analytics • <b>Stack</b> Google Cloud Storage BigQuery Dataform Cloud Shell GitHub • <b>Goal</b> turn raw taxi files into reliable daily analytics marts
</p>

## Overview

This project converts raw NYC taxi files into decision ready tables on Google Cloud. It reads parquet and csv from Cloud Storage as external tables for instant exploration, snapshots them into native BigQuery tables for stability, shapes clean trip and zone datasets in a silver layer, and publishes compact daily marts in a gold layer. Dataform coordinates dependencies so every build is reproducible and easy to audit.

## What the pipeline does

### Data sources

<ul>
<li><b>Yellow trips</b> and <b>Green trips</b> in parquet</li>
<li><b>Taxi zones</b> in csv with location id borough zone and service zone</li>
</ul>

### Layered modeling

<ul>
<li><b>Bronze external</b> mounts Cloud Storage paths as BigQuery external tables for fast and low cost reads</li>
<li><b>Bronze native</b> copies those externals into native tables to lock schema and performance for downstream work</li>
<li><b>Silver</b> standardizes trip fields and types so yellow and green align one to one</li>
<li><b>Gold</b> provides small focused daily tables that answer common questions without joins</li>
</ul>

### Silver models

<ul>
<li><code>silver_yellow_trips</code> and <code>silver_green_trips</code> with typed columns  
vendor_id pickup_datetime dropoff_datetime pickup_zone_id dropoff_zone_id fare_amount</li>
<li><code>dim_zones</code> with location_id borough zone service_zone</li>
</ul>

### Gold marts

<ul>
<li><code>daily_trip_facts</code> trips and total fare by service type and date</li>
<li><code>daily_zone_flow</code> trips by pickup and dropoff zone pair by date</li>
<li><code>daily_vendor_metrics</code> trips and total fare by vendor by date</li>
<li><code>daily_quality_snapshot</code> side by side daily counts for yellow and green</li>
</ul>

### Built in safeguards

<ul>
<li><b>Row count checks</b> ensure native bronze tables are not empty</li>
<li><b>Schema checks</b> confirm required columns exist in the silver trip models</li>
<li><b>Timestamp checks</b> flag null pickup or dropoff times before they reach gold</li>
</ul>

## How an analyst uses the outputs

<ul>
<li>Track daily trip volume and fare by service type without writing joins</li>
<li>See movement between zones through daily origin destination pairs</li>
<li>Compare vendor activity by day for basic performance views</li>
<li>Monitor coverage with a quick daily snapshot of yellow and green counts</li>
</ul>

## Design choices that shape the system

<ul>
<li><b>External then native</b> pairs speed of access with downstream stability</li>
<li><b>Typed silver contracts</b> make queries predictable and fast</li>
<li><b>Small gold tables</b> keep results simple auditable and presentation ready</li>
<li><b>Quality as a feature</b> with assertions that fail early when assumptions break</li>
</ul>
::contentReference[oaicite:0]{index=0}
