![General Assembly Logo](http://i.imgur.com/ke8USTq.png)

## Objectives

* Generate URL path and URL Helpers.
* Show current routes.
* Using View Helpers.
* Delete a movie.
* Using the Flash for messages.

## Previous Lesson
[Update a Movie](./ControllerUpdate.md)

## Source Code/Implementation

**Note: The implementation of this lesson is in the `movies_delete` branch of this repository**
[`movies_crud_app`](https://github.com/tdyer/movies_crud_app)

## Setup

**Re-init the DB with the schema, seed data and start the app.**

```bash
$ rake db:reset
$ rails s
```

## Route URL Helpers

**Add a path helper for two routes in the `config/routes.rb` file.**

A route not ONLY determines what controller/action should be invoked by a path and HTTP verb. It also generates path and url helpers.

**Update two routes to have generate URL helpers.**

```ruby
get '/movies/new', to: 'movies#new', as: 'new_movie'
 
get '/movies/:id/edit', to: 'movies#edit', as: 'edit_movie'
```
The `:as` options will generate the correct URL paths.

Now we can see these helpers in the rails console.

```
$ rails console
> app.movies_path  
"/movies"
> 
> app.movies_path(3)
"/movies/3"
>
> app.new_movies_path
"/movies/new"
>
>> app.edit_movie_path(3)
"/movies/3/edit"
> 
```

Now we can use these URL Helper methods in a view or controller.

For example, we use these URL Helpers in the create and update actions.

**In the movies_controller.rb.**

```ruby
...
	format.html { redirect_to movie_path(@movie), notice: 'Movie created' }
...
	format.html { redirect_to movie_path(@movie), notice: 'Successfully updated the movie' }
```

And we can use it along with a `link_to` helper to create a link to a movies in the `index.html.erb` template file.

**Update the `app/views/movies/index.html.erb`**

Now the index view will have a link to the show, edit, delete and movie action for each movie. Also it will have a link to create a new movie.

```
<ul>
  <% @movies.each do |movie| %>
  <li><%= movie.name %>
    | <%= link_to("Show", movie_path(movie.id)) %>
    | <%= link_to "Edit", edit_movie_path(movie.id) %>
    | <%= link_to 'Destroy', movie_path(movie.id), method: :delete, data: { confirm: 'Are\
 you sure?' } %>
  </li>
  <% end %>
  <hr/><br/>
  <%= link_to "Create a New Movie", new_movie_path %>
</ul>

```

See the `movie_path(movie.id)` URL helper. It will generate a `<a href="/movies/1">Show</a>` anchor tag.

## Show All the Routes.

Let's take a look at the app's routes.

```
$ rake routes
Prefix Verb  URI Pattern                Controller#Action                             
    movies GET   /movies(.:format)          movies#index                                  
 new_movie GET   /movies/new(.:format)      movies#new                                    
edit_movie GET   /movies/:id/edit(.:format) movies#edit                                   
     movie GET   /movies/:id(.:format)      movies#show                                   
           POST  /movies(.:format)          movies#create                                 
           PATCH /movies/:id(.:format)      movies#update 
```

This is an often used command to see the routes for our app.



## Lab

## Next Lesson
[Update a Movie](ControllerUpdate.md)

## Resources
* [Rails Cheat Sheet](Cheatsheet.md)
* [Rails Guide - Using Partials](http://guides.rubyonrails.org/layouts_and_rendering.html#using-partials)
* [PragStudio - RubyOnRails Level 1](https://pragmaticstudio.com/rails). This is a **very** good resource for learning Rails. They have been teaching Rails since the beginning and their teaching and presentation skill are **excellent**.



