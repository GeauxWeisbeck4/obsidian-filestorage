---
tags:
  - servers
  - wsgi
  - programming
  - web-development
  - python
---
# WSGI Basics
- https://wsgi.readthedocs.io/en/latest/

WSGI is the Web Server Gateway Interface. It is a specification that describes how a web server communicates with web applications, and how web applications can be chained together to process one request.

WSGI is a Python standard described in detail in [**PEP 3333**](https://peps.python.org/pep-3333/).

For more, see [Learn about WSGI](https://wsgi.readthedocs.io/en/latest/learn.html).

# Learn About WSGI
- [WSGI Tutorial](http://wsgi.tutorial.codepoint.net/intro) by Clodoaldo Neto
    
- [WSGI Explorations in Python](http://linuxgazette.net/115/orr.html) by Mike Orr
    
- [An Introduction to the Python Web Server Gateway Interface (WSGI)](http://ivory.idyll.org/articles/wsgi-intro/what-is-wsgi.html) by Titus Brown
    
- [A Do-It-Yourself Framework](https://paste.readthedocs.io/en/latest/do-it-yourself-framework.html) by Ian Bicking
    
- [URL Parsing with WSGI](https://paste.readthedocs.io/en/latest/url-parsing-with-wsgi.html) by Ian Bicking
    
- [WSGI and WSGI Middleware is Easy](https://be.groovie.org/post/2005/10/07/wsgi_and_wsgi_middleware_is_easy/) by Ben Bangert
    
- [WSGI - Gateway or Glue](https://web.archive.org/web/20170928103920/http://osdcpapers.cgpublisher.com/product/pub.84/prod.21) by Mark Rees (**particularly good as a starting point**)
    
- [Mix and match Web components with Python WSGI](https://copia.posthaven.com/mix-and-match-web-components-with-python-wsgi) by Uche Ogbuji
    
- [‘Hello World with WSGI’](http://rufuspollock.org/2006/08/31/a-very-simple-introduction-to-wsgi/) and [WSGI Middleware](http://rufuspollock.org/2006/09/28/wsgi-middleware/) by Rufus Pollock
    
- [Getting started with WSGI](http://lucumr.pocoo.org/2007/5/21/getting-started-with-wsgi) by Armin Ronacher
    
- [Why so many Python web frameworks?](https://bitworking.org/news/2006/09/why_so_many_python_web_frameworks/) by Joe Gregorio (_outlines the creation of a web framework using several WSGI-based tools_)
    
- [Introducing WSGI: Python’s Secret Web Weapon](http://www.xml.com/pub/a/2006/09/27/introducing-wsgi-pythons-secret-web-weapon.html) by James Gardner [[xml2006-09]](https://wsgi.readthedocs.io/en/latest/learn.html#xml2006-09)
    
- [Introducing WSGI: Python’s Secret Web Weapon, Part Two](http://www.xml.com/pub/a/2006/10/04/introducing-wsgi-pythons-secret-web-weapon-part-two.html) by James Gardner [[xml2006-10]](https://wsgi.readthedocs.io/en/latest/learn.html#xml2006-10)
    
- [test.wsgi](http://hg.moinmo.in/moin/1.8/raw-file/tip/wiki/server/test.wsgi) a WSGI test app showing whether your WSGI environment is working (and also outputs some interesting information like Python version, sys.path, WSGI environment, etc.). It can be directly used for mod_wsgi and easily for all other WSGI servers. When started directly from command line, it tries to use wsgiref’s simple server to serve the application.
    

[[xml2006-09](https://wsgi.readthedocs.io/en/latest/learn.html#id1)]

xml.com, Sept 2006. Part 1: getting started

[[xml2006-10](https://wsgi.readthedocs.io/en/latest/learn.html#id2)]

xml.com, Oct 2006. Part 2: Making Use of a Middleware