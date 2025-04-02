require 'rake'
require 'fileutils'
require "tmpdir"

SOURCE_BRANCH = "main"
DEPLOY_BRANCH = "gh-pages"
BUILD_DIR = "_site"

desc "Build the site with Jekyll"
task :build do
  sh "bundle exec jekyll build"
end

desc "Commit source code to main"
task :commit_source do
  sh "git add ."
  sh %(git commit -m "Update source site content" || echo 'Nothing to commit on main')
  sh "git push origin #{SOURCE_BRANCH}"
end

desc "Deploy _site to gh-pages branch"
task :deploy do
  # Save current branch
  current_branch = `git branch --show-current`.strip
  fail "current_branch is empty" if current_branch.empty?
  origin = `git config --get remote.origin.url`
  fail "origin is empty" if origin.empty?
  # Build the site
  Rake::Task[:build].invoke

  Dir.mktmpdir do |tmp|
    # Copy accross our compiled _site directory.
    cp_r "_site/.", tmp

    # Switch in to the tmp dir.
    Dir.chdir tmp

    # Prepare all the content in the repo for deployment.
    sh "git init" # Init the repo.
    sh "git checkout #{DEPLOY_BRANCH} 2>/dev/null || git checkout --orphan #{DEPLOY_BRANCH}"
    sh "git add . && git commit -m 'Site updated at #{Time.now.utc}'" # Add and commit all the files.

    # Add the origin remote for the parent repo to the tmp folder.
    sh "git remote add origin #{origin}"

    puts "Pushing to #{origin}"
    sh "git push --force origin #{DEPLOY_BRANCH}"
  end

  sh "git push #{REMOTE} #{DEPLOY_BRANCH}"

  # Return to previous branch
  sh "git checkout #{current_branch}"
end

desc "Full deploy: commit source and publish site"
task :default => [:commit_source, :deploy]
