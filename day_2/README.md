#Rails Continued

## Authentication in Rails
- Authentication scheme is built in with `has_secure_password`.
- Rails uses Bcrypt out of the box.
- Opens up an `authenticate` method that returns a boolean upon checking plain text passwords against DB passwords.

##### Generating a User Model

```
rails g model User username:string password:string password_digest:string
```

##### Under the Hood
- `has_secure_password` will automatically do the following operations for us:

```
attr_reader :password

validates_presence_of :password, on: create
validates_presence_of :password_confirmation
validates_confirmation_of :password

# methods
authenticate(plain_text_password)
```

##### Testing the Authentication

- First, let's jump in rails console - rails c in terminal
- Let's create a new user
- Save the new user
- Test out the authenticate method on the user and pass in a correct and incorrect password and see what it returns
- Notice the fields for password and password digest. If you see a hashed password, you did something right!

## Session Hash

Your application has a session for each user in which you can store small amounts of data that will be persisted between requests. All session stores use a cookie to store a unique ID for each session (you must use a cookie, Rails will not allow you to pass the session ID in the URL as this is less secure).

Rails sets up a secret key used for signing the session data. This can be changed in config/initializers/session_store.rb and initializers/secrets.yml

You can access the session simply by using session[] so when a user successfully logs in we can assign session[:user_id] = @user.id assuming that @user is an instance of the User class that we authenticated successfully. We can now use this session[:user_id] in our code to ensure that only logged in users have access to certain pages. We can also use the session hash to ensure that only users with the current session id can view their personal information.

Here is an example of creating a user and immidiately logging them in with our session hash. Notice the use of an instance variable in this action. This is so that we can preserve form values if there is an issue submitting.

```
def create
	@user = User.create(user_params)
	if @user.save
		session[:user_id] = @user.id
		flash[:success] = "You are now logged in!"
		redirect_to students_path
	else
		render :signup
	end
end
```

When we log out a user, we assign any session keys to be nil. Here is an example:

```
def logout
	session[:user_id] = nil
	flash[:notice] = "Logged out"
	redirect_to login_path
end
```

## Flash Hash

The flash is a special part of the session which is cleared with each request. This means that values stored there will only be available in the next request, which is useful for passing error messages etc. It is accessed in much the same way as the session, as a hash (it's a FlashHash instance).

The easiest way to do this is by sending a message before a redirect (and less commonly before a render)

```
flash[:notice] = "You have successfully logged out."
```

The flash has has two default keys, notice and alert. We can include these any time after a redirect by passing it in as a second parameter like this:

```
redirect_to root_url, notice: "You have successfully logged out."
``` 

We can also create our own keys for the flash hash. Suppose we want something like flash[:success] we can either call it like this

```
redirect_to root_url, flash: { success: "Nice!" }
```

or

```
flash[:success] = "Nice!" 
redirect_to root_url
```

## Configure ActionMailer to Use Mandrill
- Mandrill is a mail as a service provider that allows you to send email much easier and better through a simple API.
- We will sign up for a free account [here](https://mandrillapp.com).
- We will add an initializer to configure our mail settings:

##### config/initializers/mailer_settings.rb

```ruby
ActionMailer::Base.delivery_method = :smtp
ActionMailer::Base.smtp_settings = {
	:port =>           '587',
    :address =>        'smtp.mandrillapp.com',
    :user_name =>      "username here",
    :password =>       "password here",
    :domain =>         'domain here',
    :authentication => :plain
}
```

## Using ActionMailer
- ActionMailer is a piece of software built into Rails that helps you send emails using the standard ERB template format we are used to.
- You can generate different mailers using the command line interface:

```
rails g mailer UserMailer
```

- You will now get a file that looks like a controller in your "mailers" folder. Each method you define here is a class method by default:

```ruby
class UserMailer < ApplicationMailer
	default from: "Arun Sood <arun.instructor@gmail.com>"

	def say_hello(name)
		@name = name
		mail(to: "test@test.com", subject: "Hello there!")
	end
end
```

- The way it works is that you will have views inside of views/user_mailer that have the same names as the method names in the mailer file. Here we have a file called say_hello.html.erb:

```ruby
<h1>Hello <%= @name %>!</h1>
```

- We can use this mailer in our controller whenever we need to send mail using this template:

```ruby
UserMailer.say_hello("Arun Sood").deliver_now
```

## Book Manager Lab Continued
- In this lab we will add an authentication component to our book manager lab.
- First we will implement has_secure_password and then we will ensure that a user gets an email once they sign up for an account.

## Model Validations

##### Validation methods

for all of these validations you can pass in an error message of - :message => "Something error related"

- validates_presence_of - attribute must not be blank
- validates_length_of - must have a length of x. Pass in a hash with (:is, :minimum, :maximum, etc.) 
- validates_numericality_of - attribute must be an integer or float (can pass in :equal_to)
- validates_inclusion_of - attribute must be in a list of choices 
- validates_exclusion_of - attribute must be in a list of choices 
- validates_format_of - attribute must match a regular expression (pass in :with)
- validates_uniqueness_of - attribute must be unique
- validates_acceptance_of - makes sure an attribute is accepted (a TOS is agreed)
- validates_confirmation_of - attribute must be confirmed (type in password twice). Rails will only run this if the attribute is set to something
- validates_associated - validates associated objects (validate options in associated relationships)
- can pass in (:on => :save/:create/:update) to specify when to validate
- can also pass in :if => :method / :unless => :method to help with determining when to validate (only check email if user wants to be identified by email)

[Full list of validations](http://guides.rubyonrails.org/active_record_validations.html)

##### Using validation methods

- Before you even start - think and ask yourself what should I validate?
- Add the validations after your associations in the model.

##### Validates Method

- shortcut for validations 
- wrap these all up in a hash

```
validates :COL_NAME, 
	presence: true, 
	length: {:maximum => 50}, 
	numericality: false, 
	uniqueness: true
```

##### In-class Validation Example

We set our validations in our app/models/user.rb model.

```
class User < ActiveRecord::Base
  validates_presence_of :first_name
  validates_presence_of :last_name
end
```

or

```
class User < ActiveRecord::Base
  validates :first_name, presence: true
  validates :last_name, presence: true
end
```

## Model Associations
- Associations allow us to create relationships between sets of data.
- For example, let's say we had two models - owners and pets. The association between pets and owners is that owners have many pets.
- Associations are handled in the model:

##### One to many

owner.rb

```
has_many :pets
```

pet.rb

```
belongs_to :owner
```

- Now you can accomplish things like `Owner.find(1).pets` to find all pets associated with the owner with an ID of 1.

##### Many to many
- Often times your data relationship will be a many to many relationship if both sets relate to multiple records in each other.
- For example, let's say we have a user model, an event model, and our application allows multiple users to have various events, and different events to have many users associated with them.
- Rails makes this association simple by a "join" table that links to two together.

user.rb

```
has_many :events, through: :rsvps
has_many :rsvps
```

event.rb

```
has_many :users, through: :rsvps
has_many :rsvps
```

rsvp.rb
- This third model acts as a bridge between the two models.
- This model will have two attributes: user_id and event_id.

```
belongs_to :user
belongs_to :event
```

## Chirp! Lab - The Next Big Thang
Chirp! is the newest social network that brings together all of the coolest aspects of socializing. Your mission should you choose to accept it is as follows:
- Use the files in the `chirp_html` folder as your frontend.
- Create a brand new Rails project that will take the input from the chirp textarea and save it as a chirp in the database.
- Create edit and delete functionality for the chirps.
- Each chirp shown should display a randomly chosen bird.
- Application should have at least two models - chirp and user, and should have authentication.
- Bonus: Practice using validations on both models.