---
layout: post
title:  What was the most difficult part of learning HTML5?
date:   2016-05-13 04:14:55 -0400
---

The trickiest part that I came across when learning HTML5 was **browser compatibility issues**. This problem was covered in the last two lessons of the HTML5 series: *HTML5 Media* and *HTML5 Semantic Elements*.

## HTML5 Media
With the advent of HTML5 came the introduction of the **video** element, which allows sites to render video without relying on the previously used Adobe Flash plugin. 

Various browsers support different types of media files. For example, the three most commonly used video format types per browser (as of the time of writing):

* **Chrome**: .mp4, .ogv, .webm
* **Firefox**: .ogv, .webm, .mp4 (partially supported)
* **Safari**: .mp4
* **Opera**: .ogv, .webm
* **Internet Explorer**: .mp4

As we can see, .mp4 is the most widely supported video format, but by itself, cannot stand as the consensus to be adopted across all browsers.

In addition, we as programmers must also consider the possibility of users using outdated browsers. To address this issue, we must provide a source element that corresponds to each browser's media type. A browser that does not recognize a specified audio or video element can simply skip until it finds an alternative source type that it does understand. Generally, it is recommended to include at least two different video formats to ensure close to perfect compatibility across all browsers and devices. 

```html
<video>
  <source src=”movie.mp4” type=”video/mp4”> 
  <source src=”movie.ogv” type=”video/ogg”> 
  <source src=”movie.webm” type=”video/webm”>
</video>
```

In the event that an older browser does not support any of the listed media elements, fallback content like the following should be provided:

```html
Your browser does not support HTML5 video. <a href="http://browsehappy.com" target="_blank">Please upgrade your browser</a>.
```

## HTML5 Semantic Elements
HTML5 introduces another new concept called **semantic elements**. In previous HTML versions, the `div` element was used often to create containers to surround HTML content and separate each content from each other. This is an important feature because `div` elements provide organization and structure for the page, especially in terms of positioning and adding column structures. The downfall to this, however, was in having to name the `div`s for each separate page component. Compare the following:

| pre-HTML5  |  HTML5  |
|------------------- | ----------------- |
| `<div id = "header">` | `<header>` |
| `<div id = "main-content"> `| `<article>` |
| `<div id = "sidebar">` | `<aside>` |
| `<div id = "footer"> `| `<footer>` |

Instead of having to name each `div` element directly, we can use the **semantic element** that comes predefined with HTML5. Not only is this more concise, but this new labeling allows even beginner coders to know exactly what each element does. This is an excellent improvement but again, we come across the same issue as mentioned before. *How do we support older browsers that may not recognize HTML5?* 

### Modernizr
Modernizr is a JS library that looks for a browser's current HTML5 / CSS3 settings, and provides additional support if necessary. Inside the HTML page, a link to the file should be placed within the `<script>` tags. 

### Normalize
Normalize supports cross-browser consistency for styling. A link to the *normalize.css* file should be placed within the `<link>` tags of the HTML document. 

In order to avoid incompatibility issues and provide an enjoyable experience for the user, it is important to consider a website's interaction with and performance across all devices and browsers. Aiming for a responsive web design is a key component in attaining high user experience / satisfaction. 
