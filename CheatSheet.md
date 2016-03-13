## Cheatsheet

* Create a new rails app.

```
$ rails new movies_crud_app -T 
```

* Create the DB for this app.

```
$ cd movies_crud_app
$ rake db:create
```

* Run the rails app on localhost port 3000.

```
$ rails server
```

_Should see the Welcome Page at `http://localhost:3000`_

* Create a movie migration and model.

```
$ rails generate model Movie name:string rating:string desc:text length:integer
```

* Create a `movies` table in the DB, i.e. run the migration.

```
$ rake db:migrate
```

* Create seed data in the movies table. _Add code above to the `db/seed.rb` file._

```
$ rake db:seed
```

* Check the current DB schema in `db/schema.rb`.
* Check the data in the movies table.

```
$ rails dbconsole
> SELECT * FROM movies;
...
> 
```

* Use the Rails console to find data using the Movie ActiveRecord class/model.

```
$ rails console
> m1 = Movie.first
> m2 = Movie.find(2)
> mlast = Movie.last
> all_movies = Movie.all
> all_movies[2]
> Movie.find_by_name("Mad Max")
> Movie.find_by_rating("R")
> Movie.where(rating: "R")
```

