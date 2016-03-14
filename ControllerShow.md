![General Assembly Logo](http://i.imgur.com/ke8USTq.png)

## Objectives

* Create a route to view one movie.
* Create a ActionController show action/method.
* Construct a View for the HTML Represenation of one movie resource.
* View how Rails processes a HTTP Request by viewing the server log.
* Understand how the params hash is used and created.
* Construct a JSON Representation of one movie resource.
* Construct a JSON Representation of ALL movie resources.

## Model, View, Controller (MVC)

Rails is based on the MVC Architecture.

![MVC](mvc_archi1.png)

## HTTP Request Processing.

![MVC Detailed](./mvc_detailed-full.png)

_Here we are getting the Users View but the flow through the app will be the same for a Movie View._

## Previous Lesson
[View all Movies](./ControllerIndex.md)

## Source Code/Implementation

**Note: The implementation of this lesson is in the `movies_show` branch of this repository**
[`movies_crud_app`](https://github.com/tdyer/movies_crud_app)

## Setup

**Re-init the DB with the schema, seed data and start the app.**

```bash
$ rake db:drop
$ rake db:create
$ rake db:migrate
$ rails db:seed
$ rails s
```

## View one Movie.

**Go to `http://localhost:3000/movies/1` in the browser**

Oops, Rails doesn't know how to handle a HTTP GET Request with a path of `/movies/1`.

> We need to create a route to handle this request.

## Create a Route for viewing one movie resource.

**In the `config/routes.rb` file.**

```ruby
  # Route a HTTP GET Request for one movie to the                                         
  # MoviesController show action                                                          
  get '/movies/:id', to: 'movies#show
```

The will create a route for a specific movie with a id.

**Go to `http://localhost:3000/movies/1` in the browser**

> Unknown action
>
> The action 'show' could not be found for MoviesController
> 

Oops, Rails is looking for the MoviesController#show action in the `app/controllers/movies_controller.rb`. Let's create it.

## Create a show action for viewing one movie.

```ruby
# GET /movies/:id                                                                                                                             
def show
  @movie = Movie.find(params[:id])
end
```

Creates a `@movie` instance variable that will contain the Movie model with an `id` equal to one. This uses the ActiveRecord#find method to get a row with id of 1 in the movies table. Then it will use the data in this row to create Movie model.

Note, this `@movie` instance variable is also available to the View code.

**Go to `http://localhost:3000/movies/1` in the browser**

Oops, the template file is missing.

## Create the View, in a template file, for one movie.

```
$ touch app/views/movies/show.html.erb
```

```ruby
<dl>
  <dt>Name:</dt>
  <dd><%= @movie.name %></dd>
  <dt>Rating:</dt>
  <dd><%= @movie.rating %></dd>
  <dt>Description:</dt>
  <dd><%= @movie.desc %></dd>
  <dt>Length (minutes):</dt>
  <dd><%= @movie.length %></dd>
  <dt>Year Released:</dt>
  <dd><%= @movie.released_year %></dd>
</dl>
```

See how we are using the `<%= ruby code .. %>` ERB syntax to invoke each Movie getter method. _Remember, a setter and getter method is created for each table column. In this case a getter/setter for each column in the movies tables._


**Go to `http://localhost:3000/movies/1`**

**Hurray!** This will create a HTML Representation of one movie.

## Params Hash

When data is passed to a controller action from a HTTP Request it's often kept in a `params` hash. In order to see this we can take a look at how the server logs a HTTP Request.

_In this case, Rails will create a params hash with the movie id using the id value in the path. `/movies/1` -> `{:id => "1"}`_

**Server Log for a HTTP GET Request with the path `/movies/1`**

_Knowing how to read the server log is **VERY** important._

```
Started GET "/movies/1" for ::1 at 2016-03-13 11:00:12 -0400
Processing by MoviesController#show as HTML
  Parameters: {"id"=>"1"}
  Movie Load (0.2ms)  SELECT  "movies".* FROM "movies" WHERE "movies"."id" = ? LIMIT 1  [["id", 1]]
  Rendered movies/show.html.erb within layouts/application (0.8ms)
Completed 200 OK in 31ms (Views: 29.7ms | ActiveRecord: 0.2ms)
```

* Line 1 - Shows the request method, GET, path, "/movies/1" and timestamp.
* Line 2 - Shows the controller/action that this request will invoke, MoviesController#show. Also shows that the request is for an HTML representation.
* Line 3 - Shows the params hash, {"id"=>"1"} created by Rails.
* Line 4 - Shows the SQL generated in the action.
* Line 5 - Shows the view, template file, rendered.
* Line 6 - Amount of time spent processing the request.

**Params Hash is formed from the id passed in the path!**

`/movies/id` forms the `{"id"=>"1"}` params hash. And the id, params[:id], is used to find  the movie in the DB. `Model.find(params[:id])`

## Use `respond_to` to determine the representation of the movie.

```ruby
 # GET /movies/:id                                                                       
  def show
    @movie = Movie.find(params[:id])

    # Handle multiple representations, (html, json, ...)                                  
    # By default render HTML representation                                               
    respond_to do |format|
      format.html # render app/views/movies/show.html.erb template                        
      format.json { render json: @movie }
    end
  end

```

The ActionController `respond_to` method will look at the either the URL prefix OR the HTTP Request Accept Header to determine which representation, (json, html,...), to show.


For the JSON representation:

**Go to `/movies/1.json` in the browser to view the JSON Representation of the Movie Resource.**

**Use curl to view the JSON Representation.**
```
curl -H 'Accept: application/json' http://localhost:3\
000/movies/1
```

## Update the index action to show JSON.

```ruby
 # GET /movies                                                                           
  def index
    # create an instance variable, @movies, that                                          
    # will have all the movies stored in the DB.                                          
    @movies = Movie.all

    # By default will render the app/views/movies/index.html.erb                          
    # file                                                                                
    respond_to do |format|
      format.html
      format.json { render json: @movies }
    end
  end

```

In the browser go to `http://localhost:3000/movies.json`

**Use curl to view the JSON Representation of all movies.**

```
curl -H 'Accept: application/json' http://localhost:3\
000/movies
```


### Cheat Sheet
[Cheat Sheet](Cheatsheet.md)
Here's one to get you started on your own!


## Lab

## Next Lesson
[Create a Movie](ControllerCreate.md)

## Resources
* [Rails Cheat Sheet](Cheatsheet.md)
* [Rails Guide: Rails Routing](http://guides.rubyonrails.org/routing.html)
* [PragStudio - RubyOnRails Level 1](https://pragmaticstudio.com/rails). This is a **very** good resource for learning Rails. They have been teaching Rails since the beginning and their teaching and presentation skill are **excellent**.



