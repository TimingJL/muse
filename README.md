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






 



To be continued...