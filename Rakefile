require 'rake'
require 'fileutils'

SOURCE_BRANCH = "main"
DEPLOY_BRANCH = "gh-pages"
BUILD_DIR = "_site"
REMOTE = "origin"

desc "Build the site with Jekyll"
task :build do
  sh "bundle exec jekyll build"
end

desc "Commit source code to main"
task :commit_source do
  sh "git add ."
  sh %(git commit -m "Update source site content" || echo 'Nothing to commit on main')
  sh "git push #{REMOTE} #{SOURCE_BRANCH}"
end

desc "Deploy _site to gh-pages branch"
task :deploy do
  # Save current branch
  current_branch = `git branch --show-current`.strip

  # Build the site
  Rake::Task[:build].invoke

  # Checkout deploy branch
  sh "git checkout #{DEPLOY_BRANCH} 2>/dev/null || git checkout --orphan #{DEPLOY_BRANCH}"
  sh "git reset --hard"

  # Copy _site content
  FileUtils.rm_rf(Dir.glob("*"))
  FileUtils.cp_r("#{BUILD_DIR}/.", ".")

  # Commit built site
  sh "git add ."
  sh %(git commit -m "Deploy site to GitHub Pages" || echo 'Nothing to commit on gh-pages')
  sh "git push #{REMOTE} #{DEPLOY_BRANCH}"

  # Return to previous branch
  sh "git checkout #{current_branch}"
end

desc "Full deploy: commit source and publish site"
task :full_deploy => [:commit_source, :deploy]
