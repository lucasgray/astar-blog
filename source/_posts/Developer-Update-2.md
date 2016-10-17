title: 'Developer Update #2'
author: Lucas Gray
tags: []
categories: []
date: 2016-10-17 07:19:00
---
Solid progress this week!  

## Route Recording

I've made big improvements in route recording.  Check out my Friday lunch run!

![](/img/route-recording.png)

I've added the ability to display time/speed/distance.  I would love to add split times and a nice speed graph next, but I'll probably move onto more game related code before coming back to that.

Time is easy, last timestamp minus the first, using the great moment.js.  

Distance and speed were a little more difficult.

```
import _ from 'lodash'
import haversine from 'haversine'
import moment from 'moment'

//probably should pass smoothed into distance, right?
distance(g) {

  let latLongs = g.latLongs.map((ll) => JSON.parse(ll));

  let distance = _.reduce(latLongs, (acc, cur, index, li) => {

    let d = 0;
    if (index !== 0) {
      d = haversine(cur.coords, li[index-1].coords, {unit: 'mile'})
    }

    return acc + d;
  }, 0)

  return distance;
}

//me too?
speed(g) {

  let timeInMs = this.time(g)
  let distance = this.distance(g)

  let speedInMs = timeInMs / distance

  return speedInMs
}

formatAsMinutesAndSeconds(x) {

  let dur = moment.duration(x)
  return dur.minutes() + ':' + dur.seconds()
}
```

Using `lodash` made it nice and simple to gather all the distances between GPS coordinates and add them together, using [`njj/haversine`](https://github.com/njj/haversine) for a nice simple implementation of the [haversine formula](https://en.wikipedia.org/wiki/Haversine_formula).

## React-Native Code Organization

I've always struggled to figure out how exactly I should structure my javscript projects, be it Node, or react, or something else.  But I'm pretty happy with how it turned out, and how simple it was in React-Native.  

![](/img/restructure.png)

All my react code now lives in `/app`, static resources in `/app/assets`, etc.  Love that everything in react-native is a component, but I needed some delineation between sharable/reusable components and full page views.  Also needed some geo business logic so I built a service for it.

## Next!
* Offline storage solution (looking at you, firebase)
* POC of a canvas or webgl game embedded in a WebView














