require "reduce"

desc "Delete _site/"
task :delete do
  puts "\## Deleting _site/"
  status = system("rm -r _site")
  puts status ? "Success" : "Failed"
end

desc "Preview _site/"
task :preview do
  puts "\n## Opening _site/ in browser"
  status = system("open http://0.0.0.0:4000/")
  puts status ? "Success" : "Failed"
end

# Courtesy of https://github.com/pacbard/blog/blob/master/_rake/minify.rake
desc "Minify _site/"
task :minify do
  puts "\n## Compressing static assets"
  original = 0.0
  compressed = 0
  Dir.glob("_site/**/*.*") do |file|
    case File.extname(file)
      when ".css", ".gif", ".html", ".jpg", ".jpeg", ".js", ".png", ".xml"
        puts "Processing: #{file}"
        original += File.size(file).to_f
        min = Reduce.reduce(file)
        File.open(file, "w") do |f|
          f.write(min)
        end
        compressed += File.size(file)
      else
        puts "Skipping: #{file}"
      end
  end
  puts "Total compression %0.2f" % (((original-compressed)/original)*100)
end

namespace :build do
  desc "Build _site/ for development"
  task :dev do
    puts "\n## Starting local Jekyll server via Bundler"
    status = system("bundle exec jekyll serve --config _config.yml")
    puts status ? "Success" : "Failed"
  end

  desc "Build _site/ for production"
  task :pro do
    puts "\n## Building Jekyll to _site/"
    status = system("bundle exec jekyll build --config _config.yml")
    puts status ? "Success" : "Failed"
    Rake::Task["minify"].invoke
  end
end

desc "Build the production site locally"
task :deploy do
  Rake::Task["build:pro"].invoke
end

desc "Deprecated: deployment now happens via GitHub Actions on push"
task :commit_deploy do
  abort <<~MESSAGE

    rake commit_deploy is deprecated.

    Use this workflow instead:
      1. bundle exec jekyll serve
      2. git add -A
      3. git commit -m "Update site"
      4. git push origin <main-or-source>

    GitHub Actions will build and deploy the site automatically.

  MESSAGE
end
