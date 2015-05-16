# TalkingStick

This gem provides basic [WebRTC](https://webrtc.org) communication in any Rails app.


## Goals

* Provide easy group-based (2 or more participants) audio/video communication
* Allow selecting audio or audio+video
* Pluggable signaling delivery technology
* Plug & Play functionality - works out of the box
* Focus on simplicity at the API level, while maximizing the possibilites of integrating communication into existing Rails apps

## Installation

Note that TalkingStick requires jQuery, and installs [jquery-rails gem](https://github.com/rails/jquery-rails) as a dependency.

TalkingStick is built as a Rails Engine, and so follows those conventions.

1. Add to Gemfile and update bundle
```Ruby
gem 'talking_stick'
```

Now run `bundle install` to download the gem.

2. Mount the engine
Add the following to your application's `config/routes.rb`:
```Ruby
mount TalkingStick::Engine, at: '/talking_stick'
```

3. Install and Run migrations
To add the required models to your application, the migrations must be copied and run:
```
$ rake railties:install:migrations db:migrate
```

## How it works

* Each group is assigned a unique ID
* Rails app keeps track of participants in each room
* Rails app notifies all room participants when one joins or leaves
* Rails app proxies call setup between participants in each room

## Providing your own signaling mechanism

One of the critical decisions made by the standards bodies behind WebRTC was a decision to leave the signaling of WebRTC up to the application. This means that an application may use any means it desires to give the endpoints the data necessary to communicate directly.

This plugin tries to be "batteries included", which means that we included a signaling mechanism that will work on any Rails server. As such, we could not rely on any particular push technology, such as Websockets or long polling. Thus, the default behavior is that the browser polls the Rails API for updates on connected peers. This works fine for small user bases, but obviously will not scale well.

### Want help designing and deploying an efficient, scalable signaling mechanism for your application?
[Contact Mojo Lingo](https://mojolingo.com/connect) today! As authors of this Gem, Mojo Lingo can help you get the most value out of WebRTC by integrating communications into your application and business processes.


Alternatively, you can build your own by passing a "Signaling Engine" to the TalkingStick class.

The [reference Signaling Engine](https://github.com/mojolingo/talking_stick/blob/master/app/assets/javascripts/talking_stick/talking_stick/rails_signaling.js) periodically polls the Rails API for signals, but can be replaced with any API-compatible implementation, including Websockets, XMPP, or anything you like.

The Signaling Engine must implement the following methods. For each method where `guid` is specified, it represents the ID of the remote participant to which the information must be sent.

* `connected`: Takes no parameters. Is called after the user's media is set up and the participant is registered to the room.
* `sendICECandidate(guid, candidate)`: The second parameter is the ICE candidate. Called each time a new local ICE candidate is discovered by the browser. This must be sent to the remote participant.
* `iceCandidateGatheringComplete(guid, candidates)`: Called when all ICE candidates have been gathered. This can be used in place of `sendICECandidate` to send all candidates at once, rather than trickling them in one-by-one (though trickling is generally a better user experience).
* `sendOffer(guid, offer)`: Called when the local browser wants to initiate an offer to a remote party.
* `sendAnswer(guid, answer)`: Called when the local browser wants to respond to a received offer.

## TODO

See the [Pivotal Tracker story board](https://www.pivotaltracker.com/n/projects/1343190)

## The Name

The [Talking Stick] is a tradition among Native American (and other) cultures which specifies a simple yet effective way for facilitating group conversations. For more, see the [Wikipedia Article](https://en.wikipedia.org/wiki/Talking_stick), or this insightful blog post by [Rich Goidel](http://www.dangerouskitchen.com/shut-up-and-listen/).

## Credits & Copyright

* Original author: [Ben Klang](https://twitter.com/bklang), [Mojo Lingo LLC](https://mojolingo.com)

This project is licensed under the MIT License. This project incorporates [Adapter.js from the WebRTC.org team](https://github.com/webrtc/adapter), which is licensed under the Apache license.

Copyright 2015 Mojo Lingo LLC
Portions copyright 2014 The WebRTC project authors
