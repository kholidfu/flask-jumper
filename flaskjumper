#!/bin/bash
#: Title       : flaskjumpstart
#: Date        : 2013-09-16
#: Author      : "Kholid Fuadi @sopier" <sopier@gmail.com>
#: Version     : 1.1.3
#: Description : Flask Boilerplate Project with Twitter Bootstrap 3
#: Options     : None
#: Usage       : $ flaskjumper projectname


project="$1"

# check if the project name exists
if [ ! -d $1 ]; then

  # create virtualenv $project    
  virtualenv $project

  cd $project

  # create app directory and cd to it
  mkdir app 
  cd app

  # create necessary directory and file[s]
  mkdir templates/
  mkdir static/
  mkdir static/css/
  mkdir static/js/

  # download twitter bootstrap v.3
  wget -c https://github.com/twbs/bootstrap/releases/download/v3.0.2/bootstrap-3.0.2-dist.zip

  # unzip it
  unzip bootstrap-3.0.2-dist.zip

  # rm it
  rm bootstrap-3.0.2-dist.zip

  # move it
  mv dist/* static/

  # rm empty bootstrap folder
  rm -r dist

  # snap the jquery
  touch static/css/style.css

  # download jquery and extract
  # wget -c http://code.jquery.com/jquery-1.9.1.js
  wget -c http://code.jquery.com/jquery-1.10.2.js
  mv jquery* static/js/

  # add robots.txt file
  touch static/robots.txt

  # posisi terakhir di app/

  # create app/__init__.py file
  cat > __init__.py <<EOF
from flask import Flask

app = Flask(__name__, 
        static_folder="static", # match with your static folder
        static_url_path="/static" # you can change this to anything other than static, its your URL
      )
from app import views

# logging tools 
# author: https://gist.github.com/mitsuhiko/5659670
# monitor uwsgi access / error :: output di nohup.out

import sys
from logging import Formatter, StreamHandler
handler = StreamHandler(sys.stderr)
handler.setFormatter(Formatter(
    '%(asctime)s %(levelname)s: %(message)s '
    '[in %(pathname)s:%(lineno)d]'
))
app.logger.addHandler(handler)
EOF

  # create app/views.py
  cat > views.py <<EOF
# author: @sopier

from flask import render_template, request, redirect, send_from_directory
from flask import make_response # untuk sitemap
from app import app
# untuk find_one based on data id => db.freewaredata.find_one({'_id': ObjectId(file_id)})
# atom feed
from werkzeug.contrib.atom import AtomFeed
from bson.objectid import ObjectId 
from filters import slugify, splitter, onlychars, get_first_part, get_last_part, formattime, cleanurl

import datetime

@app.template_filter()
def slug(s):
    """ 
    transform words into slug 
    usage: {{ string|slug }}
    """
    return slugify(s)

@app.template_filter()
def split(s):
    """ 
    split string s with delimiter '-' 
    return list object
    usage: {{ string|split }}
    """
    return splitter(s, '-')

@app.template_filter()
def getlast(text, delim=' '):
    """
    get last word from string with delimiter ' '
    usage: {{ string|getlast }}
    """
    return get_last_part(text, delim)

@app.template_filter()
def getfirst(text, delim=' '):
    """
    get first word from string with delimiter '-'
    usage: {{ string|getfirst }}
    """
    return get_first_part(text, delim)

@app.template_filter()
def getchars(text):
    """
    get characters and numbers only from string
    usage: {{ string|getchars }}
    """
    return onlychars(text)

@app.template_filter()
def sectomins(seconds):
    """
    convert seconds to hh:mm:ss
    usage: {{ seconds|sectomins }}
    """
    return formattime(seconds)

@app.template_filter()
def urlcleaner(text):
    """
    clean url from string
    """
    return cleanurl(text)

# handle robots.txt file
@app.route("/robots.txt")
def robots():
    # point to robots.txt files
    return send_from_directory(app.static_folder, request.path[1:])

@app.route("/")
def index():
    return render_template("index.html")

@app.route("/sitemap.xml")
def sitemap():
    # data = db.freewaredata.find()
    # sitemap_xml = render_template("sitemap.xml", data=data)
    # response = make_response(sitemap_xml)
    # response.headers['Content-Type'] = 'application/xml'

    # return response
    pass

@app.route('/recent.atom')
def recent_feed():
    # http://werkzeug.pocoo.org/docs/contrib/atom/ 
    # wajibun: id(link) dan updated
    # feed = AtomFeed('Recent Articles',
    #                feed_url = request.url, url=request.url_root)
    # data = datas
    # for d in data:
    #    feed.add(d['nama'], content_type='html', id=d['id'], updated=datetime.datetime.now())
    # return feed.get_response()
    pass
EOF

  # create filters.py
  cat > filters.py <<'EOF'
# author sopier

import re
from unidecode import unidecode

_punct_re = re.compile(r'[\t !"#$%&\'()*\-/<=>?@\[\\\]^_`{|},.]+')

def slugify(text, delim=u'-'):
    """Generates an ASCII-only slug."""
    result = []
    for word in _punct_re.split(text.lower()):
        result.extend(unidecode(word).split())
    return unicode(delim.join(result))

def splitter(text, delim=' '):
    """Split string into list, usage {{ string|split }}"""
    return text.split(delim)

def get_first_part(text, delim='-'):
    """Get first part from list of string with - delimiter"""
    return text.split(delim)[0]

def get_last_part(text, delim='-'):
    """Get last part from list of string with - delimiter"""
    return text.split(delim)[-1]

def onlychars(text):
    return " ".join(re.findall("[a-zA-Z0-9]+", text))

def formattime(seconds):
    """Convert seconds into minutes"""
    m, s = divmod(seconds, 60)
    h, m = divmod(m, 60)
    return '%02d:%02d:%02d' % (h, m ,s)

def cleanurl(text):
    """Remove url from text"""
    return re.sub(re.compile(r"http://.*? "), '', text)
EOF

  # go to the root directory
  cd ../

  cat > run.py <<EOF
#!/usr/bin/env python
from app import app

app.run(debug=True)
EOF

  chmod a+x run.py

  # activate the environment
  . bin/activate

  # go back to the app directory
  cd app/

  # we still in app directory

  # install module needed
  pip install flask # also installed => flask, jinja2 and wekzeug
  pip install pymongo
  pip install unidecode
  # uncomment the lines below if you think you need to
  # pip install ipython
  # pip install ipython-notebook
  # pip install tornado # needed by ipython notebook
  # pip install pyzmq # needed by ipython notebook
  # to install pyzmq, make sure you already have libzmq-dev and python2.7-dev
  # sudo apt-get install libzmq-dev
  # sudo apt-get install python2.7-dev

  # lets create base.html in templates folder

  cat > templates/base.html <<EOF
{% from "util.html" import link_tag, script_tag %}
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>{% block title %}{% endblock %} | example.com</title>
    {% block metadesc %}
    {% endblock %}
    {{ link_tag('bootstrap') }}
    {{ link_tag('style') }}
    {% block css %}
    {% endblock %}
    {{ script_tag('jquery-1.10.2') }}
    {{ script_tag('bootstrap') }}
    {{ script_tag('bootstrap.min') }}
    {% block js %}
    {% endblock %}
  </head>
  <body>
    <div class="container">
      <div class="container-narrow">
	<div class="masthead">
	  <ul class="nav nav-pills pull-right">
	    <li class="active">
	      <a href="/">Home</a>
	    </li>
	    <li>
	      <a href="#">About</a>
	    </li>
	    <li>
	      <a href="#">Contact</a>
	    </li>
	  </ul>
	  <h3 class="muted">
	    Project name
	  </h3>
	</div>
      </div>
      <hr>
      <div class="container-narrow">
	{% block content %}{% endblock %}
      </div>
      <div class="footer container-narrow">
	<p>
	  © Company 2013
	</p>
      </div>
    </div>
  </body>
</html>
EOF

  # create util.html as our macro container
  # macro will makes your code easier to read / understand and more flexible
  cat > templates/util.html <<EOF
{%- macro link_tag(location) -%}
  <link rel="stylesheet" href="/static/css/{{location}}.css">
{%- endmacro -%}

{%- macro script_tag(location) -%}
  <script src="/static/js/{{ location }}.js"></script>
{%- endmacro -%}
EOF

  # create index.html
  cat > templates/index.html <<EOF
{% extends "base.html" %}
{% block title %}Index {% endblock %}
{% block metadesc %}
<meta name="description" content="index">
{% endblock %}
{% block content %}
  <div class="jumbotron">
    <h1>Flask Jump Start</h1>
    <p class="lead">
      The quick and right way to develop web apps in seconds!
    </p>
    <a class="btn btn-large btn-success" href="#">Sign up today</a>
  </div>
  <hr>
  <div class="marketing row-fluid">
    <div class="span12">
      <h1>Hot Topics</h1>
      <p>Hot Contents</p>
    </div>
  </div>
  <hr>
<!-- /container -->
{% endblock %}
EOF

  # create index.html
  cat > templates/sitemap.xml <<EOF
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
{% for d in data %}
<url>
    <loc>http://www.domains.com/{{ d.title|getchars|slug }}/{{ d._id }}</loc>
</url>
{% endfor %}                                                                
</urlset>
EOF


  # deactivate the virtualenv
  deactivate
else 
  { echo >&2 "Project name exists. Aborting..."; exit 1; }
fi
