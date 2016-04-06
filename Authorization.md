![General Assembly Logo](http://i.imgur.com/ke8USTq.png)

## Objectives


* Update the User to have a first and last name.
* Allow a User to have the Admin role, needed for  authorization.
* Generate User Controller and views.
* Limit access to the User resource.
* Allow all access for an Admin user.


We are going to restrict access in this application. There are typically a large set of application behaviors that we want to restrict access.

In our case we:

1. Don't allow users to modify other user's accounts.
2. Allow Admin users the ability to modify any user's account.

We may also want to restrict access in other ways? Perhaps we only allow admin users the ability to delete reviews? Or we only allow a review creator to delete a review. This is a large topic that can be addressed in a number of different ways. 

For example, [Role Based Access Control (RBAC)](https://en.wikipedia.org/wiki/Role-based_access_control), is one methodology used to authorize users. 

There are a number of Ruby Gems one can add to an app to implement authorization. [Rails Authorization Gems](https://www.ruby-toolbox.com/categories/rails_authorization)

## Previous Lesson

[Authorization with Devise](Authorization.md)

## Source Code/Implementation

**Note: The implementation of this lesson is in the `authorization` branch of this repository**
[`movies_crud_app`](https://github.com/tdyer/movies_crud_app)

## Setup

Reset the DB. 

```bash
$ rake db:reset
...
``` 

## Update the User resource.

Let's allow a User to have a first and last name. 

```
$ rails generate migration AddNameToUser first_name last_name
$ rake db:migrate
```

> You can check this in the schema, dbconsole or rails console.

Create a virtual attribute for the User's full name. _A virtual attribute is just an attribute that act's like a database backed attribute._

```ruby
class User < ActiveRecord::Base
...
 # Create a virtual attribute for the full name                                                                       
  def full_name
    "#{first_name} #{last_name}"
  end

  def full_name=(fullname)
    self.first_name, self.last_name = fullname.split.map(&:strip)
  end
...
end
```

Let's also add a boolean attribute, admin, that will indicate if this user is an admim. _We'll use this later to restrict access._

```
$ rails g migration AddAdminToUsers admin:boolean
$ rake db:migrate
```


## Generate a controller and set of views for users.

```
$ rails g scaffold_controller User email first_name last_name admin:boolean --jbuilder=false
```

Add routes for users.

```ruby
  devise_for :users
   ...
  resources :users, except: [:new, :create]
  ...
```

_Notice, that we will be using the register user from Devise to create new users._

Cleanup the index view and remove new and create actions from the UsersController.

**Remove the new and create actions from the UsersController.**

**In the `app/views/users/index.html.erb` file remove the link to create create a new user.**

```html
<!-- Remove this line, we'll create a user via Devise registration. -->
<!-- 
<%= link_to 'New User', new_user_registration_path %>
--->
```

_Check out the User's in the browser_

### Seed Users.

Let's seed the DB with a four of Users, one User will be an Admin.

```ruby
...
User.delete_all
...

moe = User.create!(email: 'moe@foo.com', password: 'password', password_confirmation: 'password', first_name: 'Moe', last_name: 'Howard', admin: false)
larry = User.create!(email: 'larry@foo.com', password: 'password', password_confirmation: 'password', first_name: 'Larry', last_name: 'Fine', admin: false)
curly = User.create!(email: 'curly@foo.com', password: 'password', password_confirmation: 'password', first_name: 'Curly', last_name: 'Howard', admin: false)

# Create an admin user                                                                                                 
tom = User.create!(email: 'tom@foo.com', password: 'password', password_confirmation: 'password', first_name: 'Tom', last_name: 'Jones', admin: true)

puts 'Created a couple of Users'

```

```
$ rake db:seed
```

### Authorize Current User.

Let's have a User Story for authorization.

**Only a logged in user can modify their account.**

So, we must use a before filter in the UsersController to restrict the current user from updating or deleting other user's account information.

**In the `app/controllers/users_controller.rb`.**

Add a before filter after the set_user before filter.

```ruby
  before_action :set_user, only: [:show, :edit, :update, :destroy]

  # The logged in user can only modify their account.                                                                  
  before_action :allow_user_modification, only: [:edit, :update, :destroy]
...
  private
  # redirect unless the current user is modifying 
  # their account                                       
  def allow_user_modification
    unless current_user && (current_user.id == @user.id)
      flash[:error] = "#{current_user.email} can NOT modify the account for #{@user.email}"
      redirect_to users_url
    end
  end  
...
  
```

Let's create a better way to handle the flash in the layout.

```html
  <!-- Show flash messages -->
  <% flash.each do |name, msg| -%>
    <%= content_tag :div, msg, class: name %>
  <% end -%>
```

### Authorize an Admin.

Typically an application will have a user with the authorization to do anything. Typically this user is called a **super user** or **admin user**. We'll indicate that a user is an admin by setting the user's admin attribute/flag to true.

Here's our User Story:

**Allow an admin user the ability to modify any user's account.**

We just add a condition, `current_user.admin?` to the `allow_user_modification` filter.

```ruby
...
private

 def allow_user_modification
   unless current_user.admin? || current_user && (current_user.id == @user.id)
    ...
 end
...
```
We have seeded the DB with an admin user, `tom@foo.com`.

**Login as `tom@foo.com` and verify that we can update any user account.** 


## Next Lesson

[Many to Many Relationships](HasManyThrough.md)

## Resources
* [Role Based Access Control (RBAC)](https://en.wikipedia.org/wiki/Role-based_access_control)
* [Rails Authorization Gems](https://www.ruby-toolbox.com/categories/rails_authorization)
* [PragStudio Tutorial](https://pragmaticstudio.com/courses/rails-ii)
* [Rails Documentation](http://api.rubyonrails.org/)

