---
layout: post
title: Building a Search in Rails
date: 2020-08-12 15:41:38 -0400
author: Sarah
image: assets/rails search.jpg
---
Recently I was working on a Ruby on Rails project that allowed a user to create playlists. One page featured a list of all of the available songs and I wanted the user to be able to search by song title, artist name, or genre. The database already included tables for all three and the models had the appropriate relationships (songs belong to genre and artist, and genre and artist had many songs). Fortunately, I had previously put together a search using data from one table and could use that experience, but I was a bit apprehensive about searching through three tables.

First, I grabbed my code from a previous project as a starting point and modified it, starting with the songs index view page.

{% highlight ruby %}
<div class="search">
  <%= form_tag songs_path, method: :get do %>
    <%= text_field_tag :search, params[:search]%>
    <button type="button" class="btn btn-secondary" id="createedit">
      <%= submit_tag "Search", class: 'button' %>
    </button>
  <% end %>
</div>
{% endhighlight %}

Here I set up a form for the search with a text_field_tag for the user input and a submit button. In the text_field_tag, I set the name to search and the value to be the user input as the params[:search], which will later play a role in my controller and model. 

In the controller, I set up the index page to use a class method "search" on all the songs with the params[:search].

{% highlight ruby %}
def index
  @songs = Song.search(params[:search])
end
{% endhighlight %}

Then the model does the heavy lifting in the class method "search." In this method, I first check to see if a search is being performed. If there is no search (if the params were empty or nil), I display all songs. This is what happens the first time a user navigates to the page or when they search with an empty search bar. If there is a search, I used a combination of SQL and ActiveRecord methods to search all three tables for the given params, passed into the search method.

{% highlight ruby %}
def self.search(search)
    if search.present?
      Song.joins(:artist, :genre).where("artists.name ilike ? or title ilike ? 
      or genres.name ilike ?", "%#{search}%", "%#{search}%", "%#{search}%") 
    else
      self.all
    end
  end
{% endhighlight %}

The trickiest part of putting this together was writing the query. In order to figure it out I experimented in postgresql with 3-table inner join queries. An example of the query I used to construct the method above is below. I created an inner join between songs and artists and genres, and then used a postgresql "ilike" to make the search case-insensitive.

{% highlight ruby %}
select * from songs inner join artists on songs.artist_id = artists.id 
inner join genres on songs.genre_id = genres.id where artists.name ilike 
'%born%' or title ilike '%born%' or genres.name ilike '%born%';
{% endhighlight %}

Using this query as a model, I used the ActiveRecord methods "join" and "where" to finish the method. Song.joins(:artist, :genre) performs the inner joins and ".where" looks for a match, replacing ? with the "%#{search}%" provided. 

If I run a search with a byebug in my controller action and in my model method you can see that the user input becomes the params used in the controller and the argument used in the method.

![terminal screenshot](/cautious-coder/assets/search_in_rails_byebug_image.png)

The following resources were incredibly helpful while putting this together:

[Simple Search Form in Rails by Yassi Mortensen](https://medium.com/@yassimortensen/simple-search-form-in-rails-8483739e4042)

[Using form_tag and params to create a search or render a sorted view in Rails by Tracie Masek](https://medium.com/@traciemasek/using-form-tag-and-params-to-create-a-search-or-render-a-sorted-view-in-rails-eb3378eeaaa7)

[Posgresql Docs for 'ILIKE'](https://www.postgresql.org/docs/9.0/functions-matching.html)

[Posgreql Tutorial for Inner Join](https://www.postgresqltutorial.com/postgresql-inner-join/)