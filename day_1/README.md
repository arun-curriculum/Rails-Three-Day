#Introduction to Ruby on Rails

##Install Software

####Install RVM

`\curl -sSL https://get.rvm.io | bash -s stable`

####Install Ruby

`rvm install ruby --disable-binary`

####Check Installation

`ruby -v`

####Install Rails

`gem install rails`

##The Ruby Language

- To practice using Ruby we can use the Interactive Ruby (IRB) tool.
- Everything is an object.
- Everything that is manipulated is an object, and the results of those manipulations are objects.
- In Ruby everything is an object and you'll be able to use object methods on it all.

Let's take a look at some examples:

```
puts "Arun Sood".length
puts "Arun".index("A")
puts 35.even?
```

- We can tell that everything is an object, because everything has a "class":

```
"Arun Sood".class #String
4.class #Fixnum
[].class #Array
Object.class #Class
```

- Something like this `message = "Hello World"` is essentially the equivalent of `message = String.new("Hello World")`.

- There is little conversion of type:

```
puts "Hello " + 4 + 2 #no implicit conversion of Fixnum into String
```

##Modules Versus Objects

####Modules
- Modules are like libraries.
- Usually used for containing a bunch of "helper" functions that you can call throughout your program.
- Great example is the [Math module](http://www.ruby-doc.org/core-2.1.4/Math.html) built into Ruby:

```
Math.sqrt(9) #3.0
```

####Objects
- Ruby is a proper object-oriented language.
- "Classes" wrap methods that are related to that object.
- Classes can also inherit from other classes - this is called extension. We will get to this later.

```
class Car
	def initialize(color, make, model)
		@color = color
		@make = make
		@model = model
	end
	
	def drive
		puts "We are now driving!"
	end
	
	def paint(new_color)
		@color = new_color
		puts @color
	end
	
	def describe_car
		puts "We are driving in the #{@color} #{@make} #{@model}"
	end
end

Car.new("Red", "Honda", "Civic").paint("Blue")
```

##Control Flow in Ruby
- Controlling the flow of your application is crucial because it allows you to perform actions only under certain conditions.
- Control flow often involves the use of "conditional" statements such as `if` - `else` blocks and the use of loops such as `for` and `while`.

####`if` statements

```ruby
yes =  true

if yes
	puts "Indeed!"
end
```

####`if` - `else` blocks:

```ruby
name = "Arun"
if name == "Bob"
	puts "Hello Bob"
elsif name == "John"
	puts "Hello John"
else
	puts "Hello Someone"
end
```

- Code inside a block is only executed when the condition is met.
- By condition being met, we mean that the statement returns `true`.

##Getting User Data in IRB
- Ruby has a method called `gets` that allows you to retrieve data from the user typing into the console.
- In order to prevent the input from receiving a new line each time you must call the `.chomp` method:

```
message = gets.chomp

num = gets.chomp.to_i
```

##In-Class Lab
- Let's create a simple calculator using the Ruby Math module.
- First, write two `gets` statements that will take 2 numbers from our user.
- Create a class called `Calculator` that will wrap all of our methods.
- Create an `initializer` method that takes in the 2 numbers and sets them as instance variables.
- Write at least 4 methods that perform different math operations and output the result using `puts`. You can reference them [here](http://www.ruby-doc.org/core-2.1.4/Math.html).
- Instantiate your class using each of these 4 methods.
- Bonus 1: Create a simple `if else` statement that will choose which method to pick based on a third `gets` input.
- Bonus 2: Create another method that uses one of the Math constants in your operation.

##Rails from the Rails People

"Ruby on Rails is an open-source web framework that's optimized for programmer happiness and sustainable productivity.

It lets you write beautiful code by favoring convention over configuration."

##What Rails Includes

Rails wraps in a bunch of functionality right out of the box:
- MVC programming pattern convention
- Templating engine with syntax nearly identical to ejs
- ORM called ActiveRecord which is assured to be much better than Sequelize.
- Support for developing APIs with token authentication, namespacing, and multiple response types.
- A database already wrapped in called SQLite3
- An asset pipeline for managing static assets like stylesheets, javascript files, and images.
- Asset "uglifier" or "minifier".
- SASS support
- CoffeeScript support
- Built-in testing suite called MiniTest.
- Authentication flow with Bcrypt and has_secure_password.
- Development web server called WEBrick.
- EASY PEEZY extension of functionality through community-supported gems.
- Oh and sessions... Yeah that's a big one!

##MVC Pattern Coding

####Models
- Models are where data interactions happen.
- You will often see logic related to pulling and saving to databases here.

####Views
- Views are what the users will actually see.
- It is the UI made dynamic through the templating engine.
- Views in Rails are served through layouts.

####Controllers
- Controllers bridge the gap between models and views.
- They take requests and do something with them.
- One controller can have many methods relating to that specific logical concern.

####Routes
- Like Node, routes take HTTP requests from a specific URL and hand them off to a specific controller and a specific method inside that controller.

##Rails Command Line Interface
- Allows you to create new projects.
- You can easily generate new components of your project.
- Easily destroy aspects of your application that you no longer need.
- Three tools we will see a lot:

Rails New:

```
rails new HelloWorld
```

Rails Generate:

```
rails generate controller Hello
```

Rails Destroy:

```
rails destroy controller Hello
```

Rails Console:

```
rails console
```

Rails Server:

```
rails server -p 3000
```

Rails DB:

```
rails db
```

RAKE:

```
rake db:migrate
```

##Hello World in Rails

What it's gonna take:
- A controller with a method
- A route
- A view
- Running server

Let's do it!

##Book Manager
- We will create a simple CRUD app practicing Rails with models.
- Before we implement the frontend UI we will perform the CRUD operations using Rails Console.
- Download the frontend files [here](book_library_html/).

##Asset Pipeline
- Rails wraps in a software called Sprockets that allows you to include multiple static assets in your application.
- Sprockets also handles the SASS and CoffeeScript interpretation as well as the uglifier.
- You can read about Sprockets [here](https://github.com/rails/sprockets-rails).

##Using the Asset Pipeline
- For the basic implementation, all that is needed is for you to place any stylesheets, javascripts, and images in their appropriate folders under app/assets.
- For stylesheets, you can find the asset require code in app/assets/stylesheets/application.css:

```
*= require_tree .
*= require_self
```

- For javascripts, you can find the asset require code in app/assets/javascripts/application.js:

```
//= require turbolinks
//= require_tree .
```

- You will also find a folder called `vendor` at the root of your application. This folder is where you should keep any code written by third parties such as jQuery or Bootstrap.
- To make this work however you will have to alter your application.css and application.js files a bit:

application.css

```
*= require_tree ../../../vendor/assets/stylesheets/
```

application.js

```
//= require_tree ../../../vendor/assets/javascripts/
```