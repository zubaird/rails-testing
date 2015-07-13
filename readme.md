Intro to Testing
=======

**Objectives**
 - Warm up: List and discuss pros and cons of TDD for web apps (think about your last project)
 - Students will make a new Rails app, and install RSpec.
 - Students will learn to write their first Rails test.
 - Understand basic terminology used in the testing world.
 - Students should know what is TDD and BDD.


-----------------

Warm up
-----------------
-pros
-cons


Create a new Rails app called friendface, and install RSpec.
-----------------

**Let's set everything up! (do NOT copy and paste!)**

*Terminal:*

    $ rails new friendface -TBd postgresql

    $ cd friendface

    $ rake db:create

*Gemfile:*

    group :development, :test do
      .  .  .
      gem 'rspec-rails', '~> 3.0'
    end

*Terminal:*

    bundle

    rails g rspec:install

[RSpec About](http://rspec.info/about/)

 **Side note:** Benefits to testing in Rails? Rails and testing is like peanut butter and jelly.  TDD in Rails is relatively easy and Rails gives you extremely helpful error messages. From a workflow standpoint, you can make sure your app works as intended without having to frequently check the browser.  Also, I would argue that Ruby itself is a much easier language to write tests for.  

Write your first Rails test
-----------------

Tests can be written for models, views, controllers, features, and more.  

For now we'll start with model tests and by the end of this lesson, students should be able to implement has_many, belongs_to, and check validations on an ActiveRecord model using test driven development.

In the app we just created we want users and posts.  We want to make sure that our users are correctly associated with many posts.  We also want to make sure that posts are associated with a user.  Along the way we'll write tests to ensure some basic validations for users and posts.  

Generate a User model spec, *terminal:*

     $ rails g rspec:model user

> What happens if we typed this command in twice?

Notice in terminal the following lines show up:

    invoke    rspec
    create    spec/models/user_spec.rb

The first line is just saying that rails is using rspec commands.  The second line is a little more helpful and shows you the path of the spec file.


Now take a look inside the spec file that was generated

*spec/models/user_spec.rb:*

    require 'rails_helper'

    RSpec.describe User, type: :model do
      pending "add some examples to (or delete) #{__FILE__}"
    end

There's some test code already generated for us just to show us where we should write our tests.  We don't need to follow that 'RSpec.describe...' syntax and we can go ahead and delete those three lines.  Instead, we'll use the more familiar RSpec 'describe' and 'it' blocks.  

We want to test that for the User model, its 'username' attribute cannot be left blank.  First start with the describe block.

*spec/models/user_spec.rb:*

    require 'rails_helper'

    describe 'User' do

    end

Now in terminal, type rspec to see your spec run:
(make sure you're in your app directory!)

*Terminal:*

    $ rspec

What do you see? The 'describe' block itself does nothing, it is only a containter to provide helpful context for your tests.

Remove the quotes around 'User' in the user_spec.

*spec/models/user_spec.rb:*

    require 'rails_helper'

    describe User do

    end

Run the spec again, *Terminal:*

    $ rspec

> What is RSpec looking for in this example?  

> What are constants in Ruby?

In this case, RSpec is not giving you a red failing test because the test is not able to run in the first place.  However, the error that it gives you is just as helpful, and it is literally telling you what you need to fix. You must get comfortable reading error messages!

Let's get the test running by creating a user.rb file in app/models/user.rb.  

>Run rspec in terminal again, what do you see?

Add the User model class in user.rb and make sure it inherits from ActiveRecord::Base

**app/models/user.rb:**

    class User < ActiveRecord::Base

    end

Run rspec in terminal and see that the spec can now run!

Let's write a test to see that users have a username.

> If we did not have RSpec, or any testing framework, how would we check that the User model has a username attribute? What are the steps invovled?

One way to look at it, is that we should have the ability to call the username method on a user anywhere in our code:

*example:*

    user.username
    #=> "Erlich"

Add an 'it' block that creates a user and checks to see that there is a response when user.username is called.

*spec/models/user_spec.rb:*

    it 'responds to username' do
      user = User.create

      expect(user).to respond_to :username
    end


> What is **'respond_to'**? It is an RSpec matcher.

> A list of RSpec matchers and more information about them can be found on the Relish documentation.
> [RSpec matchers](https://www.relishapp.com/rspec/rspec-expectations/v/3-3/docs/built-in-matchers)
>
> RSpec matchers go after 'expect().to' or 'expect().not_to'. This
> example uses the 'include' matcher:
>
>     expect(ice_cream).not_to include "anchovies"
>
> There are over a dozen matchers, and each one has different ways of
> handling or even receiving arguments.  It is worth referencing the
> documentation if you are using a matcher you're unfamiliar with.


All together it should look like this, *spec/models/user_spec.rb:*

    require 'rails_helper'

    describe User do
      it 'responds to username' do
        user = User.create

        expect(user).to respond_to :username
      end
    end

Now for the fun part, watch it fail!
*Terminal:*

    $ rspec

> What does the error say?

> How can we add a table to our database in Rails?

*Terminal:*

    $ rails g migration CreateUsers

> Run rspec in terminal, what does it say now?

*Terminal:*

    rake db:migrate

> Run rspec in terminal again, and we have a new message! What is it saying?

*Terminal:*

    $ rails g migration AddUsernameToUsers username:string

*Terminal:*

    $ rspec


Notice again, we need to run 'rake db:migrate' to apply the new migration we generated.  But first, take look at the schema.rb file and notice that the username column is not there yet either.  

Now run the migration *Terminal:*

    $ rake db:migrate

run rspec *Terminal:*

    $ rspec

**Green!**

![high-five](http://media.giphy.com/media/9o67upvAnOqRy/giphy.gif)

Now we want to add a validation to make sure that the username can not be blank.  

Add the following to *specs/models/user_spec.rb:*

    describe 'username' do
      it 'can not be blank' do
        user = User.create(username:"")

        expect(user).to_not be_valid
      end
    end

This should be placed within the 'describe User do...' block.  Your user_spec.rb file should look like this:

    require 'rails_helper'

    describe User do

      it 'responds to username' do
        user = User.create

        expect(user).to respond_to :username
      end

      describe '#username' do
        it 'can not be blank' do
          user = User.create(username:"")

          expect(user).to_not be_valid
        end
      end

    end

> What do you see when you run rspec in terminal?

## Exercise pt. 2

> Write a test for validating the presence of e-mail

## Exercise pt. 3

> Write a test for validating that username is 2 or more characters

## Exercise pt. 4

> Make all the tests pass.
> For the last test, you can refer to [Active Record Length Validations](http://edgeguides.rubyonrails.org/active_record_validations.html#length)


Refactoring our test
-----------------

Right now, have this code in our *spec/models/user_name_spec:*

    it 'responds to username' do
      user = User.create

      expect(user).to respond_to :username
    end

We can refactor this test to make it more readable by using braces instead of 'do end', and utilizing the is_expected method.

    it { is_expected.to respond_to :username }

Now we have this wonderful one-liner!  Unlike 'expect(user)' , is_expected does not take user as an argument.  The subject 'user' is inferred from when we said 'describe User do...'

> Do you notice any other redundancy in the user_spec.rb file?  What is being repeated?

We can use 'subject' to DRY up our code.



Basic Terminology
-----------------
You may encounter these terms on the job:

 **Unit tests -**  Low level tests to check functionality of classes, methods, or functions.  So far we have been running unit tests on small examples using ***Jasmine*** or ***RSpec***. Unit tests run fast!

 **Acceptance/Feature tests -** High level tests conducted to make sure all requirements are met.  In Rails we will use the testing tools ***RSpec*** and ***Capybara*** to run acceptance tests.  Acceptance tests run slow!

**Integration / functional / service-level testing -** Testing between unit and acceptance tests.  In most cases this will involve testing RESTful APIs.  Don't worry about this one too much right now!

> - What is a RESTful API?
> - Is it better to have more unit tests or feature tests?

**User Story - ** Plain English description of what the user does and why.

> User stories follow formats such as:
> As a **[role]** I want **[feature]** so that **[benefit]**
> As an **admin** I want to be able to **modify everyone's profile** so that I can **ensure consistency**

![testing pyramid](http://blog.codeclimate.com/images/posts/rails-testing-pyramid.png)
(source:http://blog.codeclimate.com/images/posts/rails-testing-pyramid.png)

----------

## TDD / BDD ##

The **Test-driven development** flow involves:

 1. Write the test
 2. Watch it fail
 3. Write some code
 4. Watch it pass
 5. Refactor your code
 6. **Repeat!**

   ![Write tests!](http://cdn.meme.am/instances/250x250/61920411.jpg)

> **What is BDD?**
> BDD is TDD, but with an approach that is intended to facilitate writing the right tests.  This means writing tests that help fulfill a certain feature or behavior.
> http://guide.agilealliance.org/guide/bdd.html

**Build from the 'outside in' - ** is a BDD technique that we apply heavily in Rails.  'Outside' refers to the intended outcome or high level goal.  This is what you should have in mind when writing your examples!

> **What is an 'example' in BDD?**

----------

## Types of tests in Rails ##

In Rails you will see the following types of tests:

 - Testing **Models**  -  These tests run fast, you will use them to test your model's behavior

 >  (Post model) ***'spec/models/post_spec.rb'***

 - Testing **Controllers** - Fast and useful for testing things like if an action is rendered again on some condition
>  (Post controller) ***'spec/controllers/posts_controller_spec.rb'***

 - Testing **Views** - Useful for checking conditional logic in views, i.e. what to display if a user is an admin instead of a non-admin.  

 >   (Post index view) ***'spec/views/posts/index.html.erb_spec.rb'***

 - Testing **Features** - High level testing where you simulate a user

 >   (User following users) ***'spec/features/user_follows_another_user_spec.rb'***


----------
## Capybara ##

Go to your Gemfile:
<pre>group :development, :test do
  .  .  .
  gem 'rspec-rails', '~> 3.0'
  gem 'capybara'
end

![Application Testing With Capybara](http://ecx.images-amazon.com/images/I/51-50592deL._SX411_BO1,204,203,200_.jpg)


## What does Capybara do for you? ##
**Capybara** is a DSL wrapped in a ruby gem that lets you test your web applications by simulating a real world user.  

> What is a DSL?


Capybara runs *inside* of your RSpec tests.
