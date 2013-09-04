# README #

## Instration ##

Depends on

* ruby >= 1.9
  * webrick
  * erb
* rubygems
  * bundler
  * redcarpet

```
$ git clone https://github.com/kamito/markdown-server.git
$ cd markdown-server
$ bundle install
Fetching gem metadata from https://rubygems.org/..
Resolving dependencies...
Using redcarpet (3.0.0)
Using bundler (1.3.5)
Your bundle is complete!
Use `bundle show [gemname]` to see where a bundled gem is installed.
$
```

## Rake task ##

```
$ rake -T
rake new     # Create new markdown file
rake server  # Launch server
```

### rake new ###

Generata new markdown file.

```
$ rake new file=filename
```

It file format is "YYYYMMDDhhmmss-filename".

### rake server ###

Launch local server and use port is "8080".

```
$ rake server
[2013-09-04 17:51:20] INFO  WEBrick 1.3.1
[2013-09-04 17:51:20] INFO  ruby 2.0.0 (2013-06-27) [x86_64-linux]
[2013-09-04 17:51:20] INFO  WEBrick::HTTPServer#start: pid=18101 port=8080

```
