---
layout: post
title: "Making a Memorable Dropbox Public File Redirect with Flask"
---

Files stored in your [Dropbox](https://db.tt/b5XaQPa) Public folder are viewable by anyone with access to
the URL, which is long and hard to remember but unique. [Flask](http://flask.pocoo.org/) is a
microframework for making lightweight dynamic websites in Python. In this post, I'll be demonstrating how
to make a simple webapp with Flask that redirects requests to your Dropbox public folder, and hosting it.

## Finding your user constant

Every file in your Public folder is available at the URL
`https://dl.dropbox.usercontent.com/u/[user constant]/[filename]`. To find your user constant, open
your Dropbox Public folder, right click on a file, select `Dropbox > Copy Public Link`, and paste into a
text editor.

## Writing the app

{% gist 7462815 app.py %}

Uncomment line 2, and define `USER_CONSTANT` to be the constant number you found previously.

Lines 6-8 return usage message if the app is accessed without a filename, lines 10-12 redirect to the
file with the specified name in your Public folder if `/[filename]` is accessed, and the other lines
import and run Flask. That's it!

## Hosting the app

While you could run this app locally, it's not exceptionally useful limited to your computer. To host
it for free, we'll use [Heroku](https://www.heroku.com/). Heroku uses [git](http://git-scm.com/) to
deploy your code. Make an account and create a new app, with a short, memorable name. It'll generate
a customized git clone command. Run it in your terminal:

{% highlight bash %}
git clone git@heroku.com:[your app name].git -o heroku

cd [your app name]
{% endhighlight %}

Copy `app.py` to this directory, then we have to make two more files for Heroku to know what to do with
our app.

{% gist 7462815 Procfile %}

The Procfile tells Heroku how to run our app. In this case, by running `app.py`

{% gist 7462815 requirements.txt %}

requirements.txt lists all the packages our app depends on, which Heroku will install at compiletime.

Now, add these three files to the repository, commit, and push:

{%highlight bash %}
git add app.py Procfile requirements.txt

git commit -m "Initial Commit"

git push heroku master
{% endhighlight %}

Wait for compilation to finish, then check on your app at `http://[your app name].herokuapp.com/`. If you see
the usage message, it works! Now you can use the simpler `http://[your app name].herokuapp.com/[filename]`
to access your Public files.

