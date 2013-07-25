# # # # # # # # # # # # # # # # # # # # # # # # # # # # #
# Utility class that calls forward to WebBlocks rake

require 'pathname'
require 'fileutils'

class WebBlocks
  attr_accessor :path
  attr_accessor :local
  def initialize(path)
    @path = path
    @local = Local.new "src", "build"
  end
  def rake(command = '')
    Dir.chdir @path do
        rootdir = File.dirname(Pathname.new(__FILE__).realpath)
        sh "bundle exec rake #{command} -- --config=#{rootdir}/Rakefile-config.rb"
    end
  end
  class Local
    def initialize src_dir, build_dir
      rootdir = File.dirname(Pathname.new(__FILE__).realpath)
      @src_dir = "#{rootdir}/#{src_dir}"
      @build_dir = "#{rootdir}/#{build_dir}"
    end
    def log_task name
      log "Executing task: #{name}"
    end
    def log message
      puts "[Local] #{message}"
    end
    def move from, to
      FileUtils.mkdir_p File.dirname "#{@build_dir}/#{to}"
      FileUtils.copy "#{@src_dir}/#{from}", "#{@build_dir}/#{to}"
    end
    def move_sub from, to
      Dir.foreach("#{@src_dir}/#{from}") do |item|
        next if item == '.' or item == '..'
        FileUtils.mkdir_p "#{@build_dir}/#{to}"
        FileUtils.copy "#{@src_dir}/#{from}/#{item}", "#{@build_dir}/#{to}"
      end
    end
    def remove path
      FileUtils.rm_rf "#{@build_dir}/#{path}"
    end
    def build_html
      log "Executing task: build_html"
      move_sub "html", ""
    end
    def build_fonts
      log "Executing task: build_fonts"
      move_sub "font", "font"
    end
    def build_all
      build_html
      build_fonts
    end
    def clean
      clean_html
      clean_fonts
    end
    def clean_html
      log "Executing task: clean_html"
      Dir["#{@build_dir}/*.html"].each do |file|
        FileUtils.rm_f file
      end
    end
    def clean_fonts
      log "Executing task: clean_fonts"
      remove "font"
    end
  end
end

# # # # # # # # # # # # # # # # # # # # # # # # # # # # #
# Set path to WebBlocks

blocks = WebBlocks.new('vendor/ucla/WebBlocks')

# # # # # # # # # # # # # # # # # # # # # # # # # # # # #
# Define the valid Rake tasks that will be call-forwarded

include Rake::DSL
task :default => [:build] do
end
task :init => [:_init] do
  blocks.rake 'init'
end
task :build => [:_init] do
  blocks.rake 'build'
  blocks.local.build_html
  blocks.local.build_fonts
end
task :build_all => [:_init, :clean_fonts, :clean_html] do
  blocks.rake 'build_all'
  blocks.local.build_all
end
task :build_css => [:_init] do
  blocks.rake 'build_css'
end
task :build_img => [:_init] do
  blocks.rake 'build_img'
end
task :build_js => [:_init] do
  blocks.rake 'build_js'
end
task :build_html do
  blocks.local.build_html
end
task :build_fonts do
  blocks.local.build_fonts
end
task :clean => [:_init] do
  blocks.rake 'clean'
  blocks.local.clean
end
task :clean_html do
  blocks.local.clean_html
end
task :clean_fonts do
  blocks.local.clean_fonts
end
task :clean_packages => [:_init] do
  blocks.rake 'clean_packages'
end
task :clean_all => [:_init] do
  blocks.rake 'clean_all'
end
task :reset_packages => [:_init] do
  blocks.rake 'reset_packages'
end
task :reset => [:_init] do
  blocks.rake 'reset'
end

# # # # # # # # # # # # # # # # # # # # # # # # # # # # #
# Specialized task that pre-initializes

task :_init do
  IO.popen("git rev-parse --show-toplevel") do |io|
    Dir.chdir(io.gets.strip) do
      sh "git submodule init"
      sh "git submodule update"
    end
  end
  Dir.chdir('vendor/ucla/WebBlocks') do
    sh "bundle"
    sh "npm install"
  end
end