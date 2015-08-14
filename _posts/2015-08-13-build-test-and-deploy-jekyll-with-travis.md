---
layout: post
title: Build, Test and Deploy Jekyll with Travis
date: 2015-08-13 16:30:00 +0200
published: true
categories: []
tags: [jekyll, howto, travis]
---

So I have this [jekyll](http://jekyllrb.com/) Blog here that is already on [travis](https://travis-ci.org/sangyye/uberblock). It was just sitting there, waiting for me to commit, than build and test it. But there have to be more, like deploy it. So the Plan is to just commit it, [travis](https://travis-ci.org/) noticed it, build it, use [html-proofer](https://github.com/gjtorikian/html-proofer) to test it and than deploy it to my [uberspace.de](https://uberspace.de) webspace. That would let me update my blog directly from the Github web interface if I want. What would be totally awesome.

## Get Tested

So the first step is to get your jekyll on travis to build and test it. Your jekyll repo needs to be an public Repo on Github. Than you login on travis with your Github account. There you can activate your repo for testing with travis. After that your next commit will trigger a build on travis. But without a prober config travis doesn't know what todo, so we add a `.travis.yml` to the repo:

{% highlight yaml %}
language: ruby
sudo: false
cache: bundler
rvm:
- 2.2.2
install: bundle install
script: bundle exec jekyll build && bundle exec htmlproof ./_site --disable-external
env:
  global:
  - NOKOGIRI_USE_SYSTEM_LIBRARIES=true
{% endhighlight %}

This will tell travis that it has to build an ruby project and have to use bundler to install all dependencies. We use html-proofer to test the site we build for wrong HTML syntax or broken links. This means we also need a Gemfile to define the Dependencies:

{% highlight ruby %}
source "https://rubygems.org"
gem "rake"
gem "jekyll"
gem "html-proofer"
{% endhighlight %}

Now you have to add both files and make a commit. If you now push it to your Github repo travis should detected the commit and start your build. If the Build is a success your setup and jekyll files are correct and you can start your automatic deploy.

## Get Deployed

Now is the time to get travis deploy your jekyll build. To make things easier in the future we will now add a simple Rakefile to the repo.

{% highlight ruby %}
require 'rake'
require 'html/proofer'

namespace :site do
  desc 'build and test the site'
  task :test => 'site:build' do 
	HTML::Proofer.new("./_site", {:disable_external => true, :check_html => true}).run
  end

  desc "Generate the site"
  task :build do
	sh "bundle exec jekyll build"
  end

  desc "Generate the site, test it and push changes to remote server"
  task :travis => 'site:test' do
	# Detect pull request
	if ENV['TRAVIS_PULL_REQUEST'].to_s.to_i > 0
	  puts 'Pull request detected. Not proceeding with deploy.'
	  exit
	end
	# Push to remote server via rsync
	sh "rsync -avz --delete _site/ user@server.example.com:/path/to/jekyllsite"
	# Commit and push to github
	puts "Pushed latest master to the server"
  end
end
{% endhighlight %}

You can now call a `rake site:test` to run your test locally and see if it will work. I also add an line that will not make any deployments on a push request. So if you work in a team, every request will be build and tested but not deployed. If you need more branches than I do you have to ignore them in the deployment. A SSH Key will be needed if you want to deploy via rsync, it's more secure that way and also travis support it. Run `ssh-keygen -t rsa -f deploy_key` to generate a file deploy_key and deploy_key.pub in the repo root. Generate it without password and add them to the `.gitignore`. Don't ever add the private key to your repo and also exclude it in your `_config.yml` just to make sure it will not end on your server. You need the travis commando suit to encrypt the the private ssh key for travis. Run `gem install travis` to get it. With `travis login` you login into your Github account to get the needed secrets for you repo, than with `travis encrypt-file deploy_key` you generate the deploy_key.enc that you have to add to the repo. Also you get an line that you have to add to the `.travis.yml`. It will look like this:

{% highlight yaml %}
language: ruby
sudo: false
cache: bundler
rvm:
- 2.2.2
install: bundle install
script: bundle exec rake site:travis
env:
  global:
  - NOKOGIRI_USE_SYSTEM_LIBRARIES=true
before_install:
- echo -e "Host server.example.com\n\tStrictHostKeyChecking no\n" >> ~/.ssh/config
- openssl aes-256-cbc -K $encrypted_7sduawwfcc_key -iv $encrypted_sdjsw881ws_iv -in deploy_key.enc -out deploy_key -d
- chmod 600 deploy_key
- mv deploy_key ~/.ssh/id_rsa
{% endhighlight %}

You have to change the server name to that server that you are planing to use, otherwise the deployment will fail. You have to change the openssl line that it looks like the line the travis command gave to you. Also you can now use the Rakefile in the script option to make the deployment. Now add the pub key to your `~/.ssh/authorized_keys` of your server, so travis can make the deployment. Now commit everything, the Rakefile, the .travis.yml and the new deploy_key.enc. If everything works correct, travis will build now your jekyll repo, test it and finally deploy it on your server.

You can also look at my [Rakefile](https://github.com/sangyye/uberblock/blob/master/Rakefile) and [travis.yml](https://github.com/sangyye/uberblock/blob/master/.travis.yml) to see a working example.

Congratulations your jekyll is now build and deployed with travis. Have fun :)