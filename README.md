<a href="https://typesense.org"><img src="https://typesense.org/assets/images/typesense_logo.svg" alt="Typesense" width="298" /></a> 

[![CircleCI](https://circleci.com/gh/typesense/typesense.svg?style=shield&circle-token=1addd775339738a3d90869ddd8201110d561feaa)](https://circleci.com/gh/typesense/typesense)
![Docker Pulls](https://img.shields.io/docker/pulls/typesense/typesense)

Typesense is a fast, typo-tolerant search engine for building delightful search experiences.

<img src="assets/typesense_books_demo.gif?raw=true" alt="Typesense Demo" width="459" />

## Quick Links

- [Features](#features)
- [Install](#install)
- [Quick Start](#quick-start)
- [Step-by-step Walk-through](#step-by-step-walk-through)
- [API Documentation](#api-documentation)
- [API Clients](#api-clients)
- [Search UI Components](#search-ui-components)
- [Benchmarks](#benchmarks)
- [FAQ](#faq)
- [Support](#support)
- [Getting Latest Updates](#getting-latest-updates)
- [Build from Source](#build-from-source)

## Features

- **Typo Tolerance:** Handles typographical errors elegantly, out-of-the-box.
- **Simple and Delightful:** Simple to set-up, integrate with, operate and scale.
- **Blazing Fast:** Meticulously architected from the ground-up for low-latency instant searches.
- **Tunable Ranking:** Easy to tailor your search results to perfection.
- **Faceting & Filtering:** Drill down and refine results.
- **Grouping & Distinct:** Group similar results together to show more variety of results.
- **Scoped API Keys:** Generate API keys that only allow access to certain records.
- **Curation & Merchandizing:** Pin particular records to a fixed position in search results, to highlight them.
- **Raft-based Clustering:** Setup a distributed cluster that is highly available.

## Install

**Option 1:** You can download the [binary packages](https://typesense.org/downloads) that we publish for 
Linux (x86-64) and Mac.

**Option 2:** You can also run Typesense from our [official Docker image](https://hub.docker.com/r/typesense/typesense).

**Option 3:** Spin up a managed cluster with [Typesense Cloud](https://cloud.typesense.org).

## Quick Start

Here's a quick example showcasing how you can create a collection, index a document and search it on Typesense.
 
Let's begin by starting the Typesense server via Docker:

```
docker run -p 8108:8108 -v/tmp/data:/data typesense/typesense:0.15.0 --data-dir /data --api-key=Hu52dwsas2AdxdE
```

We have [API Clients](#api-clients) in a couple of languages, but let's use the Python client for this example.

Install the Python client for Typesense:
 
```
pip install typesense
```

We can now initialize the client and create a `companies` collection:

```python
import typesense

client = typesense.Client({
  'nodes': [{
    'host': 'localhost',
    'port': '8108',
    'protocol': 'http',
    'api_key': 'Hu52dwsas2AdxdE'
  }],
  'connection_timeout_seconds': 2
})

create_response = client.collections.create({
  "name": "companies",
  "fields": [
    {"name": "company_name", "type": "string" },
    {"name": "num_employees", "type": "int32" },
    {"name": "country", "type": "string", "facet": True }
  ],
  "default_sorting_field": "num_employees"
})
```

Now, let's add a document to the collection we just created:

```python
document = {
 "id": "124",
 "company_name": "Stark Industries",
 "num_employees": 5215,
 "country": "USA"
}

client.collections['companies'].documents.create(document)
```

Finally, let's search for the document we just indexed:

```python
search_parameters = {
  'q'         : 'stork',
  'query_by'  : 'company_name',
  'filter_by' : 'num_employees:>100',
  'sort_by'   : 'num_employees:desc'
}

client.collections['companies'].documents.search(search_parameters)
```

**Did you notice the typo in the query text?** No big deal. Typesense handles typographic errors out-of-the-box!

## Step-by-step Walk-through

A step-by-step walk-through is available on our website [here](https://typesense.org/guide). 

This will guide you through the process of starting up a Typesense server, indexing data in it and querying the data set. 

## API Documentation

Here's our official API documentation, available on our website: [https://typesense.org/api](https://typesense.org/api).

If you notice any issues with the documentation or walk-through, please let us know or send us a PR here: [https://github.com/typesense/typesense-website](https://github.com/typesense/typesense-website).

## API Clients

While you can definitely use CURL to interact with Typesense Server directly, we offer official API clients to simplify using Typesense from your language of choice. The API Clients come built-in with a smart retry strategy to ensure that API calls made via them are resilient, especially in an HA setup.

- [typesense-js](https://github.com/typesense/typesense-js)
- [typesense-php](https://github.com/typesense/typesense-php)
- [typesense-python](https://github.com/typesense/typesense-python)
- [typesense-ruby](https://github.com/typesense/typesense-ruby)

If we don't offer an API client in your language, you can still use any popular HTTP client library to access Typesense's APIs directly. 

We welcome community contributions to add more official client libraries. Please reach out to us at contact@typsense.org or open an issue on Github to collaborate with us on the architecture. 🙏

## Search UI Components

You can use our [InstantSearch.js adapter](https://github.com/typesense/typesense-instantsearch-adapter) 
to quickly build powerful search experiences, complete with filtering, sorting, pagination and more.

Here's how: [https://typesense.org/docs/0.15.0/guide/#search-ui](https://typesense.org/docs/0.15.0/guide/#search-ui) 

## FAQ

### How does this differ from Elasticsearch?

Elasticsearch is a large piece of software, that takes non-trivial amount of effort to setup, administer, scale and fine-tune. 
It offers you a few thousand configuration parameters to get to your ideal configuration. So it's better suited for large teams 
who have the bandwidth to get it production-ready, regularly monitor it and scale it, especially when they have a need to store 
billions of documents and petabytes of data (eg: logs).

Typesense is built specifically for decreasing the "time to market" for a delightful search experience. It is a light-weight
yet powerful & scaleable alternative that focuses on Developer Happiness and Experience with a clean well-documented API, clear semantics 
and smart defaults so it just works well out-of-the-box, without you having to turn many knobs.

Elasticsearch also runs on the JVM, which by itself can be quite an effort to tune to run optimally. Typesense, on the other hand, 
is a single light-weight self-contained native binary, so it's simple to setup and operate.

### How does this differ from Algolia?

Algolia is a proprietary, hosted, search-as-a-service product that works well, when cost is not an issue. From our experience,
fast growing sites and apps quickly run into search & indexing limits, accompanied by expensive plan upgrades as they scale.

Typesense on the other hand is an open-source product that you can run on your own infrastructure or
use our managed SaaS offering - [Typesense Cloud](https://cloud.typesense.org). 
The open source version is free to use (besides of course your own infra costs). 
With Typesense Cloud we do not charge by records or search operations. Instead, you get a dedicated cluster
and you can throw as much data and traffic at it as it can handle. You only pay a fixed hourly cost & bandwidth charges 
for it, depending on the configuration your choose, similar to most modern cloud platforms. 

From a product perspective, Typesense is closer in spirit to Algolia than Elasticsearch. 
However, we've addressed some important limitations with Algolia: 

Algolia requires separate indices for each sort order, which counts towards your plan limits. Most of the index settings like 
fields to search, fields to facet, fields to group by, ranking settings, etc 
are defined upfront when the index is created vs being able to set them on the fly at query time.

With Typesense, these settings can be configured at search time via query parameters which makes it very flexible
and unlocks new use cases. Typesense is also able to give you sorted results with a single index, vs having to create multiple.
This helps reduce memory consumption.

Algolia offers the following features that Typesense does not have currently: 
synonyms, geo spatial searches, personalization & server-based search analytics.
With Typesense, we intend to bridge this gap, but in the meantime, please let us know
if any of these are a show stopper for your use case by creating a feature request in our issue tracker. 

### Speed is great, but what about the memory footprint?

A fresh Typesense server will consume about 30 MB of memory. As you start indexing documents, the memory use will 
increase correspondingly. How much it increases depends on the number and type of fields you index. 

We've strived to keep the in-memory data structures lean. To give you a rough idea: when 1 million 
Hacker News titles are indexed along with their points, Typesense consumes 165 MB of memory. The same size of that data 
on disk in JSON format is 88 MB. If you have any numbers from your own datasets that we can add to this section, please send us a PR!

## Benchmarks

We tested a dataset with ~3 million records (Amazon product data) that was ~13GB on disk and we were able to achieve a throughput of **250 concurrent search queries per second** on a 16GB, 8-vCPU 3-node Typesense cluster. 

We'd love to benchmark with larger datasets, if we can find large ones in the public domain. If you have any suggestions for structured datasets that are open, please let us know by opening an issue. 

We'd also be delighted if you're able to share benchmarks from your own large datasets. Please send us a PR! 

## Support

If you have any questions or run into any problems, please create a Github issue and we'll try our best to help.

We strive to provide good support through our issue trackers on Github. However, if you'd like to receive private & prioritized support with:

- Guaranteed SLAs
- Phone / video calls to discuss your specific use case and get recommendations on best practices
- Private discussions over Slack
- Guidance around deployment, ops and scaling best practices

We do offer Paid Support options. Please reach out to us at contact@typesense.org to sign up.

## Getting Latest Updates

If you'd like to get updates when we release new versions, click on the "Watch" button on the top and select "Releases only". Github will then send you notifications along with a changelog with each new release.

We also post updates to our Twitter account about releases and additional topics related to Typesense. Follow us here: [@typesense](https://twitter.com/typesense).

## Build from source

**Building with Docker**

The docker build script takes care of all required dependencies, so it's the easiest way to build Typesense:

```
TYPESENSE_VERSION=nightly ./docker-build.sh --build-deploy-image --create-binary [--clean] [--depclean]
```

**Building on your machine**

Typesense requires the following dependencies: 

* C++11 compatible compiler (GCC >= 4.9.0, Apple Clang >= 8.0, Clang >= 3.9.0)
* Snappy
* zlib
* OpenSSL (>=1.0.2)
* curl
* ICU
* brpc
* braft

```
./build.sh --create-binary [--clean] [--depclean]
```

The first build will take some time since other third-party libraries are pulled and built as part of the build process.

---
&copy; 2016-2020 Typesense Inc.
