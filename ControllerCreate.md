![General Assembly Logo](http://i.imgur.com/ke8USTq.png)

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
[Show a Movies](./ControllerShow.md)

## Source Code/Implementation

**Note: The implementation of this lesson is in the `movies_create` branch of this repository**
[`movies_crud_app`](https://github.com/tdyer/movies_crud_app)

## Setup

**Re-init the DB with the schema, seed data and start the app.**

```bash
$ rake db:reset
$ rails s
```

## Create a HTML From to create a Movie.

#### Create a route.

> Note: this should be ABOVE the route for the show action.

**In `config/routes.rb`**

```ruby
  # Route a to generate a HTML form to create a movie.                                    
  # MoviesController new action                                                           
  get '/movies/new', to: 'movies#new'
```

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

#### Create a view.

**In `app/views/movies/new.html.erb`**

```html
<header>
  <h1>Create a new Movie</h1>
</header>

<!-- Use a form_for helper to generate a HTML form -->
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
    <%= f.submit %><br/>
  </p>
</fieldset>
<% end %>
```

This "new" view will generate a HTML Form to create a Movie.

The [form_for](http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html) helper will generate a HTML form. It takes two arguments:

* A model. _In this case just an empty movie object/model._
* A block containing a set of helpers to generate fields.

Each attribute of the movie is shown using a helper that will generate a specific type of text field or select field.


#### Form Helpers

Helper methods are ordinary ruby methods that generate HTML. In this case they will generate HTML form fields.

* [f.text_field](http://apidock.com/rails/ActionView/Helpers/FormHelper/text_field)
* [f.text_area](http://apidock.com/rails/v4.2.1/ActionView/Helpers/FormHelper/text_area)
* [f.select](http://api.rubyonrails.org/classes/ActionView/Helpers/FormOptionsHelper.html#method-i-select)
* [f.number_field](http://apidock.com/rails/ActionView/Helpers/FormHelper/number_field)


#### Create Movie Ratings.

**In the `app/models/movie.rb`**

```ruby
class Movie < ActiveRecord::Base
  # Valid Movie Ratings
  RATINGS = ['G', 'PG', 'PG-13', 'R', 'NC-17']
end
```

## Add an action, MoviesController#create, that will create a new movie.

#### Create a route to create a movie.

**In the config/routes.rb file.**

```ruby
  # Route a HTTP POST Request for movies to the                                           
  # MoviesController create action.                                                       
  post '/movies', to: 'movies#create'
```

This will route all HTTP POST request with a path of `/movies to the MoviesController@create action.

**In `app/controllers/movies_controller.rb`**

Create an action that will create a movie!

```ruby
	# POST /movies
  def create
    # Create a new movie from the params hash
    # (Note the movies_params is a method from below)
    @movie = Movie.new(movie_params)

    # Dispatch to the code that willbuild the correct Resource Representation.
    respond_to do |format|

      if @movie.save  # Will return true if saved in DB.

        # save succeeded! Send a HTTP Redirect to the /movies/:id
        format.html { redirect_to movie_path(@movie), notice: 'Movie created' }

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

  # Enforces strong parameter. Limit what attributes/columns can be et in the 
  # movies table.                                                                         
  def movie_params
    params.require(:movie).permit(:name, :desc, :rating, :released_year, :length)
   end

```

## Lab

## Next Lesson
[Create a Movie](ControllerCreate.md)

## Resources
* [Rails Cheat Sheet](Cheatsheet.md)
* [Rails Guide: Rails Routing](http://guides.rubyonrails.org/routing.html)
* [PragStudio - RubyOnRails Level 1](https://pragmaticstudio.com/rails). This is a **very** good resource for learning Rails. They have been teaching Rails since the beginning and their teaching and presentation skill are **excellent**.



