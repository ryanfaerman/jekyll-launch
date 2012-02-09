require "rubygems"
require 'rake'
require 'yaml'

SOURCE = "."
CONFIG = {
  'version' => "0.2.0",
  'layouts' => File.join(SOURCE, "_layouts"),
  'posts' => File.join(SOURCE, "_posts"),
  'projects' => File.join(SOURCE, "_projects"),
  'post_ext' => "md",
  'theme_package_version' => "0.1.0"
}


namespace :jekyll do

  desc 'Delete generated _site files'
  task :clean do
    system "rm -fR _site"
  end
  
  desc 'Run the jekyll dev server'
  task :server do
    system "jekyll --server --auto"
  end
  
  desc 'Clean temporary files and run the server'
  task :compile => [:clean, 'less:compile'] do
    system "jekyll"
  end

  desc 'Clean, Compile, and Deploy to S3'
  task :deploy => [:compile] do
    system "jekyll-s3"
  end

end

namespace :less do
  desc 'Compile LESS'
  task :compile do
    puts "pretend everything compiled"
  end
end

namespace :generate do
  # Usage: rake page name="about.html"
  # You can also specify a sub-directory path.
  # If you don't specify a file extention we create an index.html at the path specified
  desc "Create a new page"
  task :page, [:name] do |t, args|
    name = args.name || "new-page.md"
    filename = File.join(SOURCE, "#{name}")
    filename = File.join(filename, "index.html") if File.extname(filename) == ""
    title = File.basename(filename, File.extname(filename)).gsub(/[\W\_]/, " ").gsub(/\b\w/){$&.upcase}
    if File.exist?(filename)
      abort("rake aborted!") if ask("#{filename} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
    end
    
    mkdir_p File.dirname(filename)
    puts "Creating new page: #{filename}"
    open(filename, 'w') do |post|
      post.puts "---"
      post.puts "layout: page"
      post.puts "title: \"#{title}\""
      post.puts "---"
    end
  end # task :page

  # Usage: rake post title="A Title"
  desc "Create a new post"
  task :post, [:title] do |t, args|
    abort("rake aborted: '#{CONFIG['posts']}' directory not found.") unless FileTest.directory?(CONFIG['posts'])
    title = args.title || "new-post"
    slug = title.downcase.strip.gsub(' ', '-').gsub(/[^\w-]/, '')
    filename = File.join(CONFIG['posts'], "#{Time.now.strftime('%Y-%m-%d')}-#{slug}.#{CONFIG['post_ext']}")
    if File.exist?(filename)
      abort("rake aborted!") if ask("#{filename} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
    end
    
    puts "Creating new post: #{filename}"
    open(filename, 'w') do |post|
      post.puts "---"
      post.puts "layout: post"
      post.puts "title: \"#{title.gsub(/-/,' ')}\""
      post.puts "category: "
      post.puts "tags: []"
      post.puts "---"
      post.puts "hello world"
    end
  end # task :post
end

def ask(message, valid_options)
  if valid_options
    answer = get_stdin("#{message} #{valid_options.to_s.gsub(/"/, '').gsub(/, /,'/')} ") while !valid_options.include?(answer)
  else
    answer = get_stdin(message)
  end
  answer
end

def get_stdin(message)
  print message
  STDIN.gets.chomp
end
