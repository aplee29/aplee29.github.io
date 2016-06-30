---
layout: post
title:  My First Ruby CLI Gem
date:   2016-06-14 00:58:39 -0400
---

I am proud to announce that I have officially completed my very first CLI gem using Object-Oriented Ruby! Like many other students, I was initially intimidated about creating an entire project from scratch. To help me get through the first stages of coder's block, I watched the walkthrough videos provided by Learn, which turned out to be an immense help. 

Below are the details outlining the project's requirements, as well as the procedures that were undertaken in completing this gem.

# Project Requirements
  1. Package as a gem.

  2. Provide a CLI on gem installation.

  3. CLI must provide data from an external source, whether scraped or via a public API.

  4. Data provided must go at least a level deep, generally by showing the user a list of
     available data and then being able to drill into a specific item.

# Getting Started
As someone who cannot live without her daily cup of Java, I chose to use the **Eight O'Clock Coffee** homepage as the basis for my project. The website has two main components:

  1. The "main" page where all the coffees are organized by *theme*. Each theme has a *name* 
     and a *description*.

  2. Individual pages show the *description*, *tasting notes*, *roast*, and *available 
     types* of each coffee.

I decided to build my gem based on the above layout. The following outlines the basic CLI interface: 

  1. Welcomes the user.

  2. A list of themes is displayed.

  3. User chooses a theme.

  4. Based on the selected theme, a list of related coffees is shown.

  5. User chooses a coffee.

  6. Details regarding the selected coffee appear.

  7. Prompt asks if user wants to continue or exit.

  8. Program ends (if exit) or starts up again (if continue).

Once I laid out the foundation for my CLI in pseudocode, I began to implement the structure of the gem. Running the command `bundle gem eight_oclock_coffee` in Terminal created all the necessary files and folders for the gem. After some additional environment modifications, I was now ready to work. 

# The Models
To implement the logic for the program, I defined three classes: Theme, Coffee, and Scraper. Each class has a specific, exclusive responsibility. For example, the Theme class is solely responsible for creating Theme instances, which holds data about each theme, as well as the coffees related to it. The Coffee class creates instances of each coffee, which stores various properties such as its *roast* and *description*. The Scraper class uses Nokogiri to scrape all the contents from the webpages, such as the theme name, theme description, coffee name, coffee URL, etc.

# Issues

**Update:** Following the assessment, I was able to not only refactor a majority of the code but also resolve the troublesome issue of associating coffees to their themes.

```ruby
def self.scrape_themes # Scrapes main page
  @doc = Nokogiri::HTML(open("http://www.eightoclock.com/coffee-varieties"))
  @doc.css(".headingVarity .sfContentBlock").each do |theme_div|
    theme = EightOclockCoffee::Theme.new

    theme.theme_name = theme_div.css("h3 span").text.upcase
    theme.theme_desc = theme_div.css("p").text
    theme.coffees = []

    # Scrapes names and URLs of each coffee on main page
    @doc.css(".varitieslists div").each do |coffee_div|
      # e.g. Checks if theme name is included in the coffee's id attribute (coffee belongs to the theme)
      if coffee_div.attributes["id"].value.downcase.include?(theme.theme_name.downcase)
        coffee = EightOclockCoffee::Coffee.new

        theme.coffees << coffee

        coffee.coffee_name = coffee_div.css("a").attr("title").value
        coffee.coffee_url = "http://www.eightoclock.com#{coffee_div.css("a").attr("href").value}"
      end
    end
  end
end
```

The refactored method instantiates a new Theme object, assigns it a name (*theme_name*) and description (*theme_desc), and then creates a new array for pushing in each subsequent Coffee object. This is a much cleaner and more efficient means of reproducing the Theme-Coffee relationship, as opposed to the former method of creating a mixed array, converting it into a hash, etc. Now that we scraped the entire main page and saved all its contents, I was able to refactor the following scraper method.

```ruby
def self.scrape_coffees(url) # Scrapes individual coffee page
  @doc = Nokogiri::HTML(open(url))
  @doc.css(".productDescription").each do |coffee_info|
    coffee_name = coffee_info.css("h2").text.strip
    
    # Finds the existing coffee instance by name
    coffee = EightOclockCoffee::Coffee.find_by_name(coffee_name)

    coffee.coffee_desc = coffee_info.css("p").text.strip.split("\r")[0]
    coffee.tasting_notes = coffee_info.css(".subblock p").text.strip.split("\r")[0]
    coffee.roast = coffee_info.css(".subblock p").text.strip.split("\r")[1].strip

    availabilities = []
    @doc.css(".subblock ul li").each do |availability|
      availabilities << availability.css("img").attr("alt").value unless availability.attributes["style"].value == "display:none;"
    end
    coffee.available_in = availabilities.join(", ")
    coffee.save
  end
end
```

Here, I defined a new class method `.find_by_name`, which takes the `coffee_name` that is scraped from the individual coffee's page, and looks for the instance with that name from the Coffee class's @@all array. Once the matching coffee has been found, additional properties such as the description (*coffee_desc*), tasting notes (*tasting_notes*), roast (*roast*), and available types (*available_in*) are assigned to the object.

# Final Comments
Please click [here](https://www.youtube.com/watch?v=RjRYueu_ozE) to view the walkthrough video of my CLI gem. 