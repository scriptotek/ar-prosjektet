---
layout: post
title:  "Designing the prototype"
author: kyrretl
date:   2019-01-17 18:15:00
categories: AR Design Development Wikitude Android javaScript
---

![Concept sketch for the final AR prototype](https://scriptotek.github.io/ar-project/assets/sketch.png "Concept sketch for the final AR prototype")
Implementing functionalities and designing the prototype with Wikitude
<!-- more -->

After reviewing six different AR-platforms and testing most of them ([see here for details](https://scriptotek.github.io/ar-project/platform-evaluation/)), we decided on Wikitude and started the process of fleshing out a prototype based on the possibilities and limitations of the chosen platform. In this blog post we write *mainly* about the design and functionality of the prototype app - for more in depth about our experiences with the Wikitude you can read ([this post](https://scriptotek.github.io/ar-project/blog/2019/01/17/exp-wikitude.html)). But we recommend you continue reading here first.

## Functionalities of the prototype
We wanted to offer both `wayfinding` - locating books on the shelves in the library - and `metadata` - extending the information you can get from a book when AR is used.

## Wayfinding
The first functionality we looked into was wayfinding. With the main purpose of locating books inside a library we needed good support for *indoor wayfinding*.

As of 2018 the possibilties of using mobile phones for indoor wayfinding or localizing is unfortunately still quite limited. GPS-sattelites do not penetrate the insides of buildings and adapting WiFi-networks for trilateration usually yield low precision (with error margins of several meters often placing the user in a different room). Solutions require specialized beacons, specialized WiFi-points or other third party technologies, taking time and resources to implement.
We wanted to see if we could offer wayfinding without the costly and time consuming alternatives, ideally only requiring a phone.

Wikitude offers different ways of recognizing objects in the environment or the environment itself:

* Scene reconition
  * Images of large (mainly outdoor) scenes. 
* Image recognition
  * Image recognition recognizes specific images, signs, banners and so on in the real world.
* Object recogntion
  * A video or series of pictures of a real world physical object


### Scene recognition test
We wanted first to see if we could use a *Scene recognition* approach for wayfinding. We hoped we could take pictures of the library rooms, halls, shelves, staircases, the reception, etc, and use these to let the prototype app recognize where it was.

We did preliminary tests using shots of the office and some shelves nearby to get a sense of how well scence recognition would work with wayfinding. Below is an example of a typical library setting, and the prototype is displaying an arrow guiding the user to a specific collection.

![Testing of wayfinding inside the library](https://scriptotek.github.io/ar-project/assets/wayfinding_scene_test.png "Testing of wayfinding inside the library")

For the prototype to show a wayfinding arrow like the one in the image above, it needs to recongnize where it is at any time, regardless of the angle of the viewfinder. To maximize the chances of scence regonition, we took multiple overlapping shots of the room from different angles (around 20 images). Unfortunately, it did not work as we hoped. The issues we faced were that the scene failed to be recognized more often than not, making the guiding arrow disappear, jitter or get stuck pointing in a wrong direction. This happened either if you stood at an angle where no (or too few) pictures were taken of the scene, or if the scene itself had changed. As seen in the image above, there are moving elements in a library that could potentially change the scene: we have a touch table on wheels, exhibition shelves that change content, a user sitting in a moveable chair, etc. Also, at any time there will be users standing/moving about obscuring the scene. We found the scene recognition solutuion to be untenable due to the instability of detection. This was unfortunately not very surprising since scence recognition is meant to be used outside on large scale scenery that doesn't change nor gets obscured enough to prevent detection.

### Image recognition test
Since we couldn't get scene recogntition to work reliably, we had to resort to image recogntion with specific target images that users would have to get up and close to. Obviously not preferrable to a system that guides the user without the user having to do anyting for it - but it was the only option available since we did not have the time or resources to use custom third party hardware for localization. 

The image recognition proved to be very stable, although the user would sometimes be required to move quite close to the target image to get a response. We didn't really see this as a problem for the prototype - the important part is that the *user* recognizes the target image in the room, understands what it is and knows to approach it.

We chose an image of a lighthouse as a wayfinding anchor. It's easy to recognize, and has a nice look to it. We called it "AR light house" and made three different colored variations of it:

![The three different AR anchor images](https://scriptotek.github.io/ar-project/assets/AR_lighthouses.png "The three different AR anchor images")

This way the prototype can separate the different target images (by color) and know which location it is at, and users see a common recognizabe theme. *Update: We realized later that Wikitude renders target images in greyscale, so we had to change the AR Light Houses' appearance. To read more about this, see [The finalized app](https://scriptotek.github.io/ar-project/finalized-app/).* 

Below we see the prototype reacting to the red AR lighthouse image:

![Testing of wayfinding inside the library](https://scriptotek.github.io/ar-project/assets/wayfinding_image_test.png "Testing of wayfinding inside the library")


## Metadata
Using AR to get metadata on a book was the second main goal of the project. After some brainstorming and design prototyping we decided to divide metadata into three categories:

1. External resources
    * Many different sources of metadata, such as book description, wikipedia article, video, game, app, etc.
2. Similar books
    * Physical books in our library catalogue that have at least one Subject Heading (topic) in common with the target book.
3. More from author
    * Physical books in our library catalogue from the same author


![Metadata on book](https://scriptotek.github.io/ar-project/assets/metadata_test.png "Metadata on book")

### 1. External resources
When it comes to external resources, there is figuratively an ocean out there, and different books will have different kinds of relevant resources: scientific articles, online tools, games, mobile apps, paintings, poems, videos, podcasts, etc, etc. 

We ended up narrowing the list down to the following widely shared resources:

* Description
* Wikipedia button
* Video

The aim was to do automatic content harvesting and if the resources were missing to not display them rather than manually adding them.
We used different APIs ([Application Programming Interface](https://en.wikipedia.org/wiki/Application_programming_interface)) to automatically access and display content where we could.

#### Description
Sometimes a book might not have a sleeve description, it might be very short and not so informative, or just a list of buzzwords and recommendations, we wanted to display this using the [Google Books API](https://developers.google.com/books/).

#### Wikipedia button
In the image above, the button titled 'External resources' was originally intended to link to a web page where there could be links to as many external resources desired, but because of time and resource constraints we reduced this to a direct 'Wikipedia'-link. This button would be shown or hidden depending on whether an API call to Wikipedia found an article. 

#### Video
Unfortunately at the moment Wikitude does not allow overlay videos from Youtube due to licencse issues and the fact that Wiktude requires a direct link to the H.264 video encoded file and not any mediator format - Youtube (or Vimeo, etc) links to a url and not the video file itself. To put this in breif, it basically means:

1. You will not be able to play a Youtube video file in a "magic window" (as seen in the lowest rectangle superimposed on the book above).
2. If you click on a button that links you to the Youtube video, it will not be played in the phone's video player, rather a web browser will open with the Youtube web page, or if the user has installed the Youtube app then it will be played there.

After testing, we found that although we prefer videofiles that can be played directly inside the prototype app, it is quite hard to find such free licences videos, and not using Youtube would make us miss out on many excellent videos. 

To illustrate the differences of these approaces, we included two different videos - one: a link to a Youtube video - and two: a direct link to a videofile to be played inside the prototype app. To not add confusion, we decided to not have any video be playable inside the "magic window" (since Youtube videos cannot) and rather display a button to access the video.

### 2. Similar books
We wanted to offer related books using our own Subject Headings. To achieve this, we created a designated web page showing up to ten books (sorted by year, newest first) that share at least one Subject Heading with the target book. We used the API to our library catalogue for this purpose.

### 3. More from author
Similarly, using out catalogue API, we also wanted a way for a user to find more books by the same author. In our library, books are never organized on the shelves by author, rather by some classification system like [Dewey](https://en.wikipedia.org/wiki/Dewey_Decimal_Classification). In fact, books by the same author might even be spread across several collections in different rooms.


## Read more

* [Experiences with Wikitude](https://scriptotek.github.io/ar-project/blog/2019/01/17/exp-wikitude.html)
* [The finalized app](https://scriptotek.github.io/ar-project/blog/2019/01/17/final-app.html)

## Sources
* [Wikitude](http://www.wikitude.com)
* [Lighthouse image on Wikimedia Commons](https://commons.wikimedia.org/wiki/File:Brier_Island_Lighthouse_(1).jpg)
* [Wikipedia API](https://www.mediawiki.org/wiki/API:Main_page)
* [Google Books API](https://developers.google.com/books/)

