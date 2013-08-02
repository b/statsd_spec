StatsD Metrics Export Specification v0.1
========================================

Edited by Benjamin Black, <b@b3k.us>

This document describes current practice for the implementation of the various incarnations of the StatsD metric collection protocol.  The protocol originated at Flickr, was further developed at Etsy, and has been subsequently influenced by Coda Hale's Metrics.  The intent of this document is not to specify or enforce a standard, but only to act as a snapshot of current practice and a guide to ease implementation.


Terminology
===========

StatsD is used to collect metrics from infrastructure.  It is push-based: clients export metrics to a collection server, which in turn derives aggregate metrics and often drives graphing systems such as Graphite.  Few assumptions are made about how the data is processed or exposed.

The terms metrics and infrastructure are both defined broadly.  A metric is a measurement composed of a name, a value, a type, and sometimes additional information describing how a metric should be interpreted.  Infrastructure is any part of a technology stack, from datacenter UPS controllers and temperature sensors in servers all the way up to function calls in applications and user interactions in a browser.

If it can be structured as one of the metric types below, it can consumed by StatsD.


Metric Types & Formats
======================

The format of exported metrics is UTF-8 text, with metrics separated by newlines.  Metrics are generally of the form `<metric name>:<value>|<type>`, with exceptions noted in the metric type definitions below.

The protocol allows for both integer and floating point values. Most implementations store values internally as a IEEE 754 double precision float, but many implementations and graphing systems only support integer values. For compatibility all values should be integers in the range (-2^53^, 2^53^).

Gauges
------

A gauge is an instantaneous measurement of a value, like the gas gauge in a car.  It differs from a counter by being calculated at the client rather than the server.  Valid gauge values are in the range [0, 2^64^)

	<metric name>:<value>|g

Counters
--------

A counter is a gauge calculated at the server.  Metrics sent by the client increment or decrement the value of the gauge rather than giving its current value.  Counters may also have an associated sample rate, given as a decimal of the number of samples per event count.  For example, a sample rate of 1/10 would be exported as 0.1. Valid counter values are in the range (-2^63^, 2^63^).

	<metric name>:<value>|c[|@<sample rate>]

Timers
------

A timer is a measure of the number of milliseconds elapsed between a start and end time, for example the time to complete rendering of a web page for a user.  Valid timer values are in the range [0, 2^64^).

	<metric name>:<value>|ms

Histograms
----------

A histogram is a measure of the distribution of timer values over time, calculated at the server.  As the data exported for timers and histograms is the same, this is currently an alias for a timer.  Valid histogram values are in the range [0, 2^64^).

	<metric name>:<value>|h

Meters
------

A meter measures the rate of events over time, calculated at the server.  They may also be thought of as increment-only counters.  Valid meter values are in the range [0, 2^64^).

	<metric name>:<value>|m

In at least one implementation, this is abbreviated for the common case of incrementing the meter by 1.

	<metric name>

While this is convenient, the full, explicit metric form should be used.  The shortened form is documented here for completeness.


References
==========

* [Flickr StatsD](http://code.flickr.com/blog/2008/10/27/counting-timing/)
* [Etsy StatsD](https://github.com/etsy/statsd)
* [Coda Hale's Metrics](http://metrics.codahale.com/)
* [metricsd](https://github.com/mojodna/metricsd)
* [Graphite](http://graphite.wikidot.com/)

