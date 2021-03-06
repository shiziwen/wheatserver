Wheatserver(Alpha)
===========

Full stack sync/asyc(wait) IO Application Framework, help you build your own
application server.

You can use Wheatserver to develop web server, application proxy server and
db proxy server etc. You will enjoy using sync programming model to have
great performance under async model.

Now supporting WSGI, Static file distribute and Redis distribute.

[中文文档](http://www.wzxue.com/wheatserver-2/)

Feature
===========

* Fast: Full C implemented, and discards any module unnecessary and try to
avoid to copy data.

* Low memory: Keep low memory and take actions to let multi worker processes
share read-only memory.

* Pluggable: Worker type, protocol implement and application server all can be
remove and add. You can construct your own worker, application server and
other.

* Statistic: You can use 'kill -s sigusr1 PID' command or tool to get statistic 
information from wheatserver.

* Reload: If you change config file and apply it without restart wheatserver.
Only you need to do is 'kill -s sighup PID' let wheatserver reload file and
reset workers.

* Manage: Administrator can get information of running wheatserver by command
  interface which can be accessed via client/client.py. You can get more info by
  enter 'help' with running it.

* Module extend: Module can define own statistic information, command(accessed
  by Wheatserver client) and configuration in simple way. All module defines can
  utilize Wheatserver's admin tools.

* Simple: Try to hide complexity logical processing and provide simple
interfaces to developer.

* Implemented:

> Workers: Sync Worker and Async Worker

> Protocol: Http 1.0 and Http 1.1, Redis protocol

> Application Server: WSGI support and static file support both under Http,
> Redis-cluster app support under Redis protocol

TODO Feature
===========

* Web Dashboard aimed to replace with CLI interactive
* Add SystemTap Toolkit support to monitor the status of application and for
  ease of Ops
* Refactor ugly code such as configuration definition and replace with more clear
  definition.
* Provide Static Module with `fd`(file description) cache like Nginx
* Provide with multi IP address binding
* Add corotine worker support

If anyone is interested in Wheatserver and join us, feel free to mail me!

Build
===========

Requestments: python, python-dev 

Support Platform: Linux,  Macosx

Support Web Service: WSGI

shell > cd wheatserver

shell > cd src

shell > make

Run
===========

-./wheatserver --app-project-path {your app path } --app-project-name {app filename} --app-name {callable object}

Config
===========

*Attention*: you must specify needed options under your choosing `protocol`

See [wheatserver.conf](https://github.com/yuyuyu101/wheatserver/blob/master/wheatserver.conf)

WSGI Example(Sample)
===========

<pre>
#sample.py which is in the wheatserver/src
HELLO_WORLD = b"Hello world!\n"

def simple_app(environ, start_response):
    """Simplest possible application object"""
    status = '200 OK'
    response_headers = [('Content-type', 'text/plain')]
    start_response(status, response_headers)
    return [HELLO_WORLD]
</pre>

-./wheatserver --app-project-name sample --app-name simple_app

WSGI Example(Django)
===========

-My Django Project Directory:
<pre>
|-signup
   |-wsgi.py
   |-bin
   |-include
   |-lib
   |-signup
   |---activity
   |-----fixtures
   |-----static
   |-------css
   |-------img
   |-------js
   |---assets
   |-----static
   |---benefits
   |-----templatetags
   |---finance
   |-----templatetags
   |---fixtures
   |---logs
   |---match
   |-----......
   |---media
   |-----......
   |---snapboard
   |-----.......
   |---specialist
   |-----static
   |-------css
   |-------js
   |-----templates
   |-------admin
   |---------specialist
   |---static
   |---templates
   |-----......
   |---third_user
   |-----static
   |---settings.py
   |---urls.py
</pre>

wsgi.py at the top of tree is the entry of Django WSGI.

<pre>
import os, sys

sys.path.append(os.path.join(os.path.dirname(os.path.abspath(__file__)), 'signup'))
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "signup.settings")

# This application object is used by the development server
# as well as any WSGI server configured to use this file.
from django.core.wsgi import get_wsgi_application
application = get_wsgi_application()
</pre>

-shell> ./wheatserver --app-project-path /Users/abcd/signup/ --app-module-name wsgi --app-name application

Admin management
===========

shell > client/client.py

enter `help` to get command list and description

Signals for controlling
===========

<pre>
# using reload to send the signal
kill -HUP `cat /tmp/wheatserver.pid`
# using kill to send the signal
kill -INT `cat /tmp/project-master.pid`
</pre>

<table>
   <tr>
      <td>Signal</td>
      <td>Description</td>
   </tr>
   <tr>
      <td>SIGHUP</td>
      <td>reload configuration file, gracefully reload all the workers and the master process</td>
   </tr>
   <tr>
      <td>SIGTERM</td>
      <td>brutally kill all the workers and the master process</td>
   </tr>
   <tr>
      <td>SIGINT</td>
      <td>brutally kill the workers and the master process</td>
   </tr>
   <tr>
      <td>SIGQUIT</td>
      <td>gracefully kill the workers and the master process</td>
   </tr>
   <tr>
      <td>SIGUSR1</td>
      <td>print statistics</td>
   </tr>
   <tr>
      <td>SIGUSR2</td>
      <td>reexec the entire master process and spawn workers</td>
   </tr>
   <tr>
      <td>SIGTTIN</td>
      <td>add one to workernumber</td>
   </tr>
   <tr>
      <td>SIGTTOU</td>
      <td>subtraction of one from workernumber</td>
   </tr>
   <tr>
      <td>SIGWINCH</td>
      <td>only gracefully kill the workers and the master process backend</td>
   </tr>
</table>
