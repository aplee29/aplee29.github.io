---
layout: post
title:  "What are some of the key concepts in Rails Review: TodoMVC 1?"
date:   2016-07-14 12:20:29 +0000
---

TodoMVC is an open source project that provides various implementations of a to-do app in several frameworks. Using a vanilla JavaScript example as a template, the objective is to re-build a modified version of the application using Rails and JavaScript. 

Here are some of the main concepts presented in Part 1 of this lecture series.

## Asset Pipeline
Here, we integrate the CSS from the TodoMVC main template page (`index.css`) into our **Asset Pipeline** so that we have the base theme to work with. The CSS code is then pasted into a new file in `todomvc-base.css`. In the `application.css` file, entering the line `*= require 'todomvc-base'` will point to the `todomvc-base.css` file. 

## Controller
Executing `rails generate resource list name` in Terminal stubs out a new **model** and **controller** for our *list* resource. We must then configure our router file, `config/routes.rb`, by adding the line `root 'lists#index'`. This routes the root of our website by creating a *lists* **controller** with an *index* **action**. The line `resources :lists` is automatically added to the file by our Rails generator, which auto-generates the seven RESTful routes of our *list* resource. In the **controller** file, `lists_controller.rb`, we define our *index* action. Here, Rails implicitly renders `lists/index`, where `lists` is the **controller** and `index` is the **action**.

```ruby
def index
  @list = List.new
  @lists = List.all
end
```

To create a *show* **action** for the **ListsController**, we add the following method:

```ruby
def show
  @list = List.find(params[:id])
end
```

Here, *List* is the ActiveRecord model, which reads the value of the **params** hash with the key of *id*. `:id` is what's known as a **route variable**, which identifies the primary key of the list to be loaded via the URL that is submitted by the user. 

## form_for
In order to facilitate the process of creating forms in Rails (which can be quite tedious if done manually), we can use the **form_for** helper method to generate the forms for us. When used in conjunction with an instance of the ActiveRecord model, **form_for** provides a *form builder* (yielded to the block as |f|), which can generate input fields based on the object. In `index.html.erb`:

```ruby
<%= form_for(@list) do |f| %>
  <%= f.text_field :name, :class => "new-todo" %>
  <%= f.submit :style => "display: none" %>
<% end %>
```

Let's examine the following line in more detail: `<%= f.text_field :name, :class => "new-todo" %>`

If we were to examine the webpage in the browser, the line shown above would be represented (in HTML) as: `<input class="new-todo" type="text" name="list[name]" id="list_name">`. The *name* attribute is very important because it shows where in the **params** hash the user's input is stored. By convention, the value for our form fields can be accessed through `params[:list][:name]`. 

## Mass Assignment & Strong Parameters
Using **mass assignment** in a Rails application can pose a serious security vulnerability. In order to thwart the possibility of a user trying to manipulate the form's data, we must sanitize the parameters and enable them to be explicitly assigned. This can be accomplished through **strong parameters**. 

In order to implement **strong parameters** in a Rails app, it is conventional to define a *private method* at the bottom of the controller.

```ruby
def list_params
  params.require(:list).permit(:name)
end
```

The **params** hash *require*s a key called `:list`. Within that key, it also *permit*s a key called `:name`. 

Now, to incorporate this method into our *create* action:

```ruby
def create
  @list = List.new(list_params)
  @list.name = params[:list][:name]
  @list.save

  redirect_to list_url(@list)
end
```

By passing in the sanitized version of the parameters, it ensures that outside visitors cannot change the data in the **params** hash. 
