
desc "Generate and view the site locally"
task :preview do
  require "launchy"

  # Yep, it's a hack! Wait a few seconds for the Jekyll site to generate and
  # then open it in a browser. Someday we can do better than this, I hope.
  Thread.new do
    sleep 4
    puts "Opening in browser..."
    Launchy.open("http://localhost:4000")
  end

  # Generate the site in server mode.
  puts "Running Jekyll..."
  sh "jekyll serve --watch"
end

desc "Commit the local site to the master branch and publish to GitHub Pages"
task :publish do
  puts "Checking the branch..."
  branch = `git branch`.match(/^\* ([a-z0-9]*)$/)[1]
  unless branch == "source"
    puts "You're not at the source branch, you cannot publish another branch to gh-pages."
    puts "Please, if you really want to publish THIS content, merge it on source branch and then call this task again."
    exit(1)
  end

  puts "Checking project status..."
  status = `git status`
  puts status
  unless status.match(/^nothing to commit, working directory clean$/)
    puts "The project have some uncommited files, please commit and push them."
    exit(1)
  end
  if status.match(/^# Your branch is ahead of/) ||
      status.match(/^# Your branch is behind of/) ||
      status.match(/^# Your branch and .* have diverged/)
    puts "The project is not sync with origin, please resolve this before publish."
    exit(1)
  end

  puts "Checking for master dir..."
  unless File.exist?("./master")
    puts "No master directory found. Run the following commands first:"
    puts "  $ git clone https://github.com/kelvinst/kelvinst.github.io master"
    puts "  $ cd master"
    puts "  $ git checkout master"
    exit(1)
  end

  # Ensure master branch is up to date.
  Dir.chdir('master') do
    sh "git pull origin master"
  end

  puts "Copying site to master branch..."
  Dir.glob("_site/*") do |path|
    sh "cp -R #{path} master/"
  end

  puts "Committing and pushing to GitHub Pages..."
  sha = `git log`.match(/[a-z0-9]{40}/)[0]
  Dir.chdir('master') do
    sh "git add ."
    sh "git commit -m \"Updating to #{sha}.\""
    sh "git push origin master"
  end
  puts 'Done.'
end
