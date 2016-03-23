![General Assembly Logo](http://i.imgur.com/ke8USTq.png)

## Objectives

* Dry up Controller with a before filter/action.

## Previous Lesson
[ActiveRecord Validations](./ActiveRecordValidations.md)

## Source Code/Implementation

**Note: The implementation of this lesson is in the `controller_misc` branch of this repository**
[`movies_crud_app`](https://github.com/tdyer/movies_crud_app)

## Setup

**Re-init the DB with the schema, seed data and start the app.**

```bash
$ rake db:reset
$ rails s
```

## [Before Filter/Action](http://guides.rubyonrails.org/action_controller_overview.html#filters).


> Filters are methods that are run before, after or "around" a controller action.

> Filters are inherited, so if you set a filter on ApplicationController, it will be run on every controller in your application.

>"Before" filters may halt the request cycle. A common "before" filter is one which requires that a user is logged in for an action to be run. You can define the filter method this way"

**In the `app/controllers/movies_controller.rb`**

```ruby
class MoviesController < ApplicationController

  # Use a before action to reduce duplicate code in the below actions.                    
  # Specifically, getting the Movie by id from the DB.                                    
  before_action :set_movie, only: [:show, :edit, :update, :delete]
  
  ...
  
  private

  # Used by multiple actions above.                                                       
  def set_movie
    @movie = Movie.find(params[:id])
  end

...  
```

This will create a **before action** that will run before the show, edit, update and delete actions. It will retrieve the movie from the DB and set the instance variable, `@movie` to this movie model.

**Now you can remove this duplicate code from each of these actions!!**

And test of course.



## Next Lesson
[Active Record Has Many Relationships](ARHasMany.md)

## Resources
* [Rails Cheat Sheet](Cheatsheet.md)

* [PragStudio - RubyOnRails Level 1](https://pragmaticstudio.com/rails). This is a **very** good resource for learning Rails. They have been teaching Rails since the beginning and their teaching and presentation skill are **excellent**.



