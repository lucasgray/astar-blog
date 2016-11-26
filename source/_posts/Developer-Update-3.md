title: 'Developer Update #3'
author: 'Jerome Russ, Leigh Gray, Lucas Gray'
date: 2016-11-25 20:27:47
tags:
---
Time for another developer update!  Much progress in the last few weeks, despite busy work and personal lives.

### Robust Recording

Big news on the run recording front!  One big hurdle with our run recording development has been how to continue recording even when the phone is suspended, or the app is killed.  Since we're working in react-native, we only had a few options.  For a while, [react-native-workers](https://github.com/devfd/react-native-workers) was okay - at least suspending the phone wouldn't break the run recording, but switching to another app, hitting a menu item, etc. would.  

Finally posted the question and discovered [react-native-background-geolocation](https://github.com/mauron85/react-native-background-geolocation) - what a lifesaver.

With background-geolocation we can have a true android service, and we can config it to run in the foreground, with an undismissable notification, just like strava.  The notification takes you right back into the tracking screen where you can finish the run and see the results, and no messing with the phone will do anything to destroy the background geo tracking!  Here's a little preview...

First we hit record,

![](/img/record.png)

Which changes the screen UI and pops up the undismissable notification + foreground service,

![](/img/foreground-service.png)

(Yes I have two weather apps, no I don't know why either)

Then hitting stop takes us straight to the updated route results page,

![](/img/route-display.png)

### Pace Chart

Which is a great segway into a small feature addition in the route recording - the pace chart in the middle of the previous screen shot!  It's not great yet, I think it needs smart Y axis scaling, but it's a start!

### Start of the game

We've started officially on the hidout defense game.  Nothing good enough to show off yet, but we're progressing quickly.  Look forward to another installment for an alpha reveal.

### Firebase

Finally - FIREBASE!  We now have offline cloud storage for routes, and soon to come game information.  The route tracking still stores it's data in local-to-phone storage until the route is finished, then it is shipped to firebase and presented on the screen.  

### Next?

Now that we've got firebase installed, we'll be working on how the flow of the games, RPG-style progress, etc. will work.  Expect more of that next time!