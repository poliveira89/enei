# ENEI Workshop

This guide assumes that you already have a Ruby on Rails environment. We will also assume that you are using **Rails 4.0.3** and at least **Ruby 1.9.3**. This is just a "getting started" tutorial and there are many aspects of a Rails application that will not be covered.

## Get to know the tools

Here is the list of preferred software for this workshop:

* **Text editor** — Sublime Text ([http://www.sublimetext.com/2](http://www.sublimetext.com/2))

* **Terminal** — Where you start the Rails server and run commands. You must get used to terminal (or you can use iTerm instead).

* **Web browser** — Your backoffice will be visible on one of these. Preferably Chrome.

* **REST client** - Where you can send the requests to the API — [Cocoa REST Client](http://mmattozzi.github.io/cocoa-rest-client/)

## Rails MVC

Before moving forward it's important that you get used to Rails architecture. [MVC](http://en.wikipedia.org/wiki/Model%E2%80%93View%E2%80%93Controller) is a software architecture, or a pattern used in software engineering, seeking to separate content, presentation, and behavior.

![](https://github.com/marcric/ror3gangsclock/wiki/general_mvc.png)

#### Controllers

Controllers are the conductors of an MVC application they are in the front line accepting requests from the outside world, meaning user input.

They perform the necessary processing, call the appropriate functions on model objects if necessary, and through the actions that live inside it, passes control to the view layer to render the results in whatever format you want.

In Rails **Action Controller** does most of the groundwork for you and uses smart conventions to make everything as straightforward as possible.

#### Views

Views are responsible for the visible part of the application or what the user actually sees. It can be HTML markup to be displayed in a browser but could be XML, JavaScript, JSON, or any other.

#### Models

Models are the representation of the objects (users, personal data, friendships, etc.), involved in the application so called "business logic".
While we call the entire layer "the model", Rails applications are usually made up of several individual models, each of which (usually) maps to a database table.

In Rails the models are all descendants of **Active Record**. Active Record facilitates the creation and use of business objects whose data requires persistent storage to a database. It is an implementation of the Active Record pattern which itself is a description of an Object Relational Mapping (ORM) system.

## Creating the application

Today we will be creating a simple web application for you to manage your daily tasks and your TODO list. This application will be able to support different users, having each one its own set of tasks.

You can call it whatever you want but from now on we will call it `todo`.

First open your terminal type the `rails new` command to create your new project and change current directory:

```
$ rails new todo
$ cd todo
```

If you `ls` on the newly created project you can see that Rails creates a lot of files and directories for you. Ruby on Rails follows a philosophy of **convention over configuration** so every piece of code you'll code it's a change do the default Rails behaviour rather than a configuration of a new project.

Here's a short list of the directories and files of most importance:

| File/Folder | Purpose                                                       |
| ------------|---------------------------------------------------------------|
| app/        | Contains the controllers, models, views, helpers, mailers and assets for your application. You'll focus on this folder for the remainder of this guide. |
| bin/        | Contains the rails script that starts your app and can contain other scripts you use to deploy or run your application. |
| config/ | Configure your application's runtime rules, routes, database, and more. |
| db/ | Contains your current database schema, as well as the database migrations. |
| Gemfile Gemfile.lock | These files allow you to specify what gem dependencies are needed for your Rails application. These files are used by the Bundler gem. For more information about Bundler, see the [Bundler website](http://gembundler.com/) |
| lib/ | Extended modules for your application. |
| log/ | Application log files. |
| public/ | The only folder seen to the world as-is. Contains the static files and compiled assets. |
| Rakefile | This file locates and loads tasks that can be run from the command line. The task definitions are defined throughout the components of Rails. Rather than changing Rakefile, you should add your own tasks by adding files to the lib/tasks directory of your application. |
| test/ | Unit tests, fixtures, and other test apparatus. |
| tmp/ | Temporary files (like cache, pid and session files)
| vendor/ | A place for all third-party code. In a typical Rails application, this includes Ruby Gems and the Rails source code (if you optionally install it into your project). |

Your application is ready to run as soon as you create it! Type `rails server`. This will fire up WEBrick which is the default Rails web server and runs on port 3000. You can open `http://localhost:3000` and check that the server is running and serving your `todo` application!

You should see the default "Welcome aboard" page. You can stop the server now (`ctrl+C` on the terminal). We just have to customize the behaviour of that default application.

## Changing the default behaviour

We will be using the `rails` command a lot. If you want type `rails` with no arguments inside your application to check what you can do. Go ahead and type `rails generate`. This command is used to generate different things like controllers, models, etc.

> **Note**: every command has a shortcut. `rails generate` can be written as `rails g`, `rails server` can be written as `rails s` and so on.

Let's add a simple view changing that welcome screen to something different. You can see that on `app/controllers` you already have an `application_controller.rb`. Every controller that you add will inherit from this one.

Create a new controller for the welcome screen:

```
rails g controller welcome index
```

We are telling Rails to generate a new controller called **WelcomeController** and having only an **index** action.

Rails controllers will have the default **CRUD** actions:

* **C**reate — `create` action
* **R**ead — `show` action
* **U**pdate — `update` action
* **D**elete — `destroy` action

The file — `welcome_controller.rb` is living under `app/controllers/`. You should have this content:

```ruby
class WelcomeController < ApplicationController
  def index
  end
end
```

> **Note**: you should be aware of the Rails conventions. One of them is that the file names should have "snake_case_names" that match "CamelCaseNames" classes.

So we created the class `WelcomeController` that inherits from `ApplicationController` (the use of `<` denotes that `ApplicationController` is the superclass of `WelcomeController`). Inside we only have one method called `index` which is empty.

You can now open the `config/routes.rb` file, where you can define which URLs will be available on your application. The file has a lot of examples but no routes configured yet. Edit the file to add a "root" url (the one that will be seen when you go to `localhost:3000/`):

```ruby
Todo::Application.routes.draw do
  root 'welcome#index'
end
```

Now we told Rails that our newly created **WelcomeController#index** is the root method that should be called.

> **Note**: in Ruby it's common to see the `<Class>#<method>` notation and in routes we use a similar notation when configuring which controller and which method should be called.

Fire up your WEBrick again and if you go to your localhost address you should see the default view for that method. It tells you can find the view in  `app/views/welcome/index.html.erb`.

Rails always tries to use the `<method>.html.erb` on the correct directory if you don't tell otherwise. When we generated the controller the command also generated this file for you.

The `.erb` extension is because Rails uses **Embedded RuBy** as the default templating system for HTML output. We won't be playing much with ERB since the application will use JSON to comunicate with the mobile app.

## REST Resources

Rails heavily encourages the use of resources. A resource is the term used for a collection of similar objects, such as posts, people or animals. You can do the CRUD operations on items for a resource. In Rails this resources are also standard REST resources.

REST stands for Representational State Transfer. It relies on a stateless, client-server, cacheable communications protocol — and the HTTP protocol is used.

It is an architecture style for designing networked applications. The idea is that, rather than using complex mechanisms such as CORBA, RPC or SOAP to connect between machines, simple HTTP is used to make calls between machines.

RESTful applications use HTTP requests to post data (create and/or update), read data (e.g., make queries), and delete data. Thus, REST uses HTTP verbs (mainly: GET, POST, PUT and DELETE) for all four CRUD operations.

Rails provides a resources method which can be used to declare a standard REST resource. Here's how config/routes.rb will look like.

## Scaffolding of resources

Rails scaffolding is a quick way to generate some of the major pieces of an application. If you want to create the models, views, and controllers for a new resource in a single operation, scaffolding is the tool for the job.

Let's start and create a new resource that we call `User` and that will represent the users of our application. For now the users have only one field for the full name:

```
rails g scaffold User name
```

A lot of things were created with a single command!

#### Migrations

One of the files created with the above command is what we call a **migration**.

Migrations are a convenient way to alter your database schema over time in a consistent and easy way. They use a Ruby DSL (Domain Specific Language) so that you don't have to write SQL by hand, allowing your schema and changes to be database independent.

You can think of each migration as being a new 'version' of the database. A schema starts off with nothing in it, and each migration modifies it to add or remove tables, columns, or entries. Active Record knows how to update your schema along this timeline, bringing it from whatever point it is in the history to the latest version. Active Record will also update your `db/schema`.rb file to match the up-to-date structure of your database.

You should have a file with timestamp in `app/db/migrate` folder and the content is something like this:

```ruby
class CreateUsers < ActiveRecord::Migration
  def change
    create_table :users do |t|
      t.string :name

      t.timestamps
    end
  end
end
```

The `t.timestamps` line will automagically add `created_at` and `updated_at` fields in the `users` table that will have their values set by Rails. To change our database we must *migrate* it to this new state where it will have the `users` table, using rake command:

```
rake db:migrate
```

By default Rails will use SQLite as the default database, because it doesn't need configuration and it's OK for simple development purposes. In real projects you won't stick with it for long, but for this tutorial we will be just fine with SQLite.

> Rake is a build language, similar in purpose to make and ant. Like make and ant it's a Domain Specific Language, unlike those two it's an internal DSL programmed in the Ruby language.

#### Know your routes

If you check your `app/config/routes.rb` you can see that the scaffold generator also creates a `resources :users` line. This is telling Rails that there's a `User` model with default CRUD actions.

You can always check your routes using the following command:

```
rake routes
```

And by now here's what the output should be:

```
   Prefix Verb   URI Pattern               Controller#Action
    users GET    /users(.:format)          users#index
          POST   /users(.:format)          users#create
 new_user GET    /users/new(.:format)      users#new
edit_user GET    /users/:id/edit(.:format) users#edit
     user GET    /users/:id(.:format)      users#show
          PATCH  /users/:id(.:format)      users#update
          PUT    /users/:id(.:format)      users#update
          DELETE /users/:id(.:format)      users#destroy
     root GET    /                         welcome#index
```

> The `:id` on the URI will be replaced with the numeric id for each user. Rails automatically creates the `id` column for every model.

Note that we have `users#new` and `users#edit` that aren't mentioned on the basic CRUD operations. That's because these two are only to present web forms to input data for the `create` and `update` operations.

If we fire up the WEBrick again we'll see a lot of things working right now. Go to `localhost:3000/users` and you can see that CRUD operations for users are working!

You probably noticed that every URL has a `(.:format)` in the end. This means that it accepts .html, .json, .xml or whatever format you define. By default you can ommit the format and Rails will use the one that it considers the default format (in this case HTML is the default format).

If you access `localhost:3000/users.json` you can see that your controllers already respond to JSON format but it isn't the default. To make it default change the `routes.rb` file to:

## Adding tasks

Let's add our second model, called `Task` to our application:

```
rails g scaffold task title completed:boolean user_id:integer
```

Our `tasks` table will have a title, a completed field (if it's complete or not) and an user id. Note that every field that is not `string` should explicitly tell what is the type of data.

Running that command we generate the same files we did when we scaffolded the `User` model. After this we must run the migration:

```
rake db:migrate
```

If you access `localhost:3000/tasks` you'll see that there is already the HTML views to interact with tasks like the ones for the `User`.

## The Rails console and Active Record ORM

Object-Relational Mapping, commonly referred to as its abbreviation ORM, is a technique that connects the rich objects of an application to tables in a relational database management system. Using ORM, the properties and relationships of the objects in an application can be easily stored and retrieved from a database without writing SQL statements directly and with less overall database access code.

Active Record is the Rails ORM, and is also loaded when you run the rails console. When you run the command `rails console` all your development environment is loaded, so you can also test any code that you've written.

Start by launching the rails console:

```
rails console
```

> **Note**: once again, you can just type `rails c`. You should see a prompt ending in something like `:001 >`.

We can now use Active Record to do the CRUD operations we've seen above without having to write SQL. Rails will output the performed query and then output the result. Lets try to find how many users are "registered" in your app:

```ruby
User.count
```

Now let's see who was our first and last "registered" user.

```ruby
User.last
User.first
```

If you look closely you will see that the records are already ordered by `id`.
You can also perform `WHERE`, `ORDER`, `JOINS` and almost anything you can think of doing with SQL with the help of some syntatic sugar given by Active Record. For example, you can search for users called John:

```ruby
User.find_by(name: 'John')
User.where(name: 'John').count # count them
```

> **Note**: as you can see, first we have used the `where` method, but to find a specific User we used `find`, more precisely `find_by`. This is because `where` will return an active record association, which is basically an array of users, even if only one is found, and `find` or `find_by` will return an instance of User, even if it finds more than one.

#### Model associations

Associations between models make common operations simpler and easier in your code. We already have User and Task models and we created an `user_id` field on tasks to identify the user that created that task. So if we want to get the tasks for the user 1 we must write:

```ruby
Task.where(user_id: 1)
```

Or if we wanted to delete user and all the tasks for that user:

```ruby
@user = User.find(1)
tasks = Task.where(user_id: @user.id)
tasks.each do |task|
  task.destroy
end
@user.destroy
```

With Active Record associations, we can streamline these — and other — operations by declaratively telling Rails that there is a connection between the two models. Here's the revised code for setting up users and tasks:

```ruby
class User < ActiveRecord::Base
  has_many :tasks, dependent: :destroy
end

class Task < ActiveRecord::Base
  belongs_to :user
end
```

Now if we want to get all tasks for a given user we can write:

```ruby
@user = User.find(1)
@user.tasks
```

To create a new task associated to that user:

```ruby
@user.tasks.create(title: 'New task by association!')
```

You can learn more about Active Record associations [here](http://guides.rubyonrails.org/association_basics.html).

## User authentication

User authentication is often a requisite on web applications. For a faster and easier authentication system we will use a gem called devise.

To install it we must add the gem dependency on our `Gemfile`. Just add this line:

```ruby
gem 'devise', '3.0.0' # don't install the most recent one
```

Then you must run the `bundle install` command inside the project directory. This command will check for new gem dependencies that you added on `Gemfile` and install them.

Once finished you can now use a devise generator (remember the generators are run with `rails generate`) to install the configuration files:

```
rails g devise:install
```

And then another one to generate some code on our user model:

```
rails g devise User
```

This last command will also create a new migration to add some missing fields to our user (`email`, `encrypted_password`, etc.). You should go ahead and change that migration because our authentication will be done with an authentication token rather than sessions, like in normal web applications.

Uncomment this lines:

```
# t.string :authentication_token
# add_index :users, :authentication_token, :unique => true
```

> **Note**: by now you should be aware that comments in ruby are done with the '#' character.

Run the devise user migration:

```
rake db:migrate
```

Edit `config/initializers/devise.rb` and also uncomment the line with the `config.token_authentication_key` configuration. It should be like this:

```
config.token_authentication_key = :authentication_token
```

On our User model add `:token_authenticatable` to the list of devise modules already listed and add a method to change the authentication_token every time the user is saved. Your user model should be something like this:

```ruby
class User < ActiveRecord::Base
  has_many :tasks, dependent: :destroy

  before_save :ensure_authentication_token

  # Include default devise modules. Others available are:
  # :token_authenticatable, :confirmable,
  # :lockable, :timeoutable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable, :token_authenticatable
end
```

#### Custom sessions and registrations

This step is not trivial, so just get the code below for your session and registration handling. The tutor will briefly describe what's going on on each file.

For sessions handling create `sessions_controller.rb` under `app/controllers/v1` with this content:

```ruby
class V1::SessionsController < ::Devise::SessionsController
  def create
    @user = User.where(user_params).first

    if @user.valid_password?(params[:user][:password])
      @user.authentication_token = ''
      @user.save
      render json: @user.as_json.merge(authentication_token: @user.authentication_token)
    else
      render json: 'Unauthorized', status: :unauthorized
    end
  end

  def destroy
    current_v1_user.authentication_token = nil
    super
  end

  protected

  def verified_request?
    request.content_type == 'application/json' || super
  end

  def user_params
    params.require(:user).permit(:name, :email)
  end
end
```

For registration handling create `registrations_controller.rb` under `app/controllers/v1` with this content:

```ruby
class V1::RegistrationsController < Devise::RegistrationsController
  prepend_before_filter :require_no_authentication, only: [:create]

  def create
    @user = User.new(user_params)

    if @user.save
      render json: @user.as_json.merge(authentication_token: @user.authentication_token), status: :created
    else
      warden.custom_failure!
      render json: @user.errors, status: :unprocessable_entity
    end
  end

  private

  def user_params
    params.require(:user).permit(:name, :email, :password)
  end
end
```

On your `routes.rb` file you can see that devise added a `devise_for :users` but we also need to insert it inside the namespace and give it our custom sessions controller:

```ruby
namespace 'v1', defaults: { format: 'json' } do
  devise_for :users, controllers: { sessions: 'v1/sessions' }
  resources :tasks, only: [:index, :create, :update, :destroy]
  resources :users, only: [:create]
end
```

Our tasks controller will be the one needing authentication. For that we just have to add this line:

```ruby
before_filter :authenticate_v1_user!
```

Last thing we want to do is change our `application_controller.rb` and change the `protect_from_forgery` line. Read the comment on that controller and you should know what to do:

```ruby
protect_from_forgery with: :null_session
```

## Final tweaks

We now have our application almost fully functional and ready to respond to our mobile app but you may have noticed that every user can get every task. We should only retrieve the tasks for the authenticated user.
For this, you need to change your TasksController in order to respond according to the user.

By having the `before_filter :authenticate_v1_user!` callback in the top of your controller, you now have access to the `current_v1_user` that is exactly what you need to this job.

So, change your `tasks#index` to:

```ruby
def index
  @tasks = current_v1_user.tasks
  render json: @tasks
end
```
You should also fix the `create` method so the current user gets associated to the class. This is done by changing your `tasks#create`:

```ruby
def create
  @task = current_v1_user.tasks.create(task_params)

  if @task
    render json: @task
  else
    render json: @task.errors, status: :unprocessable_entity
  end
end
```

And thats it, your API is ready to be used by your mobile app, that you'll be programming pretty soon :)

## Learn more

If you wish to learn more about Ruby on Rails and everything that we covered in this short tutorial we give you some useful links:

- [Rails Casts](http://railscasts.com/)
A nice place to watch quick, free and well explained tutorials. The paid tutorials are for more advanced techniques, but the for now, the freebies will do the job.

- [Rails for Zombies](http://railsforzombies.org/)
A nice introduction to ruby on rails lectured by Gregg Pollack, an expert rubyist.

- [Rubygems](http://rubygems.org/)
Where the hell are coming my gems from? Unless you explicitly say that you are using git or another host, this is where bundler and gem will look for the gems.

- [Ruby Toolbox](https://www.ruby-toolbox.com/)
Ruby gems is a repository with search, Ruby Toolbox helps you find your gems by category and much more.

- [Rails Guides](http://guides.rubyonrails.org/)
Everything you need to know about Ruby on Rails.

- [Bundler](http://bundler.io/)
A rails gem dependent best friend.

- [RVM](https://rvm.io/)
Control your ruby version in a dot. File.