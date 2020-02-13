---
title: BlockClock API
layout: customized
authoer: support@coinkite.com
---

# BlockClock API

## Setup

First you must create one or more API keys:

- go to "Data Sources"
- click on "Custom API Keys"
- click on "Create New API Key"
- make note of the key provided: `QFDJLA74EORGYZQIKMOWIYBM` for example

## Basics

- all api URL's start with `/api`
- only http (not https) is supported
- you must be on the same LAN as the BlockClock
- you must provide a current API key using basic Authentication header: `curl -u :APIKEY`
- most responses will include the full set of current values you have set previously
- all responses are JSON

Example:

```
% curl -u :QFDJLA74EORGYZQIKMOWIYBM "http://10.0.0.104/api/set" -d price=1234
{
  "api_values": {
    "price": 1234.0
  },
  "error": false
}
```

To make the above "price" visible, you must define a profile
with "api.price" as the data source.  You can extend that with an
expression and/or use the value in a red/green status indicator
field as well.

## Usage for Displaying Data

- Your program sends data to the BlockClock
- The clock stores it under the "api" prefix, and whatever varible name you want
- Values are forgotten at next reboot/reset.

There are multiple ways to upload data to the clock.

- set a single value, or a few values as form data (`application/x-www-form-urlencoded`):

```
% curl -u :QFDJLA74EORGYZQIKMOWIYBM "http://10.0.0.104/api/set" -d price=1234 -d ratio=34.44
```

- as query-string values:

```
% curl -u :QFDJLA74EORGYZQIKMOWIYBM "http://10.0.0.104/api/set?price=333&date=2018-01-19"
```

Things that look like numbers and dates will be promoted from string to the 
appropriate type. When you don't want this, use `/api/set/raw` in place of `/api/set`
and all values sent will be treated as strings.

You may also upload a JSON document. It's values will be converted and stored in bulk.

```bash
% cat test.json
{
    "abc": 123,
    "date": "2019-01-18T12",
    "datetime": "2019-01-18T12:34:56+05:00",
    "nested": {
        "are": "supported",
        "values": 123
    },
    "world": "hello"
}
% curl -u :QFDJLA74EORGYZQIKMOWIYBM "http://10.0.0.104/api/set" -T test.json 
{
  "api_values": {
    "abc": 123,
    "world": "hello",
    "date": "2019-01-18T12:00:00+00:00",
    "datetime": "2019-01-18T12:34:56+05:00",
    "nested.values": 123,
    "nested.are": "supported"
  },
  "error": false
}
```


## Read Display

You can get currently displayed values, and other status information, using:
    `/api/status`

For example:
```
% curl -u :QFDJLA74EORGYZQIKMOWIYBM "http://10.0.0.104/api/status"
{
  "muted": false,
  "active_page": 0,
  "tcp_addr": {
    "uap0": "10.3.141.1",
    "eth0": "10.0.0.104",
    "wlan": "10.69.0.10"
  },
  "display": {
    "rows": [
      "     133",
      "   hello",
      "19-01-18"
    ],
    "reds": [
      false,
      false,
      false
    ],
    "greens": [
      false,
      false,
      false
    ]
  }
}
```

## Read Snapshot Values

You can grab a copy of all the values the clock is tracking. This will include `api.*` values.

```
% curl -u :QFDJLA74EORGYZQIKMOWIYBM "http://10.0.0.104/api/snapshot"
{
  "datetime.local.time": "2019-01-18T13:50:48.002708-05:00",
  "datetime.utc.time": "2019-01-18T18:50:48.002944+00:00",
  "datetime.America_Caracas.time": "2019-01-18T14:50:48.003040-04:00",
  "datetime.local.date": "2019-01-18T13:50:48.003307-05:00",
  "datetime.utc.date": "2019-01-18T18:50:48.003503+00:00",
  "datetime.America_Caracas.date": "2019-01-18T14:50:48.003586-04:00",
  "datetime.genesis.btc": "2009-01-03T18:15:05+00:00",
  "temperature.celsius": "23.2\u00b0C",
  "temperature.degrees_c": 23.21875,
  "temperature.degrees_f": 73.79375,
  "temperature.fahrenheit": "73\u00b0F",
  "text.blank": "        ",
  "bitcoinaverage.ADAUSD.averages.day": 0.04417523,
  "bitcoinaverage.ADAUSD.timestamp": "2019-01-18T18:50:03+00:00",
  "bitcoinaverage.ADAUSD.bid": 0.043645077515610524
...
```

## Other functions over API

These endpoints perform immediate functions:

`/api/clear`

- wipe all api values stored

`/api/pause`

`/api/play`

`/api/toggle`

- control "mute" mode of the display

`/api/page/1`

`/api/page/2`

`/api/page/3`

`/api/page/4`

`/api/page/next`

- display a specific page number

`/api/page/reset`

`/api/page/reboot`

`/api/page/powerdown`

- perform indicated function after a 2 second delay

`/api/update`

- force immediate update of display

`/api/lamptest`

- simulate pressing LAMPTEST button on rear side

