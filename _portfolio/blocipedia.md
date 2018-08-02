---

layout: post
title: Blocipedia
thumbnail-path: "img/blocipedia.png"
short-description: Blocipedia is a web application that allows users to create public and private Markdown-based wikis.

---

{:.center}
![]({{ site.baseurl }}/img/blocipedia.png)

## Explanation

Blocipedia is a web application for creating and editing public and private wikis.  The application will allow users to select from a free plan and a premium plan.


## Problem

User story 1:  Users should be able to sign up for an account by creating a username and providing their email and a password.

Using the Devise gem, one can easily create a way for users to create accounts for a web application.  Devise also helps with authentication.  

{% highlight ruby %}  

<% if user_signed_in? %>
   Signed in as: <strong><%= current_user.email %></strong> |
   <%= link_to 'Edit profile', edit_user_registration_path, :class => 'navbar-link' %> - <%= link_to "Sign Out", destroy_user_session_path, method: :delete, :class => 'navbar-link'  %>
 <% else %>
 # #2
   <%= link_to "Sign Up", new_user_registration_path, :class => 'navbar-link'  %> -
   <%= link_to "Sign In", new_user_session_path, :class => 'navbar-link'  %>
 <% end %>
 
{% endhighlight %}   

## Solution


## Results


## Conclusion