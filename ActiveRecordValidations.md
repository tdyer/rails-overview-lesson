![General Assembly Logo](http://i.imgur.com/ke8USTq.png)

## Objectives

* Validate Model Attributes.
* Checking for a valid model.
* Working with Validation Errors.
* Displaying Error Messages.


## Previous Lesson
[Delete a Movie](./ControllerDelete.md)

## Source Code/Implementation

**Note: The implementation of this lesson is in the `movies_validations` branch of this repository**
[`movies_crud_app`](https://github.com/tdyer/movies_crud_app)

## Setup

**Re-init the DB with the schema, seed data and start the app.**

```bash
$ rake db:reset
$ rails s
```

## ActiveRecord Validations

We can constrain the values allowed for a Model's attributes. Another words, we can **validate** that an attribute can **ONLY have a valid value**.

This is best seen with a couple of examples.

#### Validate [Presence Of](http://guides.rubyonrails.org/active_record_validations.html#presencepresence)

**In the movie model.**

```ruby
class Movie < ActiveRecord::Base
  RATINGS = ['G', 'PG', 'PG-13', 'R', 'NC-17']

  # validate that this movie has a name                                                   
  validates :name, presence: true

end
```

This will validate that the movie has a name, _seems reasonable to me_.

> The ActiveRecord#validate method can take a couple of arguments. Typically, a set of symbols representing the attributes to validate followed by a hash that constrains the values for these attributes. 
> 
> Better understood after some examples.

#### Checking for a [Valid Model](http://guides.rubyonrails.org/active_record_validations.html#valid-questionmark-and-invalid-questionmark).

We can check that a model is valid using the `valid?` and `invalid?` ActiveRecord methods. 

[ActiveRecord#valid?](http://apidock.com/rails/ActiveRecord/Validations/valid%3F)

**Start the Rails console**

```ruby
> m1 = Movie.first
> m1.valid?
true
> m1.invalid? 
false
``` 

Yes, the movie is valid because it has a name.


#### Showing [Errors](http://guides.rubyonrails.org/active_record_validations.html#working-with-validation-errors).

ActiveRecord models have a an `errors` method that returns an instance of [ActiveModel::Errors](http://api.rubyonrails.org/classes/ActiveModel/Errors.html)

This `errors` object behaves like a Ruby Hash were you can show error messages. _Let's have a look at it._

**In the Rails console.**

```ruby
> m1.errors
#<ActiveModel::Errors:0x007f9839c266d0 @base=#<Movie id: 1, name: "Affliction", rating\
: "R", desc: "Little Dark", length: 123, created_at: "2016-03-13 02:30:45", updated_at: "\
2016-03-13 02:30:45", released_year: 1997>, @messages={}>
>
> m1.errors.messages
{}
>
```

The interesting piece above is the messages hash. It will contain the error messages for you model instance. _Let's take a look._


But, first we'll make the movie invalid by setting the name to an empty string.

```ruby
> m1.name = ""
> m1.valid?
false
> m1.errors.messages
{:name=>["can't be blank"]}
>
```
See how we made the movie invalid and showed the error message. _Pretty cool._

For an invalid model the messages hash will have an **Array of messages** for each **invalid** model attribute.

## More ActiveRecord Validations

Let's create some more validations.


```ruby
class Movie < ActiveRecord::Base

  # validate that this movie has a name                                                   
  validates :name, presence: true
  # name must be between 2 and 30 characters                                              
  validates :name, length: {minimum: 2, maximum: 30}
  # name must unique!                                                                     
  validates :name, uniqueness: true
end
```

Here we assert that a name must be have at least 2 characters but not more than 30 characters.

And that there can NOT be movies with the same name! _We'll fix this later._

**In the rails console.**

```ruby
> m1 = Movie.first
> m1.name = "B"
> m1.valid?
false
> m1.errors.messages
{:name=>["is too short (minimum is 2 characters)"]}
> m1.errors[:name]
["is too short (minimum is 2 characters)"]
>
>
```

Lets check the uniqueness of the name in the model.

```ruby
> mbad = Movie.create(name: 'Affliction', rating: 'PG-13', desc: 'aaa', length: 105, released_year: 2001)
(0.1ms)  rollback transaction
...
> mbad.errors[:name]
["has already been taken"]
```

Not too sure about this validation? Can't we have remakes of old movies? Yes.

```ruby
  ...
  # name must unique within a specific year!                                              
  validates :name, uniqueness: {scope: :released_year }
  ...
```

Now we can check this in the console.

**Validate the rating.**

```ruby
  # validate that the rating must be one of values in the                                 
  # RATINGS constant.                                                                     
  validates :rating, inclusion: { in: RATINGS, message: "%{value} is not a MPAA rating" }
```

**Check it in the console.**

```
> mbad = Movie.create(name: 'Jaws', rating: 'foo', desc: 'a', length: 105, released_year: 2001)   
> mbad.errors.message
> {:rating=>["foo is not a MPAA rating"]}
> 
```

**Validate the movie length and released year**

```ruby
  ...
  # validate that the length of the move is between 3 and 300 minutes.                    
  validates :length, numericality: {greater_than: 2, less_than: 301 }

  # validate the released year                                                            
  validates :released_year, inclusion: { in: 1910..Date.today.year, message: "%{value} is not a valid release year, it must be between 1910 and #{Date.today.year}" }
 ...
```
## Error Handling in the views

In order to view ActiveRecord Errors in the views we'll add the below to the Form partial `app/views/movies/_form.html.erb`

```
<% if @movie.errors.any? %>
<div id="error_explanation">
  <h2><%= pluralize(@movie.errors.count, "error") %> prohibited this movie from b\
    eing saved:</h2>

  <ul>
    <% @movie.errors.full_messages.each do |message| %>
    <li><%= message %></li>
    <% end %>
  </ul>
</div>
<% end %>
```

If there are errors, it will display the number of errors and the descriptions of the errors.


## Next Lesson
[Controller Misc](ControllerMisc.md)

## Resources
* [Rails Cheat Sheet](Cheatsheet.md)

* [PragStudio - RubyOnRails Level 1](https://pragmaticstudio.com/rails). This is a **very** good resource for learning Rails. They have been teaching Rails since the beginning and their teaching and presentation skill are **excellent**.



