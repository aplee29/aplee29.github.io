---
layout: post
title:  jQuery Rails Restaurant App
date:   2017-01-12 00:58:39 -0400
---

Piggybacking on the previous [Rails app](http://aplee29.github.io/2016/10/22/rails_restaurant_app/), the fourth assessment requires students to implement jQuery on the front-end while communicating with the server using AJAX. Initially, I thought this would be an easy assignment since it adds to an existing application rather than creating one from scratch. It did come with its own difficulties, however, which will be discussed later in this post.

Below are the details outlining the project's requirements.

# Project Requirements
  1. Must render at least one index page (index resource - 'list of things') via jQuery and an Active Model Serialization JSON Backend.

  2. Must render at least one show page (show resource - 'one specific thing') via jQuery and an Active Model Serialization JSON Backend.

  3. The Rails API must reveal at least one has-many relationship in the JSON that is then rendered to the page.

  4. Must use your Rails API and a form to create a resource and render the response without a page refresh.

  5. Must translate the JSON responses into Javascript Model Objects. The Model Objects must have at least one method on the prototype.

# Introduction
Every component of the previous restaurant application worked in a discrete fashion. A user clicks on a link to load a new review form. After the form is completed, the user hits 'Submit', and he/she is then guided back to the restaurants' index page. All of these actions ended in a page reload or a redirect of some sort. Now, we can use a client-side technique called **AJAX** to retrieve data from the server and display the content without having to refresh the page. Requests are made to the **Rails API** via AJAX, which in turn, serializes the resource using *ActiveModel::Serializer* and responds back with **JSON**. The resulting JSON is subsequently parsed and added back to the DOM.

# Issues
Given the interconnected relationships among the different models, I found it often difficult to isolate the data to retrieve exactly what was needed.

I wanted to create a functionality that allows a user to "View" his/her own review from the their own welcome page without navigating to the review's show view. Clicking on the link triggers a GET request via AJAX, which retrieves the necessary data from the API and renders the JSON response. The main problem was figuring out how to get only the reviews written by a specific user. In other words, users should not be allowed to read reviews written by others.

```ruby
# app/controllers/users_controller.rb

class UsersController < ApplicationController

  def show
    if user_id_match
      @user = User.find_by(id: current_user.id)
      respond_to do |f|
        f.html { render :show }
        f.json { render json: @user }
      end
    else
      redirect_to root_path, alert: "Access denied."
    end
  end

  private

  def user_id_match
    params[:id].to_i == current_user.id
  end

end
```

To address this issue, I defined a separate method in the UsersController's `show` action to compare the id of the `current_user` with the id in `params`. If and only if both id's match can the user access the review.

For example, let's suppose that a user tries to manually access `/users/3.json` from the application. If `params[:id]` (in this case, 3) matches the `current_user`'s id, the JSON will be successfully rendered. Otherwise, the user is redirected to the root path, with the alert, "Access denied".

So far, this solved one of the problems, but we still have to figure out how to retrieve the reviews based on the current user. 

In order to do this, we have to bind certain attributes that will allow the app to correctly identify both the user and the review, just from the event of clicking on the "View" link.

```html
<!-- app/views/welcome/home.html.erb (Some parts of the code were omitted for simplicity) -->

<ol>
  <% current_user.reviews.each do |review| %>
    <li id="review"><strong><%= review.restaurant.name %></strong>: <a href="#" class="show_review" data-user-id="<%= current_user.id %>" data-review-id="<%= review.id %>">View</a></li>
    
    <div class="my_review" data-id="<%= review.id %>">
    </div>
  <% end %>   
</ol>

```

Here, we're using the `data-*` attributes for `user-id` and `review-id`. This is to ensure that we can correctly identify each id later on using jQuery. 

```javascript
// app/assets/javascripts/users.js

$(function() {
  $(".show_review").on("click", function(event) {
    var userId = $(this).data("user-id");
    reviewId = $(this).data("review-id");

    $.get("/users/" + userId + ".json", function(json) {
      var reviews = json["reviews"];
      var review = reviews.find(findReview);

      user = new User(review.rating, review.comment);
      user.showReview();
    })

    event.preventDefault();
  });
});

function findReview(review) {
  return review.id === reviewId;
}

function User(rating, comment) {
  this.rating = rating;
  this.comment = comment;
}

User.prototype.showReview = function() {
  var $div = $("div.my_review");
  $div.html("");
  $div.filter('[data-id="' + reviewId + '"]').html("<p>" + "<em>" + "Rating: " + "</em>" + this.rating + "</p>" + "<p>" + this.comment + "</p>");
}
```

Clicking on the "View" link (which is assigned the `.show-review` class) allows the app to call the `.data` method on `this`, which refers to the link itself, and save both the `userId` and `reviewId`. We are now able to retrieve information for the current user via an AJAX GET request, using the `userId`. The response from this request is the user's reviews in serialized JSON format. Since we are reading each review separately, we must find the review from the user's *reviews* array. The `findReview()` helper function helps us achieve that.

After finding the desired review, we create a new object `user` instantiated off of the User prototype object, passing in the `rating` and `comment` properties. The `.showReview` function is added to the prototype, which selects the <div> with the `data-id` attribute equal to the review and renders its `rating` and `comment` in the HTML.

# Final Comments
Please click [here](https://www.youtube.com/watch?v=NWPBbP68j0A) to view the walkthrough video of my Rails with jQuery application. 