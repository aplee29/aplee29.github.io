---
layout: post
title:  Rails Restaurant App
date:   2016-10-22 00:58:39 -0400
---

The third assessment requires building a Ruby on Rails application that manages data through complex forms and RESTful routes. Trying to avoid some of the "cliche" domains seen repeatedly throughout the curriculum, I decided to base my domain on a simple restaurant application. Users can submit ratings and reviews for some of their favorite (or not-so favorite) restaurants, and they can also search for other people's reviews on a particular restaurant.

Below are the details outlining the project's requirements.

# Project Requirements
  1. Use the Ruby on Rails framework.

  2. Your models must include a `has_many`, a `belongs_to`, and a `has_many :through` relationship.

  3. The join model must store an additional user submittable attribute (not just timestamps or auto-created fields) describing the relationship.

  4. Your models should include reasonable validations for simple attributes.

  5. You must include at least one class-level ActiveRecord scope method.

  6. You must include a nested form that writes to an associated model through a custom attribute writer.

  7. Your application must provide a standard user authentication, including signup, login, logout, and passwords.

  8. Your authentication system should allow login from some other service, e.g. Facebook, Twitter, Foursquare, Github, etc.

  9. You must make use of a nested resource with the appropriate RESTful URLs. Additionally, your nested resource must provide a form that relates to the parent resource.

  10. Your forms should correctly display validation errors.

  11. Your application must be, within reason, a DRY Rails app.

# Getting Started
My restaurant application contains four model classes: **User**, **Review** (Join Model), **Restaurant**, and **Category**. The following chart illustrates the relationships among them. 

| **User**             | **Review**      | **Restaurant** | **Category** | 
| -------------------- | --------------- | -------------- | ------------ |
| `name`               | `rating`        | `name`         | `name`       |
| `email`              | `comment`       | `city`         |              |
| `encrypted_password` | `user_id`       | `state`        |              |
| `provider`           | `restaurant_id` | `address`      |              |
| `uid`                |                 | `category_id`  |              |
| *etc...*             |                 |                |              |

The following associations can be drawn:

  1. A **User** has many **Reviews**.

  2. A **User** has many **Restaurants** through **Reviews**.

  3. A **Review** belongs to a **User**. (has the foreign key `user_id`)

  4. A **Review** belongs to a **Restaurant**. (has the foreign key `restaurant_id`)

  5. A **Restaurant** has many **Reviews**.

  6. A **Restaurant** has many **Users** through **Reviews**.

  7. A **Restaurant** belongs to a **Category**. (has the foreign key `category_id`)

  8. A **Category** has many **Restaurants**. 

Attributes such as `email` and `encrypted_password` are provided by the **Devise** gem. **Devise** helps to establish a full-fledged user authentication system, allowing users to register an account and log in and out of the app. A password encryption feature is also provided, which eliminates the task of having to manually implement an encoding mechanism.

The `provider` and `uid` properties are defined as part of **Omniauth**, which is a Rails gem that enables someone to log into the application via a third party service provider, such as Github or Facebook. This is seen as a more robust and desirable alternative to relying on our own database to store encrypted passwords and authenticate users.

# Issues
The main issue I came across was deciding on the layout for my app. In terms of functionality, users should be able to write, edit, and delete their own reviews, as well as read reviews written by other users. *But how do we associate a review to a restaurant?* It would not make sense for a user to create a restaurant and then write the review every single time. Instead, users *indirectly* generate a new restaurant through the review form.

```ruby
# app/views/reviews/new_restaurant_review.html.erb (Actual code has been refactored using partials)

<%= form_for(@review) do |f| %>
  
  <%= f.fields_for :restaurant do |restaurant_field| %>

    <%= restaurant_field.label :name %>
    <%= restaurant_field.text_field :name, size: 30, autofocus: true %>

    <%= restaurant_field.label :address %>
    <%= restaurant_field.text_field :address, size: 80 %>

    <%= restaurant_field.label :city %>
    <%= restaurant_field.text_field :city %>

    <%= restaurant_field.label :state %>
    <%= restaurant_field.text_field :state, placeholder: "ex. NY", size: 5 %>

    <%= restaurant_field.label :category_id %>
    <%= restaurant_field.collection_select :category_id, Category.all, :id, :name, prompt: true %>
    
  <% end %>

  <hr>

  <p>Select a rating for this restaurant. 0 (poor) - 5 (great).</p>
  <%= f.number_field :rating, label: "Rating", min: 0, max: 5, size: 5, autofocus: true %>

  <p>Add a comment.</p>
  <%= f.text_area :comment, label: "Comment", size: "30x10" %>

  <%= f.submit %>

<% end %>
```

Here, we are using a nested form to associate the restaurant model to the new review using the `fields_for` helper. This allows all of the restaurant's attributes (i.e. `name`, `address`, `city`, `state`, and `category_id`) to be nested under a key of `review` inside the params hash. The last two attributes, `rating` and `comment`, correspond to the review itself, and are the only inputs that are shown when a user writes a review for a restaurant that already exists.

In order to prevent a user from creating multiple identical restaurants, the user must first select an existing restaurant from a list. An additional defensive measure was added by defining a custom writer method in the Review model called `restaurant_attributes=`.

```ruby
# app/models/review.rb (Some parts have been omitted from the actual code.)

class Review < ActiveRecord::Base
  ...

  def restaurant_attributes=(restaurant_attributes)
    self.restaurant = Restaurant.find_or_create_by(name: restaurant_attributes[:name]) do |r|
      r.address = restaurant_attributes[:address]
      r.city = restaurant_attributes[:city]
      r.state = restaurant_attributes[:state]
      r.category_id = restaurant_attributes[:category_id]
    end
  end

  ...
end
```

Using the values collected from posting our nested form, the application checks to see if the restaurant with the specified `name` has already been persisted to the database. If it has, the restaurant object is returned without saving a duplicate. If it's not, the app will create the new restaurant using the given properties. As an example, let's say that a user writes a new review for "Burger King". He is completely unaware that this fast food restaurant has already been created, and he proceeds to enter all of its details. Once the form has been submitted, however, the application will ignore all the restaurant information provided by our user and instead, simply add his review to the existing restaurant.

# Final Comments
Please click [here](https://www.youtube.com/watch?v=U_CO_DXH2bQ) to view the walkthrough video of my Rails application. 