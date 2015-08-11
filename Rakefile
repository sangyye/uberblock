require 'html/proofer'

task :default => [:test]

task :test => 'site:build' do 
  HTML::Proofer.new("./_site").run
end

#############################################################################
#
# Site tasks
#
#############################################################################

namespace :site do
  desc "Generate the site"
  task :build do
    sh "bundle exec jekyll build"
  end

  desc "Generate the site and serve locally"
  task :serve do
    sh "bundle exec jekyll serve"
  end

  desc "Generate the site, serve locally and watch for changes"
  task :watch do
    sh "bundle exec jekyll serve --watch"
  end

  desc "Generate the site, and push it to the remote server"
  task :deploy => 'build' do
    sh "rsync -avz --delete _site/ gauss:uberblock.de"
  end

  desc "Generate the site, test it and push changes to remote server"
  task :travis => 'test' do
    # Detect pull request
    if ENV['TRAVIS_PULL_REQUEST'].to_s.to_i > 0
      puts 'Pull request detected. Not proceeding with deploy.'
      exit
    end
    # Push to remote server via rsync
    sh "rsync -avz --delete _site/ gauss@peacock.uberspace.de:uberblock.de"
    # Commit and push to github
    puts "Pushed latest master to the server"
  end
end