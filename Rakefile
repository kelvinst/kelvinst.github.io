
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

desc "Update normalize.css library to the latest version and minify"
task :update_normalize_css do
  Dir.chdir("site/css") do
    sh 'curl "http://necolas.github.io/normalize.css/latest/normalize.css" -o "normalize.scss"'
    sh 'sass "normalize.scss":"normalize.css" --style compressed'
    sh 'rm "normalize.scss"'
  end
end

desc "Commit the local site to the gh-pages branch and publish to GitHub Pages"
task :publish do
  puts "Checking for gh-pages dir..."
  unless File.exist?("./gh-pages")
    puts "No gh-pages directory found. Run the following commands first:"
    puts "  $ git clone https://github.com/kelvinst/kelvinst.github.io gh-pages"
    puts "  $ cd gh-pages"
    puts "  $ git checkout gh-pages"
    exit(1)
  end

  puts "Building jekyll site..."
  sh "jekyll build"

  # Ensure gh-pages branch is up to date.
  Dir.chdir('gh-pages') do
    sh "git pull origin gh-pages"
  end

  puts "Copying site to gh-pages branch..."
  Dir.glob("_site/*") do |path|
    sh "cp -R #{path} gh-pages/"
  end

  puts "Committing and pushing to GitHub Pages..."
  sha = `git log`.match(/[a-z0-9]{40}/)[0]
  Dir.chdir('gh-pages') do
    sh "git add ."
    sh "git commit -m \"Updating to #{sha}.\""
    sh "git push origin gh-pages"
  end
  puts 'Done.'
end
