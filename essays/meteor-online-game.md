---
layout: essay
type: essay
title: "Building a Multiplayer Game with Meteor"
date: 2017-10-26
labels:
  - Software Engineering
  - Meteor
---

What stood out to me the most for Meteor was that it was built with reactivity in mind. In the past two weeks, I took the liberty of experimenting with the publish and subscribe pattern that Meteor boasts about, and came across an interesting idea that would be fun to play around with. In this short essay I explain how Meteor can be a suitable framework for creating an HTML5 multiplayer online game.

<h2>What most games use</h2>

Today, most multiplayer games operate on top of a client-server networking model that uses an authoritative server. An authoritative server dictates everything that happens in a game. Any movement of objects, interaction between players, and retrieval of player data are all processed by the server. The game information is pushed out to clients that are listening for such events, and upon receiving the data, the clients use it for rendering the game. In the same manner, clients push out input data (mouse clicks, key presses, etc.) that the server uses to calculate, predict, and create snapshots of the game at any given point in time. Note that this pushing and listening business sounds awfully familiar to Meteor’s publish and subscribe mechanism!

These two mechanisms are similar in that any one component in their system can react to another component’s actions without having instructed when to respond. In Meteor, the first component is known as the reactive computation, and the second component is known as the reactive source. While Meteor provides two common methods for you to code reactively--using session variables and database queries--only database queries are really relevant to our specific use case. Because session variables are locally stored and accessed, it would be counter-intuitive to rely on that for an authoritative multiplayer game. Database collections are also a reactive source, but the global collections are synchronised with all subscribed clients using the application, implying that any updates requested by one client, if processed, will be seen by all of the other clients. Now that sounds more like what a multiplayer game needs! There is one major caveat to this, however: using database queries to handle all of your game’s updating logic will make it incredibly hard for your game to scale. Thus I highly recommend using a hybrid approach when it comes to processing game states.

<h2> Just Meteor is not enough </h2>

Meteor’s baked-in reactivity is great and it seems like a good place to start building a client-server architecture. But to really create a solid base for the implementation, it needs to be complemented with an efficient strategy that will minimize the overhead of saving game states in the database. Consider an alternative method of communication between client and server that doesn’t involve the database. Since Meteor’s database synchronization is defined by the DDP (Distributed Data Protocol) that is built on top of Websockets, it may be feasible to use Websockets directly (the layer itself) to create communication streams between client and server to achieve the same manner of reactivity. In these streams, the client would push requests while the server listens to specific channels, determines the game state, and saves the information in a temporary object. It’s when the client connects or disconnects that the server should use the default database queries to load or save information about that client in the database.

Check out this [game](https://github.com/ironbane/ironbane). It’s a massively multiplayer online game that’s written purely in JavaScript. The game is running on a Meteor server, and part of the front-end logic is carried out with Angular 1. The source code is not commented very well and some of them look pretty hacky, but it’s a solid proof of concept that shows what Meteor is capable of.
