require 'tmpdir'

desc "Generate blog files"
task :generate do
  system "git checkout master"
  system "jekyll build"
end

desc "Generate and copy the blog to gh-pages branch"
task :deploy => [:generate] do
  Dir.mktmpdir do |tmp|
    system "mv _site/* #{tmp}"
    system "git checkout gh-pages"
    Dir.entries(Dir.pwd).each do |file|
      system "rm -rf #{file}" unless [".git", ".", ".."].include? File.basename(file)
    end
    system "mv #{tmp}/* ."
    system "git add -A"
    system 'git commit -m "Site deployed at #{Time.now.utc}"'
    system "git checkout master"
  end
end

desc "Generate and publish blog to gh-pages"
task :publish => [:deploy] do
  system "git checkout gh-pages"
  system "git push origin gh-pages --force"
  system "git checkout master"
end

task :default => :publish
