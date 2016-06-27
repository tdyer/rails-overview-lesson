## Objectives

* Create a route to show a HTML form, used to create a movie.
* Create a ActionController#new action used to display this form.
* Create a View to generate this form.
* Create route to create a movie.
* Use Strong Parameters to create a movie.
* Use respond_to to generate multiple movie representations.


## Model, View, Controller (MVC)

Rails is based on the MVC Architecture.

![MVC](mvc_archi1.png)

## Previous Lesson
[Show a Movie](./ControllerShow.md)

## Source Code/Implementation

**Note: The implementation of this lesson is in the `movies_create` branch of this repository**
[`movies_crud_app`](https://github.com/tdyer/movies_crud_app)

## Setup

**Re-init the DB with the schema, seed data and start the app.**

```bash
$ rake db:reset
$ rails s
```

> The rake db:reset is a shorthand command that will totally re-create and re-initialize your DB. It will drop, `rake db:drop`, create, `rake db:create`, migrate, `rake db:migrate` and seed, `rake db:seed` your development DB.

## Generate a HTML Movie Form.

#### Create a route.


**In `config/routes.rb`**

```ruby
  # Route to generate a HTML form.
  # Form will be used to create a movie.                                    
  # MoviesController new action                                                           
  get '/movies/new', to: 'movies#new'
```
This will allow Rails to handle a HTTP GET request for the path `/movies/new`. This route **MUST** be above the show route in the routes.rb.

> This route for the new action MUST be above the route for the show action. The routing code will look at each route starting from the top of the routes.rb file until it finds a match.
> 
>So, if the path in the request is `/movies/new` and the route for the show action, `/movies/:id` is reached. The this will be be a match and try to invoke the MoviesController#show action. **Not Good**.
>
> The `/movies/:id` will match anything following the second backslash in the path. So it would see the string `new` in `/movies/new` as the `:id`!

We also have to modify the route for the show action.

```ruby
 get '/movies/:id', to: 'movies#show', as: 'movie'
```

We have to add the `as: 'movie'` to the end of route so we will be
able to use the `movie_path` and `movie_url` helpers in the upcoming form.

> Later we will see that routes not only match a request path and dispatch to a controller action. Routes also may generate a path or url helper method.

#### Create a new action.

**In `app/controllers/movies_controller.rb`.**

```ruby
 # GET /movies/new                                                                       
  def new
    # Create Movie object, need by the form_for helper                                    
    # in the view                                                                         
    @movie = Movie.new
  end
```

> The `form_for` helper method we use below will take a Movie model object as an argument. This helper method and the other helpers, i.e. text_helper, will attempt to fill in a HTML input `<input ...>` field using the Movie model attributes.
> 
> For example, It will fill in the HTML field with the name attribute of the movie. In this case, we'll pass a movie model with no attributes. So, the fields will be empty.

#### Create a view.

**In `app/views/movies/new.html.erb`**

```html
<header>
  <h1>Create a new Movie</h1>
</header>

<!-- Use a form_for helper to generate a HTML form tag -->
<%= form_for(@movie) do |f| %>
<fieldset>
  <ul>
    <li>
      <%= f.label :name %>
      <!-- Create text field for the movie name -->
      <%= f.text_field :name, autofocus: true %>
    </li>
    <li>
      <%= f.label :rating %>
      <!-- Create a select/dropdown the movie rating -->
      <%= f.select :rating, Movie::RATINGS.to_a.map { |r| [r, r] }, include_blank: true %>
    </li>
    <li>
      <%= f.label :description %>
      <!-- Create a text area for a movie description -->      
      <%= f.text_area :desc %>
    </li>
    <li>
      <%= f.label :length %>
      <!-- Create a number field for the length of the movie -->
      <%= f.number_field :length, min: 3, max: 300, value: 120 %>
    </li>
    <li>
      <%= f.label :released_year %>
      <!-- Create a select/dropdown for the movie released year -->
      <%= f.select :released_year, (1910..Date.today.year).to_a.map { |y| [y, y] }, selected: '1980'  %>
    </li>
  </ul>
  <p>
  	 <!-- Create a Submit Button -->
    <%= f.submit %><br/>
  </p>
</fieldset>
<% end %>
```

This "new" view will generate a HTML Form to create a Movie.

The [form_for](http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html) helper will generate a HTML form tag. It takes two arguments:

* A model. _In this case just an empty movie object/model._
* A block containing a set of helpers to generate form fields.

Each attribute of the movie is shown using a helper that will generate a specific type of text field or select field.

Let's take a look at the HTML that this `form_for` helper generates.

```HTML
<form class="new_movie" id="new_movie" action="/movies" accept-charset="UTF-8" method="post">
<input name="utf8" type="hidden" value="&#x2713;" />
<input type="hidden" name="authenticity_token" value="9hjZUU+99zKpJIyJsQvJU9QApZgjuVMz4juC8ewLH5j5bhw2JUu5q01bdECyL3k4Vur6r9psTyTHCp9Fu867uA==" />
```

Notice, that the first line is the HTML form tag generated.
 
* The `action="/movies"` determines the path submitted.
* The `method="post"` determines the HTTP method 'POST' will be used.

When we submit the form will will generate a HTTP 'POST' Request with a path of `/movies`. This POST request will also submit any data you've entered in the form's fields.


**URL Encoded Data sent in the body of the HTTP POST Request for '/movies'**

`... movie%5Bname%5D=Jaws&movie%5Brating%5D=G&movie%5Bdesc%5D=Fishy&movie%5Blength%5D=130&movie%5Breleased_year%5D=1977 ... `

#### Form Helpers

Helper methods are ordinary ruby methods that generate HTML. In this case they will generate HTML form fields.

_Each of these are links to the Rails documentation for the helper method._

* [f.text_field](http://apidock.com/rails/ActionView/Helpers/FormHelper/text_field)
* [f.text_area](http://apidock.com/rails/v4.2.1/ActionView/Helpers/FormHelper/text_area)
* [f.select](http://api.rubyonrails.org/classes/ActionView/Helpers/FormOptionsHelper.html#method-i-select)
* [f.number_field](http://apidock.com/rails/ActionView/Helpers/FormHelper/number_field)

The form helpers will invoke the movie model methods to get values for the form. _In this case there are no values for (name, rating,...)._



#### Create Movie Ratings.

**In the `app/models/movie.rb`**

Typically, we would use a Ruby constant for values that will NOT change
throughtout the life of the app. 

For example, we'll assume the Movie Ratings never change.

```ruby
class Movie < ActiveRecord::Base
  # Valid Movie Ratings
  RATINGS = ['G', 'PG', 'PG-13', 'R', 'NC-17']
end
```

## Create a Movie

Now, we must  handle the case where the Browser or a HTTP client is sending us data, via a HTTP POST request, that will be used to create a movie.

A Browser will from a string of data from the submitted form.

A HTTP Client, like curl, will submt data possibly in another format such as JSON.

#### Create a route to create a movie.

**In the config/routes.rb file.**

```ruby
  # Route a HTTP POST Request for movies to the                                           
  # MoviesController create action.                                                       
  post '/movies', to: 'movies#create'
```

This will route all HTTP POST request with a path of `/movies to the MoviesController#create action.

**In `app/controllers/movies_controller.rb`**

Create an action that will create a movie!

```ruby
	# POST /movies
  def create
  
    # Create a new movie from the params hash
    # (Note the movies_params is a method from below)
    @movie = Movie.new(movie_params)

    respond_to do |format| # Select HTML or JSON representation.

      if @movie.save  # Will return true if saved in DB.

        # save succeeded! Send a HTTP Redirect to the /movies/:id
        # where :id is the id of the movie just saved, ex: /movies/4.
        format.html { redirect_to movie_path(@movie.id), notice: 'Movie created' }

        # save succeeded! Send a HTTP status of 201 Created in the Response
        format.json { render :show, status: :created, location: @movie }

      else
        # save failed! show the form again.
        format.html {render :new}

        # save failed! show the json representation of the song errors.
        # Send a HTTP status of 422 Uprocessable Entity in the Response
        format.json {render json: @song.errors, status: :unprocessable_enitity }
      end
    end
  end

```
#### [Strong Parameters](http://edgeguides.rubyonrails.org/action_controller_overview.html#strong-parameters)

"..you'll have to make a conscious decision about which attributes to allow for mass update. This is a better security practice to help prevent accidentally allowing users to update sensitive model attributes.."


**At the end of `app/controllers/movies_controller.rb`**

```ruby
  private

  # Enforces strong parameter. Limit what attributes/columns can be set in the 
  # movies table.                                                                         
  def movie_params
  	 # require(:movie) - params hash MUST have a key with the symbol :movie
  	 # permit(...) - hash value for movie must have keys, :name, :desc, ...
    params.require(:movie).permit(:name, :desc, :rating, :released_year, :length)
   end

```

### Server Log for POST /movies

```
Started POST "/movies" for ::1 at 2016-03-14 09:53:40 -0400
Processing by MoviesController#create as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"y4kWpg21/okv/o2lg34TiPFVAvZ2M84PKC50fI\
u0VNvE/9PBZ0OwEMuBdWyAWqPjc79dwY/m0hgNH2nI3HHw+w==", "movie"=>{"name"=>"Relevant", "rating"=>"R", "desc"=>"harrowing", "length"=>"134", "released_year"=>"2015"}, "commit"=>"Create Movie"}
   (0.2ms)  begin transaction
  SQL (1.3ms)  INSERT INTO "movies" ("name", "desc", "rating", "released_year", "length", "created_at", "updated_at") VALUES (?, ?, ?, ?, ?, ?, ?)  [["name", "Relevant"], ["desc", "harrowing"], ["rating", "R"], ["released_year", 2015], ["length", 134], ["created_at", "2016-03-14 13:53:40.728673"], ["updated_at", "2016-03-14 13:53:40.728673"]]
   (1.3ms)  commit transaction
Redirected to http://localhost:3000/movies/6
Completed 302 Found in 9ms (ActiveRecord: 2.7ms)
```

Notice that the Parameters:, params hash, has a Hash that is OK with the movie_params method.

```
 Parameters: {.. "movie"=>{"name"=>"Relevant", "rating"=>"R", "desc"=>"harrowing", "length"=>"134", "released_year"=>"2015"}, ...}
```

And that we Redirect to /movies/6. Where 6 is the id of movie just created.

### URL encoded Data to Params Hash

Remember that the Form in the Browser submits URL encoded data for each form field.

`... movie%5Bname%5D=Jaws&movie%5Brating%5D=G&movie%5Bdesc%5D=Fishy&movie%5Blength%5D=130&movie%5Breleased_year%5D=1977 ... `

Rails tranforms this into a params hash.

`Parameters: {.. "movie"=>{"name"=>"Relevant", "rating"=>"R", "desc"=>"harrowing", "length"=>"134", "released_year"=>"2015"}, ...}`


## Next Lesson
[Update a Movie](ControllerUpdate.md)

## Resources
* [Rails Cheat Sheet](Cheatsheet.md)
* [Rails Guide: Rails Routing](http://guides.rubyonrails.org/routing.html)
* [PragStudio - RubyOnRails Level 1](https://pragmaticstudio.com/rails). This is a **very** good resource for learning Rails. They have been teaching Rails since the beginning and their teaching and presentation skill are **excellent**.



