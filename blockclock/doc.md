---
title: BlockClock Quick Start Guide
layout: customized
author: support@coinkite.com
---

# Quick Start Guide


This guide will get you started with initial setup and installation.

* TOC
{:toc}

# Power Up and Network Access

Start by connecting the power supply provided to AC power. The DC connector
is in the middle rear of the clock.

This product requires access to the Internet in order to display
current information. You have the option of WiFi or wired Ethernet.

Wired ethernet is the easiest and more reliable option, and setup consists
of merely connecting a CAT5 cable to the rear of the clock. If your installation
can support wired ethernet, please connect such a cable at this time.

## Getting Connected

When first powered, the clock does not do anything for about 30 seconds
while the system boots up. The first message displayed is "Hello bitcoin" with
three green dots.

If using wired Ethernet, it will then show an URL:

```
http://
10.0
  .0.123
```

This example is assuming the Ethernet is connected and your DHCP network server gave
the clock an IP address of `10.0.0.123`. Enter that URL
into your web browser and you're off to the races!

_IMPORTANT: Be sure to enter `http://` and not `https://` in front of
these URLs. This product does not support SSL on the internal setup
interface, and some browsers will try to search google rather than
connect directly to a local IP address in numeric form._

If the Ethernet cable is not connected, it will instead show:

```
SSid-
 block
   clock
```

This indicates the name (`blockclock`) of the wireless network which the clock provides.

The next step is for you to join the wireless network called
"`blockclock`".  The password for the network is: `blockclock`.

We suggest using a laptop for this step as cellphones
do not typically have enough screen space to operate the website
of the system comfortably.

Once you've done that successfully, the display on the block will change to
show the IP address of the internal website. This is always:

    http://10.3.141.1

Once you've joined the `blockclock` WiFi network, you should proceed directly to 
that IP address. If you try to navigate to any random website, you will
be redirect to the Blockclock as well. You can enter "example.com" as a URL
and end up at the Blockclock.

## Basic Setup

Once your web browser is showing the web pages from the BlockClock, you
should go to the "System Settings" page and configure these values:

- System Password (optional)
- Country
- Timezone
- My Name (optional)

If your intention is to use Wifi, you should first set your country,
and then select your wireless network from the list of network names
the system detects.

Enter your Wifi password and select WPA2 as security model.

To save and test your values, press "Connect to Wifi" and wait while
the connection is made.

_IMPORTANT: Once you've got the clock talking to your local Wifi network, you should change
your laptop back to your normal Wifi settings. (And on Mac OS, select
"Forget network" as well.)_


# Regular Operation

## Pages / Profiles / Lines

There are 3 lines for each "page" of values that may be displayed.

Up to four pages can be shown by a single press on the remote, click
on the homepage, or by rotation. By default, the display changes to
the next page every 15 minutes.

There are 12 profiles, each holding a page to be displayed. Although
only four are active at a time, it's useful to be able to store
profiles for later use, or use under specific conditions (ie. for parties
and special occasions).

Each page (or profile) holds the 3 lines which the clock displays.
Each of those lines has a corresponding red and green dot which can
be controlled as well.

## Expressions

There are five values you may configure for each line of the display: 

- Data Source, for example: `bitcoinaverage.BTCUSD.last`
- Overlay Text: `USD`
- Display Expression: `int(x)`
- Red Dot: `x < 3500`
- Green Dot: `x >= 4500`

The data source is the numeric value to display, and is often a
price ticker value, such as the buy or sell price for BTC. You must
enable specific data sources (ie. exchanges) on the Data Source page,
and then you may search and select a specific value from the search results.

The overlay text is a fixed string that will be left-justified and
shown on top of the dynamic text. It's useful for labeling values.

The remaining three fields can be a complex python expression, or
left blank. Click on the _(i)_ icon to see a quick cheat sheet of
possible expressions that are the most useful.

In most cases, you will want to use `int(x)` to round the value to an
integer and remove the decimal. 

Red and green dot expressions should be a boolean (true/false or
non-zero/zero).  You can compare the `x` value to a trend, or just
a simple threshold, as shown in the above example.

## Data Sources

As of this writing, the following data sources are available:

binance
: Data from [Binance](https://Binance.com) on 400+ exchange pairs

bitcoinaverage
: [Bitcoin Average](https://Bitcoinaverage.com) of exchange-neutral pricing

bitstamp
: Prices from [Bitstamp](https://Bitstamp.net)

bylls
: [Bylls](https://bylls.com) is a Canadian retail exchange

coinbase
: Buy and spot pricing from [Coinbase](https://Coinbase.com)

cbix
: Canadian exchange data site [CBIX.ca](https://CBIX.ca)

kraken
: American exchange [Kraken](https://kraken.com)

localbitcoins
: Worldwide, on-the-ground prices, from [Local Bitcoins](https://localbitcoins.com)

electrum
: Current top block values from an Electrum server (any chain)

The following internal sources are also available.

temperature
: Local temperature sensor on the clock

datetime
: Date and time values in configurable timezones

network
: Assigned IP addresses

api
: Values uploaded by your API calls

text
: Placeholder text values (ie. `text.blank`)

Please note that some data source require additional customization. For example,
you may need to select individual exchange pairs for some exchanges, while other
exchanges provide data on all pairs.

We provide a default configuration for Electrum which points to our
own Electrum server on Bitcoin mainnet. This can be changed to
select public nodes from a number of different chains which publish their
seed nodes. You can also select a private Electrum service by
providing a suitable IP addresses for your LAN or the Internet.


# Local Control

## Remote Control

Your clock includes a remote control which operates over Bluetooth.
It has four buttons (1-4). By default the buttons 1-3 select that page
for display. The 4th button is configurable to be: "mute" (the default),
"select 4th page for display", or "lamp test".

_NOTE: The remote control sleeps to conserve power, so the first
press may require holding for 1-2 seconds. Look for the red light
to illuminate._

## Lamp Test Button

There is a small button on the rear of the clock, labeled "LAMP TEST".

It starts a display cycle which turns all segments on and off.
Finally, it displays the IP address and then returns to normal
operation.

# Further Reading

## Customer API Interface

The BlockClock can accept your custom data and display it. Go to
"Data Sources > Custom API Keys" and press "Create new API key".

A `curl` example is shown to get you started, and
[the full API specification is here](api).


## Other Features

On the "System Settings" page, you will also find diagnostic features,
such as "warm restart" and a means to download/backup your settings.
There is also a push-button software update mechanism which is very
easy to use.
