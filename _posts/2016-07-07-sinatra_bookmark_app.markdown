---
layout: post
title:  Sinatra Bookmark App
date:   2016-07-07 00:58:39 -0400
---

For the second assessment, I created a simple Content Management System, a.k.a. a CRUD (Create Read Update Delete) app. 

Below are the details outlining the project's requirements, as well as the procedures that were undertaken in completing this gem.

# Project Requirements
  1. Build an MVC Sinatra Application.

  2. Use ActiveRecord with Sinatra.

  3. Use Multiple Models.

  4. Use at least one `has_many` relationship.

  5. Must have user accounts. The user that created a given piece of content should be the only person who can modify that content.

  6. You should validate user input to ensure that bad data isn't created.

  7. Any validation failures must be shown to the user with an error message.

# Getting Started
For this project, I based the domain model on a bookmark application, which contains three classes: **User**, **Topic**, and **Site**. The following chart illustrates the relationships among the three classes. 

| **User**          | **Topic** |  **Site**  | 
| ----------------- | --------- | ---------- |
| `username`        | `name`    | `name`     |
| `password_digest` | `user_id` | `url`      |
|                   |           | `topic_id` |

The following associations can be drawn:

  1. A **User** has many **Topics**.

  2. A **User** has many **Sites** through **Topics**.

  3. A **Topic** belongs to a **User**. (Has the foreign key `user_id`)

  4. A **Topic** has many **Sites**.

  5. A **Site** belongs to a **Topic**. (Has the foreign key `topic_id`)

Since there was no initial framework to build off of, preparing an association map in advance proved to be very helpful. This is also useful when creating migrations for each table of the database, where each class represents a table. 

The `password_digest` column stores an encrypted (salted and hashed) version of the user's password. Basically, this encryption technique makes it difficult for an outsider to deliberately decode the password. 

# Building the Application
The core of the application is the `config.ru` file, which is the execution point of any Rack-based application, including this very Sinatra app. It loads the `environment.rb` file, which defines the app's environment. Additionally, all controllers are mounted in the `config.ru` file. Controllers are responsible for directing incoming requests to the appropriate responses via their routes. 

The `Gemfile` lists all of the gems the application relies on for execution, which can be installed into the system by running `bundle install`.

The `app/` directory houses all of the files and folders that define the structure of the web application. Known as the MVC (**Models**, **Views**, and **Controllers**) framework, each component is responsible for a specific set of tasks (*separation of concerns*) and interacts with other components in a defined manner.

# Issues
The main issue I came across was making sure that *only* the user that creates a topic or site can view and modify the content. After some code tweaking, I decided on the following solution:

```ruby
# app/controllers/topics_controller.rb

get '/topics/:id' do
  @topic = current_user.topics.find_by(id: params[:id])
  if @topic
    erb :'topics/show'
  else
    flash[:message] = "You aren't authorized to view this topic."
    redirect '/topics'
  end
end

get '/topics/:id/edit' do
  @topic = current_user.topics.find_by(id: params[:id])
  if @topic
    erb :'topics/edit'
  else
    flash[:message] = "You aren't authorized to edit this topic."
    redirect '/topics'
  end
end
```

Both routes rely on the return value of the `current_user` method call. `current_user` is a helper method which is defined in the Application Controller. 

```ruby
def current_user
  @current_user ||= User.find(session[:user_id]) if session[:user_id]
end
```

This method finds and returns the user whose ID is stored in the session hash. In other words, it finds the currently logged in user and saves it to the `@current_user` instance variable. 

Calling `.topics` on `current_user` allows us to access the current user's topics (made possible through the `has_many :topics` association defined in the User model). From the list of topics, we find the topic by its ID, which is taken from the user's URL input via dynamic routing. Essentially, we are trying to ascertain whether the topic was actually created by (belongs to) the current user. If so, we load the topic's show/edit page. If not, a message is shown, which warns the user that he or she cannot view/edit the topic. 

Likewise, I was able to apply a similar technique in returning only the sites created by the logged in user. 

```ruby
# app/controllers/sites_controller.rb

get '/sites/:id' do
  @site = current_user.sites.find_by(id: params[:id])
  if @site
    erb :'sites/show'
  else
    flash[:message] = "You aren't authorized to view this site."
    redirect '/sites'
  end
end

get '/sites/:id/edit' do
  @site = current_user.sites.find_by(id: params[:id])
  if @site
    erb :'sites/edit'
  else
    flash[:message] = "You aren't authorized to edit this site."
    redirect '/sites'
  end
end
```

Here, we are able to access `.sites` for `current_user` because of the `has_many :sites, through: :topics` association that was defined in the User model. This method call not only looks for all sites belonging to the user, but it also implicitly checks whether or not the user is logged in in the first place.

# Final Comments
Please click [here](https://www.youtube.com/watch?v=_RevN_La2qs) to view the walkthrough video of my Sinatra application. 