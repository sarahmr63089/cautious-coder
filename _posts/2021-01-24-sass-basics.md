---
layout: post
title: Refactoring with Sass
date: 2021-01-24 15:41:38 -0400
author: Sarah
image: assets/Stitcher.png
excerpt: A few ways I've integrated sass into my project Stitcher.
---

I've been spending a lot of time working on my front end skills. One of the technologies I've been learning is Sass. Sass is a CSS preprocessor, meaning you write your Sass code that is then compiled to CSS to be read by the browser. Using a CSS preprocessor like Sass is pretty useful because you can incorporate tools that are not available in CSS. In this post I will show three ways I refactored my styling in my app Stitcher with Sass. Before I show any code, it is important to note, quickly, that Sass has two supported syntaxes: Sass and Scss. I will be using Scss.

Sass has a few tools that are frequently used: variables, mixins, nesting, and extend. In Stitcher, I made the best use of variables, nesting, and extend. Variables in Sass are marked with "$" and can be set globally or locally. I set the following variables:

{% highlight scss %}

$font-family: 'Didact Gothic', sans-serif;
$custom-yellow: #ffd11a;
$box-shadow-color: rgba(255,255,255,0.3);

{% endhighlight  %}

I employed these variables in a variety of places like this:

{% highlight scss %}

body {
  font-family: $font-family;
}

.navbar {
  color: $custom-yellow;
}

.meter {
  box-shadow: inset 0 -1px 1px $box-shadow-color;
}

{% endhighlight %}

Nesting selectors with Sass is very useful, but, as the documentation and many articles point out, it should be used with caution. If you overuse nesting, it's very easy to override other CSS properities but can be tricky to debug. In Stitcher, I used nesting to collect button effects using the "&" as you can see below. 

{% highlight scss %}

button {
  background-color: white;
  cursor: pointer;
  font-family: $font-family;
  &:hover {
    box-shadow: 0 2px 4px 0 grey;
  }
  &:focus {
    outline: none;
    transform: scale(1.20)
  }
}

{% endhighlight %}

Lastly, I'd like to share my use of Sass's extend tool. This is a useful tool that allows you to define a group of CSS declarations together and use them throughout your stylesheet. You name the collection with an "%" and then write "@extend %[name]" wherever you'd like the collection to be used. I noticed that throughout Stitcher I was calling both "display: flex" and "flex-direction: column" frequently and decided to make that an extend and applied the same logic to aligning items and justifying content in the center.

{% highlight scss %}

%flex-column {
  display: flex;
  flex-direction: column;
}

%center-display {
  justify-content: center;
  align-items: center;
}

.login-container {
  @extend %flex-column;
  @extend %center-display;
}

.details-buttons {
  @extend %flex-column;
}

{% endhighlight %}

These are just a couple ways I have refactored my Stitcher stylesheet with Sass. I'm really looking forward to using Sass in a project from the very beginning. Many of my previous projects were written quickly within a short deadline and my CSS is functional, but a bit of a mess. While learning Sass, I came across two ways that people commonly organize their stylesheets. They seem to either have all their Sass in one directory separate from their components or they have a component-specific stylesheet included within the component's folder. I'm going to approach my next project in one of these two ways in order to maintain more organized and easy-to-read styling.

Resources:  
[Sass Documentation](https://sass-lang.com/documentation)