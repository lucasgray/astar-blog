title: Using a Kalman Filter to Smooth GPS Coordinates
author: Lucas
tags: []
categories: []
date: 2016-10-17 06:25:00
---
Lately I've been working on recording routes in ASTAR.  Using react-native, I set up a background task that fires every few seconds like this,

```
import BackgroundTimer from 'react-native-background-timer';

//snip

const watchId = BackgroundTimer.setInterval(() => {
  // this will be executed every 1000 ms
  // even when app is the the background

  navigator.geolocation.getCurrentPosition((position) => {

    //update state, update store, whatever you want to do here

  },
  (error) => alert(JSON.stringify(error)),
  { timeout : 10000, maximumAge : 500, enableHighAccuracy : true});
  }, 1000);

  this.setState({watchId: watchId});
}
```

Using `react-native-background-timer`, the route will keep recording even if the phone is asleep.

Excited to try it out, I walked home while recording.

![](/img/wobbly-route.png)

Whoa!  Those GPS coordinates are really wobbly, especially when walking.  So I looked around for an easy way to smooth them out.

## Enter Kalman

A Kalman filter is a stateful algorithm that can clean up these messy GPS readings.  I found [this article](http://www.bzarg.com/p/how-a-kalman-filter-works-in-pictures/) really helpful in getting up to speed on how it works.

Sold on the idea, I looked around for a Kalman filter in JS to implement and found [wouterbulten/kalmanjs](https://github.com/wouterbulten/kalmanjs).  While kalmanjs only operates on one dimension, and our gps coordinates are in a two dimensional space, it's enough to get started.  

```
import _ from 'lodash'
import KalmanFilter from 'kalmanjs'

//g looks like {coords : {latitude: 42.2983, longitude: 89.3245} }

smooth(g) {
  let latLongs = _.map(g.latLongs, (ll) => {

    if (ll) {
      let asJson = JSON.parse(ll)
      let coords = asJson.coords
      let f = [coords.latitude, coords.longitude];
      return f;
    }
  });

  let kalmanLat = new KalmanFilter({R: 0.8, Q: 20});
  let kalmanLong = new KalmanFilter({R: 0.8, Q: 20});

  let postKalman = latLongs.map(function(c) {
    return [kalmanLat.filter(c[0]), kalmanLong.filter(c[1])]
  });

  return postKalman;
}
```

A Kalman filter takes two floats as arguments, R and Q, as you can see above.  These are meant to model how wobbly you expect the source data to be *(R)*, and how wobbly you expect the measurement to be *(Q)*.  I landed on setting R really low and Q really high.   I played around with different numbers to seee how it'd affect the result.  I think I could add many optimizations here, including using the accuracy and speed readings into the algorithm.  It seems the faster you go, the more accurate the GPS readings become - walking was much more wobbly than running.

Here's the previous route Kalmanized:

![](/img/fixed-route.png)

Much better!

One problem I encountered is that this method drastically cuts very sharp corners - it smooths them out into curves.  I'm hoping if I could find a 2D JS Kalman Filter implementation that would solve my issue, but I haven't found one yet.  But expect a follow up blog post if I do!
