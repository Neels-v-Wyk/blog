+++
title = "An Inaccurate Map of The Internet"
date = "2020-04-25"
description = "An article showing how I made a map of the internet with some GeoIP location data"
tags = [
    "Tech",
]
categories = [
    "Networking",
    "Visualization",
    "Internet",
    "Data",
]
+++

This article is all about [IP Geolocation](https://dev.maxmind.com/geoip/geoip2/geolite2/), [Maps](https://matplotlib.org/basemap/users/examples.html), and why both are pretty inaccurate.
<!--more-->

### Why IP Geolocation sucks

Most publicly accessible IP Geolocation databases are good enough that they can estimate your country with a high amount of certainty, state/province with reasonable certainty, and city with maybe 60-70% accuracy. Due to the way DHCP/provider assigned dynamic addressing works, there's a pool of addresses, and when your modem boots up, establishes a connection via PPPoE, or your DHCP lease gets renewed, you get an address. If your lease renews again, your modem reboots, or your connection is reset somehow, you get a new address, and your old one gets thrown back into the pool and gets assigned to someone else. That might be someone down the street, in the same hood, or same city, it all depends on how your ISP segments the allocation of their pool of IPs. Possibly your IP address might be a front-end NAT IP, where users could be all over the place, like a mobile provider's NAT pool. Hundreds of thousands of users could be assigned from the same 256 addresses, just on different ports.

The way Google and their Big Brother friends know your location so well is because of their various apps (Maps, Waze, etc.) that send a combination of constant GPS data and the IP those requests were sourced from. So you're the one updating Google/Facebook/Apple's geolocation database in real time as you use their products, so even when your GPS is off later, the realm of possibilities of where any IP is, is reasonably accurate.

Things like MaxMind (where we;re getting our data from later) work a bit differently. They eat RIR data (whois), and then enrich it with bought data from 3rd party collectors, typically people who run various e-commerce sites. Those people will let you fill in forms whenever you order a thing, express interest in something, etc. So they know which IP you're filling the form from. Order a dozen meals and plonk them on a credit card from the same takeaway joint that has such backend data and an exchange deal from the same IP and now MaxMind has a good idea of where your IP lives.

This behavior is the source of your annoyance if you spin a VPS in a place like Hetzner, OVH, or whatever discount VPS hoster, and suddenly discover your default google is in Russian or Arabic - it's typically because your IP was previously used by one or another VPN provider (which like to cycle IPs often, and use dedicated server/VPS providers to run operations from), and people used it on their android or IOS phones as VPN, and used Maps/Waze/whatever, this getting Google to think you're somewhere where the previous user's VPN was active.

### Why (most) maps suck

Don't get me wrong, not _all_ Maps suck, it's just that _most_ maps suck. The reason for this is pretty simple: the earth is round, and maps tend to be flat. You can't really accurately map a curved 3d surface on a 2d plane without some distortion. The most popular method of map projection is the Mercator projection:

![Mercator Projection](/mercator_projection.png)

With this projection, the tops and bottom of the map is very inflated, and areas that are far north will be disproportionate to areas that are near the equator. For example, Greenland (2,166 million km²) is slightly less than twice the surface area of South Africa (1,22 million km²). Doesn't look it, right?

This is going to cause issues for us going forward, mainly related to the fact that our inaccurate data from our GeoIP database will become exponentially more inaccurate the closer to the poles they are. Thus most data in the far nothern countries (Greenland, Northern Russia, etc) will look slightly off for anyone that does a lot regarding Cartography.

### Getting the data

We're gonna get our free GeoIP database from  [MaxMind](https://dev.maxmind.com/geoip/geoip2/geolite2/). We're downloading the database and importing it locally instead of using the API because we're probably gonna get [ratelimited](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/429) trying to request data for every single IP available to us. That, and local is faster. After signing up for an account and getting the DB, this is how we import it:

```python
from geoip import open_database

with open_database('data/GeoLite2-City.mmdb') as db:
    match = db.lookup_mine()
    print 'My IP info:', match
    # This prints info about your current IP
```

TODO:
- clean geoip data
- make a folium map of it
- integrate map into this page
