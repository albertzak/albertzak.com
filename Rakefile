require 'rubygems'
require 'yaml'
require 'jekyll'

desc 'Start a Server'
task :serve do
  system 'jekyll serve'
end

# Hack: Run twice for cache busting:
# - generate css and minify
# - bust cache with md5 of generated files
desc 'Build site'
task :build do
  2.times do
    config = YAML.load(ERB.new(File.read('_config.yml')).result)
    config['production'] = true
    Jekyll::Site.new(Jekyll.configuration(config)).process
  end
  puts "  \e[32mBuilt site\e[0m"
end

desc 'Build site and publish to S3'
task :publish => [:build] do
  puts 'Publishing site to S3...'
  `s3_website push`
  puts "  \e[32mPublished site\e[0m"
end

desc 'Create a new post'
task :new do
  title     = gets('Title: ')
  filename  = "_posts/#{date}-#{sluggize title}.md"

  if File.exist?(filename)
    puts "Can't create new post: \e[33m#{filename}\e[0m"
    puts "  \e[31m- Path already exists.\e[0m"
    exit 1
  end

  File.open(filename, "w") do |post|
    post.puts "---"
    post.puts "layout:    post"
    post.puts "title:     #{title}"
    post.puts "excerpt: >"
    post.puts "  Hi, I'm Albert Zak. I believe in"
    post.puts "  making useful things and living"
    post.puts "  an unconventional life."
    post.puts "permalink: #{sluggize title}"
    post.puts "date:      #{date}"
    post.puts "published: false"
    post.puts "---"
    post.puts ""
  end

  puts 'Created new post:'
  puts "  \e[32m#{filename}\e[0m"
end

def gets(message)
  print message
  STDIN.gets.chomp
end

def sluggize (str)
  str.downcase.gsub(/[^a-z0-9]+/, '-').gsub(/^-|-$/, '')
end

def date
  Time.now.strftime('%Y-%m-%d')
end
