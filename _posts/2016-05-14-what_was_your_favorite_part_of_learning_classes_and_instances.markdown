---
layout: post
title:  What was your favorite part of learning Classes and Instances?
date:   2016-05-14 02:38:29 -0400
---

My favorite part about this segment was being able to **abstract** the code instead of relying on its literal representation. If we were to draw an analogy, the literal representation requires drivers to know everything that goes on under the hood of a car, including all of the engine's components. An abstraction of this low level model, on the other hand, is simply turning the vehicle's ignition from the driver's seat. We are not required to know all the nitty-gritty details of *how* a car starts running from its ignition point; rather, we only need to focus on being able to start and drive the car. In the programming world, this abstraction is a key feature in **object-oriented programming** (OOP), as opposed to the lower level procedural programming paradigm explained earlier. 

## Procedural Programming
Procedural programming relies on procedures to operate on data. The procedures and the data operate discretely. Let's consider the following example:

```ruby
patrasche = String.new("Patrasche")
nello = String.new("Nello")

def name(dog)
  dog
end

def bark(dog)
  "Woof!"
end

def my_owner(dog, owner)
  "My owner is " + owner + "."
end

puts name(patrasche)
  #=> "Patrasche"

puts bark(patrasche)
  #=> "Woof!"

puts my_owner(patrasche, nello)
  #=> "My owner is Nello."
```

So far, everything seems to work fine. Running the above code in a Ruby interpreter produces all the intended results without error. However, there are some points to consider here. First, the syntax may seem a bit awkward to the novice programmer, and does not reflect the logic seen in natural English. Based on the method call, `puts my_owner(patrasche, nello)`, users will not immediately know which is the owner and which is the dog (assuming that they aren't already familiar with *A Dog of Flanders*). Most likely, they will have to refer back to the previous method definition for clarification. Secondly, we have to constantly pass the `dog` and `owner` arguments to each method. This is a very cumbersome process and is easily prone to coding errors. In addition, since each method/procedure is separate from the data (our two variables), it's difficult to grasp the relationship between these two elements. What if we were to add more dogs and owners to this example? This entire program would inevitably turn into a programming nightmare, one that is not only buggy and inefficient, but also reveals all the literal details of the code to the outside world. It's almost like leaving the hood of a car open with each component disassembled, leaving helpless drivers scratching their heads. 

## Object-Oriented Programming
So how should we improve our previous model? In OOP, there are data structures called **objects** that encapsulate the data and behaviors (methods) into its own data structure. Each object, therefore, is responsible for managing and operating on its own data. Objects are instantiated from a **class**, which is both the *blueprint* that defines an object's behaviors and attributes, as well as the *factory* that produces new individual objects, or **instances**. Each instance holds an **internal state**, or the data and properties that are stored in instance variables. In order to expose the internal data (stored in instance variables) to the outside world, we must call instance methods on those objects. 

Here is the same code as in the previous example, but translated into OOP format:

```ruby
class Dog
  attr_accessor :name, :owner
  
  def initialize(name)
    @name = name
  end
  
  def bark
    "Woof!"
  end
  
  def my_owner
    "My owner is " + self.owner + "."
  end
end

patrasche = Dog.new("Patrasche")
patrasche.owner = "Nello"

puts patrasche.name
  # => "Patrasche"

puts patrasche.bark
  # => "Woof!"

puts patrasche.my_owner
  # => "My owner is Nello."
```

Let's now analyze the merits of this new approach. 

Syntactically, our new code is much clearer and logically organized. First, we define the Dog class with all of our methods and data. Next, we instantiate a new Dog object called `patrasche` and assign its owner. Our `patrasche` instance object now has access to all of the instance methods defined within the Dog class. This is extremely powerful in that it not only produces cleaner readability, but it also establishes a transparent relationship between the object and its properties. 

Now, we can ask the object directly for information, such as its `#name` and `#owner`. This contrasts with our procedural model where we had to call each method individually while passing in the same argument(s) over and over again. Moreover, compared to our procedural example where we couldn't immediately tell apart the owner from its dog (`puts my_owner(patrasche, nello)`), the OOP syntax (`puts patrasche.my_owner`) explains the relationship much more lucidly. This is an incredible advantage over the previous model, since it reduces confusion when creating additional dogs and owners, and can accommodate future program enhancements and changes. 

**Bonus:** Let's take it one step further and add the following instance method to our Dog class definition: 

```ruby
  def get_adopted(new_owner)
    self.owner = new_owner
  end
```

If we call our newly added `#get_adopted` instance method on our `patrasche` instance:

```ruby
patrasche.get_adopted("John")

puts patrasche.my_owner
  # => "My owner is John."
```
 
Now, we see that Patrasche's new owner is *John*. After the `#get_adopted` instance method call, the `#owner` setter method was invoked on the object itself (via `self`), which assigned the `@owner` instance variable equal to the name of the new owner. Thus, calling the `#my_owner` instance method on the `patrasche` object (`puts patrasche.my_owner`) returns the string value `"My owner is John."`

OOP allows a program to abstract away all the fine details that the user does not need to know. Instead, it focuses on clarity and understandability of the program's logic and syntax. 
