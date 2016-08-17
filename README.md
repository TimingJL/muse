# Learning by Doing 

![Ubuntu version](https://img.shields.io/badge/Ubuntu-16.04%20LTS-orange.svg)
![Rails version](https://img.shields.io/badge/Rails-v5.0.0-blue.svg)
![Ruby version](https://img.shields.io/badge/Ruby-v2.3.1p112-red.svg)

Mackenzie Child's video really inspired me. So I decided to follow all of his rails video tutorial to learn how to build a web app. Through the video, I would try to build the web app by my self and record the courses step by step in text to facilitate the review.


# Project 12: How To Build A Dribbble Type App In Rails        
This time, we create a project called Muse. A user can sign-up and share what is inspiring them. They can add a post, and the post have a image, title, description, and the link to see more about where they found. And then people can like post or dislike as well as comment on each post.


### Highlights of this course
1. Posts
2. Image Uploading
3. Voting
4. Comments
5. Custom Styling


https://mackenziechild.me/12-in-12/12/            


# Create A Dribbble Type App
```console
$ rails new muse
```

Chage directory to the pin_board. Under `Gemfile`, add `gem 'therubyracer'`, save and run `bundle install`.      

Note: 
Because there is no Javascript interpreter for Rails on Ubuntu Operation System, we have to install `Node.js` or `therubyracer` to get the Javascript interpreter.

```console
$ bundle install
```

Then run the `rails server` and go to `http://localhost:3000` to make sure everything is correct.       

# Rubygems Install
Before we create the view file, let's install a few gems for our application.       
In `Gemfile`
```
gem 'devise'
gem 'simple_form', github: 'kesha-antonov/simple_form', branch: 'rails-5-0'
gem 'haml', '~> 4.0.5'
gem 'paperclip', '~> 4.2.0'
gem 'acts_as_votable', '~> 0.10.0'
```
Save that, run `bundle install` and restart our server.    

To install simple_form, we should run the generator:
```console
$ rails g simple_form:install
```

# Create a Post
Lets' start with the ability to create a post.         
```console
$ rails g model post title:string link:string description:text
$ rake db:migrate
```

Next, what we need to do is generate our controller.
```console
$ rails g controller Posts
```

In `config/routes.rb`
```ruby
Rails.application.routes.draw do
  resources :posts

  root 'posts#index'
end
```


In `app/controllers/posts_controller.rb`
```ruby
class PostsController < ApplicationController
	def index
	end
end
```

Then, let's create a view `index.html.haml` under `app/views/posts`
```haml
%h1 This is a placeholder :)
```

# CRUD
Next, let's add the ability to create, edit and destroy a post.         
In `app/controllers/posts_controller.rb`
```ruby
class PostsController < ApplicationController
	before_action :find_post, only: [:show, :edit, :update, :destroy, :upvote, :downvote]

	def index
		@posts = Post.all.order("created_at DESC")
	end

	def show
	end

	def new
		@post = Post.new
	end

	def create
		@post = Post.new(post_params)

		if @post.save
			redirect_to @post
		else
			render 'new'
		end
	end

	def edit
	end

	def update
	end

	def destroy
	end

	private

	def find_post
		@post = Post.find(params[:id])
	end

	def post_params
		params.require(:post).permit(:title, :link, :description)
	end
end
```



In `app/views/posts`, we create a new file and save it as `new.html.haml`
```haml
%h1 Add New Inspiration

= render 'form'
```

Next, let's create a partial `_form.html.haml` under `app/views/posts` to handle our form.
```haml
= simple_form_for @post do |f|
	= f.input :title
	= f.input :link
	= f.input :description
	= f.button :submit
```


Under `app/views/posts`, let's create a show page `show.html.haml`
```haml
%h1= @post.title
%p= @post.link
%p= @post.description

= link_to "Home", root_path
```


### Loop Through Our Post

Next, let's loop through our post on the index.      
So in `app/views/posts/index.html.haml`
```haml
- @posts.each do |post|
	%h2= link_to post.title, post

= link_to 'Add New Inspiration', new_post_path
```


### Edit & Destroy


In `app/controllers/posts_controller.rb`
```
def update
	if @post.update(post_params)
		redirect_to @post
	else
		render 'edit'
	end
end

def destroy
	@post.destroy
	redirect_to root_path
end
```

And in our show page `app/views/posts/show.html.haml`
```haml
%h1= @post.title
%p= @post.link
%p= @post.description


= link_to "Edit", edit_post_path(@post)
= link_to "Delete", post_path(@post), method: :delete, data: { confirm: "Are you sure?" }
= link_to "Home", root_path
```

And let's create a file called `edit.html.haml` under `app/views/posts`
```haml
%h1 Edit Inspiration

= render 'form'
```

# Add User to Our Application

After installing Devise and add it to our Gemfile, we need to run the generator
```console
$ rails generate devise:install


===============================================================================

Some setup you must do manually if you haven't yet:

  1. Ensure you have defined default url options in your environments files. Here
     is an example of default_url_options appropriate for a development environment
     in config/environments/development.rb:

       config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }

     In production, :host should be set to the actual host of your application.

  2. Ensure you have defined root_url to *something* in your config/routes.rb.
     For example:

       root to: "home#index"

  3. Ensure you have flash messages in app/views/layouts/application.html.erb.
     For example:

       <p class="notice"><%= notice %></p>
       <p class="alert"><%= alert %></p>

  4. You can copy Devise views (for customization) to your app by running:

       rails g devise:views

===============================================================================
```

Let's copy `config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }` in `config/environments/development.rb`.


And add flash messages in `app/views/layouts/application.html.erb` and convert html to haml         

In `app/views/layouts/application.html.haml`
```haml
!!!
%html
  %head
    %meta{:content => "text/html; charset=UTF-8", "http-equiv" => "Content-Type"}/
    %title Muse
    = csrf_meta_tags
    = stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload'
    = javascript_include_tag 'application', 'data-turbolinks-track': 'reload'
  %body
    %p.notice= notice
    %p.alert= alert
    = yield
```

Next thing we need to do is generate a Devise model.
```console
$ rails g devise User
$ rake db:migrate
```

Let's setup the user and make sure that the posts are built from the current user.    

So the first thing we need to do is run a migration.
```console
$ rails g migration add_user_id_to_post user_id:integer
$ rake db:migrate
```


Next thing we need to do is add an association between our user and our post.     

In `app/models/post.rb`
```ruby
class Post < ApplicationRecord
	belongs_to :user
end
```


In `app/models/user.rb`
```ruby
class User < ApplicationRecord
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable
  has_many :posts
end
```

And inside of our controller `app/controllers/posts_controller.rb`, let's tweak the way our post are created and add a before action for authentication.
```ruby
before_action :authenticate_user!, except: [:index, :show]

...
...

def new
	@post = current_user.posts.build
end

def create
	@post = current_user.posts.build(post_params)

	if @post.save
		redirect_to @post
	else
		render 'new'
	end
end
```

Now, if we pop into the rails console, we can see the `user_id` of the post we previously created is `nil`.
```console
$ rails console

> @post = Post.last

  Post Load (0.5ms)  SELECT  "posts".* FROM "posts" ORDER BY "posts"."id" DESC LIMIT ?  [["LIMIT", 1]]       
=> #<Post id: 1, title: "Craft beer keytar", link: "http://hipsum.co/?paras=4&type=hipster-centric",         
description: "Craft beer keytar 90's synth, sartorial plaid pour...", created_at: "2016-08-16 09:07:11", updated_at:        "2016-08-16 09:07:11", user_id: nil>
```

But if we create a new post now, we can see the `user_id` is assigned to current user.


# Add Field
Next, in sign-up page, we wanna also have a name field. First we need to run a migration.
```console
$ rails g migration add_name_to_user name:string
$ rake db:migrate
```

Then we need to copy over the devise views.
```console
$ rails g devise:views
```

So what we need to do is add a name field to our sign-up form.       
Let's go to `app/views/devise/registration/new.html.erb`, and add `<%= f.input :name, required: true, autofocus: true %>`.
```html

	<h2>Sign up</h2>

	<%= simple_form_for(resource, as: resource_name, url: registration_path(resource_name)) do |f| %>
	  <%= f.error_notification %>

	  <div class="form-inputs">
	  	<%= f.input :name, required: true, autofocus: true %>
	    <%= f.input :email, required: true %>
	    <%= f.input :password, required: true, hint: ("#{@minimum_password_length} characters minimum" if @minimum_password_length) %>
	    <%= f.input :password_confirmation, required: true %>
	  </div>

	  <div class="form-actions">
	    <%= f.button :submit, "Sign up" %>
	  </div>
	<% end %>

	<%= render "devise/shared/links" %>
```

Then in `app/views/devise/registration/edit.html.erb`, let's do the same thing.
```html

	<h2>Edit <%= resource_name.to_s.humanize %></h2>

	<%= simple_form_for(resource, as: resource_name, url: registration_path(resource_name), html: { method: :put }) do |f| %>
	  <%= f.error_notification %>

	  <div class="form-inputs">
	    <%= f.input :name, required: true, autofocus: true %>
	    <%= f.input :email, required: true %>

	    <% if devise_mapping.confirmable? && resource.pending_reconfirmation? %>
	      <p>Currently waiting confirmation for: <%= resource.unconfirmed_email %></p>
	    <% end %>

	    <%= f.input :password, autocomplete: "off", hint: "leave it blank if you don't want to change it", required: false %>
	    <%= f.input :password_confirmation, required: false %>
	    <%= f.input :current_password, hint: "we need your current password to confirm your changes", required: true %>
	  </div>

	  <div class="form-actions">
	    <%= f.button :submit, "Update" %>
	  </div>
	<% end %>

	<h3>Cancel my account</h3>

	<p>Unhappy? <%= link_to "Cancel my account", registration_path(resource_name), data: { confirm: "Are you sure?" }, method: :delete %></p>

	<%= link_to "Back", :back %>
```
![image](https://github.com/TimingJL/muse/blob/master/pic/name_field.jpeg)


A few other things for this to work is we need to add something to our application controller.        
In `app/controllers/application_controller.rb`
```ruby
# Mackenzie Child's code userd in Rails 4
class ApplicationController < ActionController::Base
	protect_from_forgery with: :exception

	before_filter :configure_permitted_parameters, if: :devise_controller?

	protected

	def configure_permitted_parameters
	  devise_parameter_sanitizer.for(:sign_up) << :name
	  devise_parameter_sanitizer.for(:account_update) << :name
	end
end
```

```ruby
# Mackenzie Child's code used in Devise 3.4.1
class ApplicationController < ActionController::Base
	protect_from_forgery with: :exception

	before_filter :configure_permitted_parameters, if: :devise_controller?

	protected

	def configure_permitted_parameters
	  devise_parameter_sanitizer.for(:sign_up) << :name
	  devise_parameter_sanitizer.for(:account_update) << :name
	end
end
```
But The Parameter Sanitaizer API has changed for Devise 4.             
(http://stackoverflow.com/questions/37341967/rails-5-undefined-method-for-for-devise-on-line-devise-parameter-sanitizer)          
So we tweak the code to 
```ruby
# My code used in Devise 4
class ApplicationController < ActionController::Base
	protect_from_forgery with: :exception

	before_filter :configure_permitted_parameters, if: :devise_controller?

	protected

	def configure_permitted_parameters
	  devise_parameter_sanitizer.permit(:sign_up, keys: [:name])
	  devise_parameter_sanitizer.permit(:account_update, keys: [:name])
	end
end
```         


Then we can go to `http://localhost:3000/users/edit` to add our user name.     

So in the show page `app/views/posts/show.html.haml`, we can add user name to it.
```haml
%h1= @post.title
%p= @post.link
%p= @post.description
%p= @post.user.name


= link_to "Edit", edit_post_path(@post)
= link_to "Delete", post_path(@post), method: :delete, data: { confirm: "Are you sure?" }
= link_to "Home", root_path
```

# Image Uploading
https://github.com/thoughtbot/paperclip         

After add `paperclip` to our Gemfile, the next thing we need to do is add `has_attached_file` and `validates_attachment_content_type` inside of our class of image uploading. So we paste it to        
`app/models/post.rb`
```ruby
class Post < ApplicationRecord
	belongs_to :user
	has_attached_file :image, styles: { medium: "700x500#", small: "350x250>" }
	validates_attachment_content_type :image, content_type: /\Aimage\/.*\Z/	
end
```

Next, we need to run the migration for the post model and the image upload.
```console
$ rails generate paperclip post image
$ rake db:migrate
```

Then we need to add file upload `= f.input :image` to our form.        
Under `app/views/posts/_form.html.haml`
```haml
= simple_form_for @post do |f|
	= f.input :image
	= f.input :title
	= f.input :link
	= f.input :description
	= f.button :submit
```

Next, we need to also permit this image attribute inside of our controller.       
In `app/controllers/posts_controller.rb`
```ruby
def post_params
	params.require(:post).permit(:title, :link, :description, :image)
end
```

So let's go to the `http://localhost:3000/posts/new`, the file uploading is showing up.
![image](https://github.com/TimingJL/muse/blob/master/pic/image_uploading.jpeg)



Next, we want the image to show up inside of our show page.       
So in `app/views/posts/show.html.haml`
```haml
= image_tag @post.image.url(:medium)
%h1= @post.title
%p= @post.link
%p= @post.description
%p= @post.user.name


= link_to "Edit", edit_post_path(@post)
= link_to "Delete", post_path(@post), method: :delete, data: { confirm: "Are you sure?" }
= link_to "Home", root_path
```
![image](https://github.com/TimingJL/muse/blob/master/pic/show_image.jpeg)



In `app/views/posts/index.html.haml`, we want to show up the image above the title..
```haml
- @posts.each do |post|
	= link_to (image_tag post.image.url(:small))
	%h2= link_to post.title, post

= link_to 'Add New Inspiration', new_post_path
```
![image](https://github.com/TimingJL/muse/blob/master/pic/show_small_image.jpeg)

# Comments

The next thing we should do is add comments to our post. To do that, let's generate a comment model.
```console
$ rails g model comment content:text post:references user:references
$ rake db:migrate
```

Then, we need to add association between our different model.     
In `app/models/post.rb`
```ruby
class Post < ApplicationRecord
	belongs_to :user
	has_many :comments
	has_attached_file :image, styles: { medium: "700x500#", small: "350x250>" }
	validates_attachment_content_type :image, content_type: /\Aimage\/.*\Z/	
end
```

In `app/models/user.rb`
```
class User < ApplicationRecord
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable
  has_many :posts
  has_many :comments
end
```

And the next thing we need to do is add some routes for comments.
```ruby
Rails.application.routes.draw do
  devise_for :users
  resources :posts do
  	resources :comments
  end

  root 'posts#index'
end
```


Now, let's create a controller for our comments.
```console
$ rails g controller comments
```


So in `app/controllers/comments_controller.rb`
```ruby
class CommentsController < ApplicationController
	before_action :authenticate_user!

	def create
		@post = Post.find(params[:post_id])
		@comment = Comment.create(params[:comment].permit(:content))
		@comment.user_id = current_user.id
		@comment.post_id = @post.id

		if @comment.save
			redirect_to post_path(@post)
		else
			render 'new'
		end
	end
end
```


Let's create a partial under `app/views/comments` and save it as `_form.html.haml`.
```haml
= simple_form_for([@post, @post.comments.build]) do |f|
	= f.input :content, label: "Reply to thread"
	= f.button :submit, class: "button"
```

Then, we need to show up out our comment inside of our show page.     
In `app/views/posts/show.html.haml`
```haml
= image_tag @post.image.url(:medium)
%h1= @post.title
%p= @post.link
%p= @post.description
%p= @post.user.name

#comment
	%h2.comment_count= pluralize(@post.comments.count, "Comment")
	- @comments.each do |comment|
		.comment
			%p.username= comment.user.name
			%p.content= comment.content

	= render 'comments/form'

= link_to "Edit", edit_post_path(@post)
= link_to "Delete", post_path(@post), method: :delete, data: { confirm: "Are you sure?" }
= link_to "Home", root_path
```


In `app/controllers/posts_controller.rb`, we need to define comments in our show action.
```ruby
def show
	@comments = Comment.where(post_id: @post)
end
```
![image](https://github.com/TimingJL/muse/blob/master/pic/add_comment.jpeg)

Let's add the comment count to the index.
In `app/views/posts/index.html.haml`
```haml
- @posts.each do |post|
	= link_to (image_tag post.image.url(:small))
	%h2= link_to post.title, post
	%p= pluralize(post.comments.count, "Comment")

= link_to 'Add New Inspiration', new_post_path
```









To be continued...