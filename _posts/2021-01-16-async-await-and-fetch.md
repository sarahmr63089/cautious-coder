---
layout: post
title: Two Ways to Fetch with Dad Jokes
date: 2021-01-16 15:41:38 -0400
author: Sarah
image: assets/programmer-sitting-on-hourglass-vectorportal.jpg
excerpt: Two ways of writing fetches in Javascript.
---

Since I learned how to write API fetches, I've been writing them using promise chaining. Recently, I worked though a code challenge that used async/await and this blog post will juxtapose those two methods.

The API I'm going to be working with for this post is all about Dad jokes, arguably the best genre of humor. It is icanhazdadjoke and the documentation is available here: https://icanhazdadjoke.com/api. For this API, I need to include the "accept" header to specify how I'd like my response to be formatted and the "User-Agent" header with my GitHub repo by request of icanhazdadjoke. 

For this simple comparison, I set up a basic HTML document. The javascript code I will show later will reference the paragraph tag with the "joke" class with a variable "jokeDisplay."

{% highlight html %}

<!DOCTYPE html>
<html lang="en">
  <head>
    <script defer src="index.js" ></script>
  </head>
  <body>
    <h1>Dad Joke</h1>
    <p class="joke" ></p>
  </body>
</html>

{% endhighlight %}

First, let's take a look at a fetch written with promise chaining.

{% highlight javascript %}

fetch('https://icanhazdadjoke.com/', {
  headers: {
    "User-Agent": "https://github.com/sarahmr/fetch-practice-dad-jokes",
    "Accept": "application/json"
  }
})
  .then(res => res.json())
  .then(res => {
    console.log(res)
    jokeDisplay.innerHTML = res.joke
  })

{% endhighlight %}

Here, I wrote the fetch with the appropriate headers and the API url. The fetch returned a promise, formatted as an HTTP response. This is formatted as json with the first then and the second both logs the response and changed the inner HTML to display the joke. The then method also returns a promise and takes callback functions as arguments.

![Duvet Joke](/cautious-coder/assets/joke-duvet.png)

![Duvet Joke Log](/cautious-coder/assets/json-response-duvet.png)

Now, let's take a look at a fetch written with async and await.

{% highlight javascript %}

async function fetchJoke() {
  let jokeData = await fetch('https://icanhazdadjoke.com/', {
      headers: {
        "User-Agent": "https://github.com/sarahmr/fetch-practice-dad-jokes",
        "Accept": "application/json"
      }
    })
  let joke = await jokeData.json()
  console.log(joke)
  jokeDisplay.innerHTML = joke.joke
}

{% endhighlight %}

Here, the function "fetchJoke" is declared as an aync function. Async functions return promises and set up the expectation of an "await," which is not mandatory but often occurs. Within the function, the promise returned by the fetch is set to the variable "jokeData." This is where the "await" keyword is used. Await tells the code to pause here and wait for the fetch to be concluded before executing the next line of code, making this asynchronous code behave similarly to synchronous code. Await can only be used inside asynchronous functions where a promise is returned. Then "jokeData" is formatted as JSON data also using "await" as we need this formatted before the next line, and set to the variable "joke." Finally, the joke within the joke JSON is set as the inner HTML for "jokeDisplay." 

![dad joke ruins](/cautious-coder/assets/dad-joke-ruins.jpg)  

![dad joke ruins response](/cautious-coder/assets/json-response-ruins.png)


As visible in the logs, both of these methods work just fine. The benefit with using async/await is readibility. It's much easier to understand the second method without the nested then methods. 

Resources:  
[Mozilla - Using Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)  
[Mozilla - Making Asynchronous Programming Easier with Async and Await](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Async_await)  
Feed Image by <a href=" https://www.vectorportal.com" >Vectorportal.com</a>,  <a class="external text" href="https://creativecommons.org/licenses/by/4.0/" >CC BY</a>