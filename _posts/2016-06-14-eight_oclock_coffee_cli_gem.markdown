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
One of the biggest hurdles I faced in this project was associating coffee objects to their respective themes. It was difficult to replicate this relationship using the scraped data. Scraping was a tricky task that required a lot of precision. If I chose CSS selectors that weren't accurate or specific enough, I would end up scraping extra content that I didn't want or need. 

To work around this issue, I first instantiated a new object for each theme, and assigned values to its name (*theme_name*) and description (*theme_desc*) attributes using the scraped data. "Saving" the new Theme objects via `theme.save` allowed the objects to be stored in the Theme class's @@all array.

Within the same @@all array, I also wanted to be able to store all the coffee objects that were associated to the particular theme. Somehow, I had to find that link within the HTML itself. Upon closer inspection, I was able to find the CSS selector for each coffee, which included the name of the theme in string format. Using Pry, I ran `@doc.css(".varitieslists div").first.attributes["id"].value`, which yielded the string `varitieBlock1_C032_rptBeginnings_liBeginnings_0`. Bingo!

Now, all I had to do was clean up the string so that it returned something like `BEGINNINGS`. I was able to successfully associate each coffee to its theme. After instantiating a new Coffee object, I assigned its name (*coffee_name*), URL (*coffee_url*), and theme name (*theme_name*), and saved each coffee instance to the Theme's @@all array.

```ruby
def scrape_themes 
  @doc = Nokogiri::HTML(open("http://www.eightoclock.com/coffee-varieties"))
  @doc.css(".headingVarity .sfContentBlock").each do |theme_div|
    theme = EightOclockCoffee::Theme.new

    theme.theme_name = theme_div.css("h3 span").text.upcase
    theme.theme_desc = theme_div.css("p").text
    theme.save

    @doc.css(".varitieslists div").each do |coffee_div|
      # e.g. Checks if theme name is included in the coffee's id attribute (coffee belongs to the theme)
      if coffee_div.attributes["id"].value.downcase.include?(theme.theme_name.downcase)
        coffee = EightOclockCoffee::Coffee.new

        coffee.coffee_name = coffee_div.css("a").attr("title").value
        coffee.coffee_url = "http://www.eightoclock.com#{coffee_div.css("a").attr("href").value}"
        coffee.theme_name = coffee_div.attributes["id"].value.split("_")[-2].split("li")[-1].upcase
        coffee.save_to_theme
      end
    end
  end
  EightOclockCoffee::Theme.divide_coffees_by_theme
end
```

Now, when we introspect on the Theme's @@all array, it returns an array of Theme and Coffee objects in successive order. However, I was looking for a hash format, with keys that represent the names of each theme and values that represent the objects that are associated with that theme. 

```ruby
def self.divide_coffees_by_theme
  @@all_by_theme << @@all.group_by{|element| element.theme_name}
end
```

This method converts the @@all array to the format described above. 

Fortunately, scraping each individual coffee's page was much simpler. Based on the user's theme and coffee selections, I implemented the following Scraper method to retrieve the desired results: 

```ruby
  def scrape_coffees(url) 
    @doc = Nokogiri::HTML(open(url))
    @doc.css(".productDescription").each do |coffee_info|
      coffee = EightOclockCoffee::Coffee.new

      coffee.coffee_name = coffee_info.css("h2").text.strip
      coffee.coffee_desc = coffee_info.css("p").text.strip.split("\r")[0]
      coffee.tasting_notes = coffee_info.css(".subblock p").text.strip.split("\r")[0]
      coffee.roast = coffee_info.css(".subblock p").text.strip.split("\r")[1].strip

      availabilities = []
      @doc.css(".subblock ul li").collect do |availability|
        availabilities << availability.css("img").attr("alt").value unless availability.attributes["style"].value == "display:none;"
      end
      coffee.available_in = availabilities.join(", ")
      coffee.save
    end
  end
```

This creates a new Coffee instance, and assigns the relevant attributes using the scraped data. The final portion of the CLI uses the newly created coffee object to print out its details to the user.

# Final Comments
Please click [here](https://www.youtube.com/watch?v=RjRYueu_ozE) to view the walkthrough video of my CLI gem. 

Lastly, I plan to publish the gem after reviewing and refactoring the code with a Learn instructor. 