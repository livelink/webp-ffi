require 'rubygems'
require 'bundler/setup'
require 'rake'
require 'rake/clean'
require 'bundler/gem_tasks'
require 'rspec/core/rake_task'
require 'ffi-compiler/compile_task'
require 'mini_portile2'

WEBP = MiniPortile.new('libwebp', '1.2.4')
WEBP.files = ['https://storage.googleapis.com/downloads.webmproject.org/releases/webp/libwebp-1.2.4.tar.gz']
WEBP.configure_options << '--disable-gl' << '--disable-sdl'

desc "compiler tasks"
namespace "ffi-compiler" do
  task :libs do
    old_cflags = ENV['CFLAGS']
    ENV['CFLAGS'] = (ENV['CFLAGS'].to_s + ' -fPIC').strip
    WEBP.cook
    ENV['CFLAGS'] = old_cflags
    WEBP.activate
  end
  FFI::Compiler::CompileTask.new('ext/webp_ffi/webp_ffi') do |c|
    c.have_header?('stdio.h', '/usr/local/include')
    c.have_func?('puts')
    c.have_library?('z')
    c.ldflags << '-L' + File.join(WEBP.path, 'lib')
    c.cflags << '-I' + File.join(WEBP.path, 'include')
    c.have_header?('decode.h', '/usr/local/include')
    c.have_header?('encode.h', '/usr/local/include')
    c.have_func?('WebPDecoderConfig')
    c.have_func?('WebPGetInfo')
    c.have_library?('webp')
    c.have_library?('png')
    c.have_library?('jpeg')
    c.have_library?('tiff')
    c.ldflags << ENV['LD_FLAGS'] if ENV['LD_FLAGS']
    c.cflags << ENV['C_FLAGS'] if ENV['C_FLAGS']
  end
end
task :compile => ["ffi-compiler:libs", "ffi-compiler:default"]

desc "run specs"
task :spec do
  RSpec::Core::RakeTask.new
end

task :default => [:clean, :compile, :spec]

CLEAN.include('ext/**/*{.o,.log,.so,.bundle}')
CLEAN.include('lib/**/*{.o,.log,.so,.bundle}')
CLEAN.include('ext/**/Makefile')
