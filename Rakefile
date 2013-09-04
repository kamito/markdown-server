#!/usr/bin/env ruby
# -*- coding: utf-8 -*-

require 'rubygems'
require 'webrick'
require 'erb'
require 'redcarpet'


class MarkdownRenderer < ::Redcarpet::Render::HTML
end

class Task
  include Rake::DSL

  class << self
    def init
      instance = self.new
      instance.install
      instance
    end
  end

  def initialize
  end

  # Install rake tasks
  def install
    desc "Create new markdown file"
    task :new do
      generate_markdown
    end

    desc "Launch server"
    task :server do
      launch_server
    end
  end

  def generate_markdown
    now = Time.now
    filename = ENV['file']
    filename = "#{now.strftime('%Y%m%d%H%M%S')}-#{filename}.md"
    dirpath = File.expand_path(File.join(File.dirname(__FILE__)))
    filepath = File.join(dirpath, filename)
    File.open(filepath, 'w') do |io|
      io.write("#  #")
    end
    puts "Create new markdownfile : #{filename}"
  end

  def launch_server
    @document_root = Dir::pwd
    bind = ENV['bind'] || '0.0.0.0'
    port = ENV['port'] || 8080
    opt = {
      :DocumentRoot => @document_root,
      :BindAddress => bind,
      :Port => port,
    }
    server = WEBrick::HTTPServer.new(opt)
    server.mount_proc('/') do |request, response|
      handling_request(request, response)
    end
    trap("INT") { server.shutdown }
    server.start
  end

  # リクエストのハンドリング
  def handling_request(request, response)
    path = request.path
    case path
    when '/' then server_index(request, response)
    else server_file(request, response)
    end
  end

  def server_index(req, res)
    @files = FileList['*.md'].map do |file|
      file.gsub(/\.md/, '')
    end

    erb = ERB.new(load_template('server_index.html.erb'), nil, '-')
    src = erb.result(binding)
    res.body = src
  end

  def server_file(req, res)
    path = req.path
    filepath = File.expand_path(File.join(@document_root, path))
    if File.exists?(filepath) and not File.directory?(filepath)
      res.body = File.open(filepath, 'r') do |file|
        file.binmode # バイナリモードでのオープン
        file.read
      end
      content_types = {
        ".html" => "text/html", ".txt" => "text/plain",
        ".jpg" => "image/jpeg", ".jpeg" => "image/jpeg",
        ".gif" => "image/gif", ".png" => "image/png",
        ".mp3" => "audio/mpeg", ".mid"   => "audio/midi",
        ".css"   => "text/css", ".xhtml" => "application/xhtml+html",
        ".svg"   => "image/svg+xml"
      }
      # filenameの拡張子を見てContent-Typeを設定
      content_type = content_types[File.extname(filepath)]
      # Content-Typeが見つからなかったらtext/htmlを設定
      content_type = "text/html" if content_type.nil?
      res["Content-Type"] = content_type
    else
      filename = "#{path}.md"
      filepath = File.expand_path(File.join(Dir::pwd, filename))
      if File.exists?(filepath)
        @filename = filename
        src = File.read(filepath)
        markdown_options = {
          fenced_code_blocks: true,
          hard_wrap: true,
          tables: true,
          fenced_code_blocks: true,
          autolink: true,
        }
        renderer = ::MarkdownRenderer.new(markdown_options)
        markdown = Redcarpet::Markdown.new(renderer, markdown_options)
        @html = markdown.render(src)

        erb = ERB.new(load_template('server_file.html.erb'), nil, '-')
        html = erb.result(binding)
        content_type = "text/html"
        res["Content-Type"] = content_type
        res.body = html
      else
        res.set_redirect(::WEBrick::HTTPStatus::Redirect, '/')
      end
    end
  end

  # @return [String] Template source
  def load_template(filename)
    path = File.expand_path(File.join(File.dirname(__FILE__), 'lib', filename))
    return nil unless File.exists?(path)
    return File.read(path)
  end
end


Task.init

