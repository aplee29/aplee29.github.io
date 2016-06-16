---
layout: post
title:  "How do you approach working with Sinatra Basics?"
date:   2016-06-16 07:12:35 -0400
---

What I found most interesting about Sinatra was the way in which it was introduced into the curriculum. Sandwiched between *Rack* and *Rails*, Sinatra offers a solid introduction to creating simple, lightweight web applications. Web development can be an overwhelming project, but luckily, there are universally accepted standards that every web application should adhere to. For example, every web app should be able to provide routing services for handling client requests. Web frameworks are an excellent means of abstracting the common patterns and features of web applications, thereby allowing the developer to focus on defining the logic of the application rather than building each individual component of it.  

Sinatra is a Ruby-based domain-specific language (DSL) that is designed specifically for the purpose of creating quick web applications. It simplifies the process of web app creation by providing some bare functionalities and pre-defined methods to accomplish the job. Although much easier than having to create an entire web app from scratch, Sinatra does not offer many features outside of its basic package. The developer is still responsible for manual tasks such as generating files and directories, and for establishing routes to connect the various components of the application. This is one of the key concepts of MVC architecture, which also plays an indispensable role in larger frameworks such as Rails.

While all of this sounds great, you may be wondering, "Why Sinatra? Why can't I just start from Rails?" It would be similar to saying, "I know how to use my computer. Why should I care about things like CPU or memory?" It's true. An ordinary user probably would not have to know about his or her hardware to log into Facebook, check their e-mail, or download a music file. This is because all of the computer's functionalities are abstracted away for users, so that all they have to do is interact with the machine on a higher level. A computer mechanic, on the other hand, *should* know about the machine's anatomy. Understanding the computer on a lower level gives the mechanic greater control in things like diagnosing technical failures. So which role do programmers play in this scenario? Hands down the latter. Without knowing about the major components and moving pieces of the web application (via Sinatra), it would be difficult to fully appreciate the so-called *"magic"* of Rails.

## Basics of Sinatra

### `config.ru`
The first main file of a typical Sinatra web app is `config.ru`. This file is responsible for: (1) telling the Rack server about the app's environment requirements and (2) running the application defined in `app.rb`. 

### `app.rb`
This is the Controller file required by `config.ru` to execute the application. Simply put, a Controller defines the HTTP interface for the app. It utilizes **routes** to map requests that the client (browser) sends, and then renders the appropriate content back to the user. An example of an `app.rb` file is:

```ruby
class App < Sinatra::Base

  get '/hello' do
   "Hello world!"
  end

end
```

A **route** is composed of an HTML method (e.g. `GET` or `POST`) and its URL-matching pattern (its resource/path). After starting up the server (let's use Shotgun for this example), the user inputs `http://localhost:9393/hello` into the browser, and our application receives the request `GET /hello`. The request is subsequently matched to its corresponding controller route, and the application executes the code nested inside the action's block. In this case, the response "Hello world!" is returned and rendered to the user.  

This is just a very basic idea of Sinatra, and there are many other route patterns like `POST`, `PUT`, `PATCH`, and `DELETE`. Understanding the fundamental concepts behind Sinatra is a great way to prepare for the higher level features and functionalities seen in Rails.
