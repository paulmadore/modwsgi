﻿#summary The mod\_wsgi configuration directives.
#labels Phase-Deploy

_**If you are using mod\_wsgi, please consider making a
[donation](HowToContributeBack.md).**_

# Configuration Directives #

This document describes each of the configuration directives implemented
by the mod\_wsgi module.

  * [WSGIScriptAlias](ConfigurationDirectives#WSGIScriptAlias.md)
  * [WSGIScriptAliasMatch](ConfigurationDirectives#WSGIScriptAliasMatch.md)
  * [WSGIApplicationGroup](ConfigurationDirectives#WSGIApplicationGroup.md)
  * [WSGIDaemonProcess](ConfigurationDirectives#WSGIDaemonProcess.md)
  * [WSGISocketPrefix](ConfigurationDirectives#WSGISocketPrefix.md)
  * [WSGIProcessGroup](ConfigurationDirectives#WSGIProcessGroup.md)
  * [WSGIRestrictProcess](ConfigurationDirectives#WSGIRestrictProcess.md)
  * [WSGIImportScript](ConfigurationDirectives#WSGIImportScript.md)
  * [WSGICallableObject](ConfigurationDirectives#WSGICallableObject.md)
  * [WSGIPassAuthorization](ConfigurationDirectives#WSGIPassAuthorization.md)
  * [WSGIAuthUserScript](ConfigurationDirectives#WSGIAuthUserScript.md)
  * [WSGIAuthGroupScript](ConfigurationDirectives#WSGIAuthGroupScript.md)
  * [WSGIAccessScript](ConfigurationDirectives#WSGIAccessScript.md)
  * [WSGIScriptReloading](ConfigurationDirectives#WSGIScriptReloading.md)
  * [WSGIReloadMechanism](ConfigurationDirectives#WSGIReloadMechanism.md)
  * [WSGIOutputBuffering](ConfigurationDirectives#WSGIOutputBuffering.md)
  * [WSGICaseSensitivity](ConfigurationDirectives#WSGICaseSensitivity.md)
  * [WSGIPythonOptimize](ConfigurationDirectives#WSGIPythonOptimize.md)
  * [WSGIPythonExecutable](ConfigurationDirectives#WSGIPythonExecutable.md)
  * [WSGIPythonHome](ConfigurationDirectives#WSGIPythonHome.md)
  * [WSGIPythonPath](ConfigurationDirectives#WSGIPythonPath.md)
  * [WSGIPythonEggs](ConfigurationDirectives#WSGIPythonEggs.md)
  * [WSGIRestrictEmbedded](ConfigurationDirectives#WSGIRestrictEmbedded.md)
  * [WSGIRestrictStdin](ConfigurationDirectives#WSGIRestrictStdin.md)
  * [WSGIRestrictStdout](ConfigurationDirectives#WSGIRestrictStdout.md)
  * [WSGIRestrictSignal](ConfigurationDirectives#WSGIRestrictSignal.md)
  * [WSGIAcceptMutex](ConfigurationDirectives#WSGIAcceptMutex.md)
  * [WSGILazyInitialization](ConfigurationDirectives#WSGILazyInitialization.md)

For further examples of use see the
[Configuration Guidelines](ConfigurationGuidelines.md).

## WSGIScriptAlias ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Maps a URL to a filesystem location and designates the target as a WSGI script. |
|:-----------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)**           | `WSGIScriptAlias` _`URL-path file-path|directory-path`_                         |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)**         | server config, virtual host                                                     |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                                                                     |

The WSGIScriptAlias directive behaves in the same manner as the
[Alias](http://httpd.apache.org/docs/2.2/mod/mod_alias.html#alias)
directive, except that it additionally marks the target directory as
containing WSGI scripts, or marks the specific _file-path_ as a script, that
should be processed by mod\_wsgi's `wsgi-script` handler.

Where the target is a _directory-path_, URLs with a case-sensitive
(%-decoded) path beginning with _URL-path_ will be mapped to scripts
contained in the indicated directory.

For example:

```
WSGIScriptAlias /wsgi-scripts/ /web/wsgi-scripts/
```

A request for `http://www.example.com/wsgi-scripts/name` in this case
would cause the server to run the WSGI application defined in
`/web/wsgi-scripts/name`. This configuration is essentially equivalent
to:

```
Alias /wsgi-scripts/ /web/wsgi-scripts/
<Location /wsgi-scripts>
SetHandler wsgi-script
Options +ExecCGI
</Location>
```

Where the target is a _file-path_, URLs with a case-sensitive
(%-decoded) path beginning with _URL-path_ will be mapped to the script
defined by the _file-path_.

For example:

```
WSGIScriptAlias /name /web/wsgi-scripts/name
```

A request for `http://www.example.com/name` in this case would cause the
server to run the WSGI application defined in `/web/wsgi-scripts/name`.

If possible you should avoid placing WSGI scripts under the
[DocumentRoot](http://httpd.apache.org/docs/2.2/mod/core.html#documentroot) in
order to avoid accidentally revealing their source code if the
configuration is ever changed. The WSGIScriptAlias makes this easy by
mapping a URL and designating the location of any WSGI scripts at the same
time. If you do choose to place your WSGI scripts in a directory already
accessible to clients, do not use WSGIScriptAlias. Instead, use
[&lt;Directory&gt;](http://httpd.apache.org/docs/2.2/mod/core.html#directory),
[SetHandler](http://httpd.apache.org/docs/2.2/mod/core.html#sethandler), and
[Options](http://httpd.apache.org/docs/2.2/mod/core.html#options) as in:

```
<Directory /usr/local/apache/htdocs/wsgi-scripts>
SetHandler wsgi-script
Options ExecCGI
</Directory>
```

This is necessary since multiple _URL-paths_ can map to the same filesystem
location, potentially bypassing the WSGIScriptAlias and revealing the
source code of the WSGI scripts if they are not restricted by a
[Directory](http://httpd.apache.org/docs/2.2/mod/core.html#directory)
section.

## WSGIScriptAliasMatch ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Maps a URL to a filesystem location and designates the target as a WSGI script. |
|:-----------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)**           | `WSGIScriptAliasMatch` _`regex file-path|directory-path`_                       |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)**         | server config, virtual host                                                     |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                                                                     |

This directive is similar to the WSGIScriptAlias directive, but makes use
of regular expressions, instead of simple prefix matching. The supplied
regular expression is matched against the URL-path, and if it matches, the
server will substitute any parenthesized matches into the given string and
use it as a filename.

For example, to map a URL to scripts contained within
a directory where the script files use the `.wsgi` extension, but it
is desired that the extension not appear in the URL, use:

```
WSGIScriptAliasMatch ^/wsgi-scripts/([^/]+) /web/wsgi-scripts/$1.wsgi
```

Note that you should only use WSGIScriptAliasMatch if you know what you are
doing. In most cases you should be using WSGIScriptAlias instead. If you
use WSGIScriptAliasMatch and don't do things the correct way, then you risk
modifying the value of SCRIPT\_NAME as passed to the WSGI application and
this can stuff things up badly causing URL mapping to not work correctly
within the WSGI application or stuff up reconstruction of the full URL when
doing redirects. This is because the substitution of the matched sub
pattern from the left hand side back into the right hand side is often
critical.

If you think you need to use WSGIScriptAliasMatch, you probably don't
really. If you really really think you need it, then check on the mod\_wsgi
mailing list about how to use it properly.

## WSGIApplicationGroup ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Sets which application group WSGI application belongs to. |
|:-----------------------------------------------------------------------------------------|:----------------------------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)**           | `WSGIApplicationGroup %{GLOBAL}|%{SERVER}|%{RESOURCE}|%{ENV:variable}|name` |
| **[Default:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Default)**         | `WSGIApplicationGroup %{RESOURCE}`                        |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)**         | server config, virtual host, directory                    |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                                               |

The WSGIApplicationGroup directive can be used to specify which application
group a WSGI application or set of WSGI applications belongs to. All WSGI
applications within the same application group will execute within the
context of the same Python sub interpreter of the process handling the
request.

The argument to the WSGIApplicationGroup can be either one of four special
expanding variables or an explicit name of your own choosing. The meaning
of the special variables are:

**%{GLOBAL}**
> The application group name will be set to the empty string.

> Any WSGI applications in the global application group will always be
> executed within the context of the first interpreter created by Python
> when it is initialised. Forcing a WSGI application to run within the
> first interpreter can be necessary when a third party C extension
> module for Python has used the simplified threading API for
> manipulation of the Python GIL and thus will not run correctly within
> any additional sub interpreters created by Python.

**%{SERVER}**
> The application group name will be set to the server hostname. If the
> request arrived over a non standard HTTP/HTTPS port, the port number
> will be added as a suffix to the group name separated by a colon.

> For example, if the virtual host `www.example.com` is handling
> requests on the standard HTTP port (80) and HTTPS port (443), a request
> arriving on either port would see the application group name being set
> to `www.example.com`. If instead the virtual host was handling requests
> on port 8080, then the application group name would be set to
> `www.example.com:8080`.

**%{RESOURCE}**
> The application group name will be set to the server hostname and port
> as for the `%{SERVER}` variable, to which the value of WSGI environment
> variable `SCRIPT_NAME` is appended separated by the file separator
> character.

> For example, if the virtual host `www.example.com` was handling
> requests on port 8080 and the URL-path which mapped to the WSGI
> application was `http://www.example.com/wsgi-scripts/foo`, then the
> application group name would be set to
> `www.example.com:8080|/wsgi-scripts/foo`.

> The effect of using the `%{RESOURCE}` variable expansion is for each
> application on any server to be isolated from all others by being
> mapped to its own Python sub interpreter.

**%{ENV:variable}**
> The application group name will be set to the value of the named
> environment variable. The environment variable is looked-up via the
> internal Apache notes and subprocess environment data structures and
> (if not found there) via getenv() from the Apache server process.

In an Apache configuration file, environment variables accessible using
the `%{ENV}` variable reference can be setup by using directives such as
[SetEnv](http://httpd.apache.org/docs/2.2/mod/mod_env.html#setenv) and
[RewriteRule](http://httpd.apache.org/docs/2.2/mod/mod_rewrite.html#rewriterule).

For example, to group all WSGI scripts for a specific user when using
[mod\_userdir](http://httpd.apache.org/docs/2.2/mod/mod_userdir.html)
within the same application group, the following could be used:

```
RewriteEngine On
RewriteCond %{REQUEST_URI} ^/~([^/]+)
RewriteRule . - [E=APPLICATION_GROUP:~%1]

<Directory /home/*/public_html/wsgi-scripts/>
Options ExecCGI
SetHandler wsgi-script
WSGIApplicationGroup %{ENV:APPLICATION_GROUP}
</Directory>
```

Note that in embedded mode or a multi process daemon process group, there
will be an instance of the named sub interpreter in each process. Thus the
directive only ensures that request is handled in the named sub interpreter
within the process that handles the request. If you need to ensure that
requests for a specific user always go back to the exact same sub interpreter,
then you will need to use a daemon process group with only a single process,
or implement sticky session mechanism across a number of single process
daemon process groups.

## WSGIDaemonProcess ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Configure a distinct daemon process for running applications. |
|:-----------------------------------------------------------------------------------------|:--------------------------------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)**           | `WSGIDaemonProcess` _`name`_ `[` _`options`_ `]`              |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)**         | server config, virtual host                                   |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                                                   |

The WSGIDaemonProcess directive can be used to specify that distinct daemon
processes should be created to which the running of WSGI applications can
be delegated. Where Apache has been started as the `root` user, the
daemon processes can be run as a user different to that which the Apache
child processes would normally be run as.

When distinct daemon processes are enabled and used, the process is
dedicated to mod\_wsgi and the only thing that the processes do is run the
WSGI applications assigned to that process group. Any other Apache modules
such as PHP or activities such as serving up static files continue to be
run in the standard Apache child processes.

Note that having denoted that daemon processes should be created by using
the WSGIDaemonProcess directive, the WSGIProcessGroup directive still needs
to be used to delegate specific WSGI applications to execute within those
daemon processes.

Also note that the name of the daemon process group must be unique for the
whole server. That is, it is not possible to use the same daemon process
group name in different virtual hosts.

Options which can be supplied to the WSGIDaemonProcess directive are:

**user=name | user=#uid**
> Defines the UNIX user _name_ or numeric user _uid_ of the user that
> the daemon processes should be run as. If this option is not supplied
> the daemon processes will be run as the same user that Apache would
> run child processes and as defined by the
> [User](http://httpd.apache.org/docs/2.2/mod/mpm_common.html#user)
> directive.

> Note that this option is ignored if Apache wasn't started as the root
> user, in which case no matter what the settings, the daemon processes
> will be run as the user that Apache was started as.

> Also be aware that mod\_wsgi will not allow you to run a daemon
> process group as the root user due to the security risk of running
> a web application as root.

**group=name | group=#gid**
> Defines the UNIX group _name_ or numeric group _gid_ of the primary
> group that the daemon processes should be run as. If this option is not
> supplied the daemon processes will be run as the same group that Apache
> would run child processes and as defined by the
> [Group](http://httpd.apache.org/docs/2.2/mod/mpm_common.html#group)
> directive.

> Note that this option is ignored if Apache wasn't started as the root
> user, in which case no matter what the settings, the daemon processes
> will be run as the group that Apache was started as.

**processes=num**
> Defines the number of daemon processes that should be started in this
> process group. If not defined then only one process will be run in this
> process group.

> Note that if this option is defined as 'processes=1', then the WSGI
> environment attribute called 'wsgi.multiprocess' will be set to be True
> whereas not providing the option at all will result in the attribute
> being set to be False. This distinction is to allow for where some form
> of mapping mechanism might be used to distribute requests across
> multiple process groups and thus in effect it is still a multiprocess
> application. If you need to ensure that 'wsgi.multiprocess' is False so
> that interactive debuggers will work, simply do not specify the
> 'processes' option and allow the default single daemon process to be
> created in the process group.

**threads=num**
> Defines the number of threads to be created to handle requests in each
> daemon process within the process group.

> If this option is not defined then the default will be to create 15
> threads in each daemon process within the process group.

**umask=0nnn**
> Defines a value to be used for the umask of the daemon processes within
> the process group. The value must be provided as an octal number.

> If this option is not defined then the umask of the user that Apache is
> initially started as will be inherited by the process. Typically the
> inherited umask would be '0022'.

**home=directory**
> Defines an absolute path of a directory which should be used as the
> initial current working directory of the daemon processes within the
> process group.

> If this option is not defined, in mod\_wsgi 1.X the current working
> directory of the Apache parent process will be inherited by the daemon
> processes within the process group. Normally the current working directory
> of the Apache parent process would be the root directory. In mod\_wsgi 2.0+
> the initial current working directory will be set to be the home
> directory of the user that the daemon process runs as.

**python-path=directory | python-path=directory:directory** (2.0+)
> List of colon separated directories to add to the Python module search
> path, ie., `sys.path`.

> Note that this is not strictly the same as having set `PYTHONPATH`
> environment variable when running normal command line Python. When this
> option is used, the directories are added by calling
> `site.addsitedir()`. As well as adding the directory to
> `sys.path` this function has the effect of opening and interpreting
> any '.pth' files located in the specified directories. The option
> therefore can be used to point at the `site-packages` directory
> corresponding to a Python virtual environment created by a tool such as
> `virtualenv`, with any additional directories corresponding to
> Python eggs within that directory also being automatically added to
> `sys.path`.

**python-eggs=directory** (2.0+)
> Directory to be used as the Python egg cache directory. This is
> equivalent to having set the `PYTHON_EGG_CACHE` environment
> variable.

> Note that the directory specified must exist and be writable by the
> user that the daemon process run as.

**stack-size=nnn** (2.0+)
> The amount of virtual memory in bytes to be allocated for the stack
> corresponding to each thread created by mod\_wsgi in a daemon process.

> This option would be used when running Linux in a VPS system which has
> been configured with a quite low 'Memory Limit' in relation to the
> 'Context RSS' and 'Max RSS Memory' limits. In particular, the default
> stack size for threads under Linux is 8MB is quite excessive and could
> for such a VPS result in the 'Memory Limit' being exceeded before the
> RSS limits were exceeded. In this situation, the stack size should be
> dropped down to be in the region of 512KB (524288 bytes).

**maximum-requests=nnn**
> Defines a limit on the number of requests a daemon process should
> process before it is shutdown and restarted. Setting this to a non zero
> value has the benefit of limiting the amount of memory that a process
> can consume by (accidental) memory leakage.

> If this option is not defined, or is defined to be 0, then the daemon
> process will be persistent and will continue to service requests until
> Apache itself is restarted or shutdown.

**inactivity-timeout=sss** (2.0+)
> Defines the maximum number of seconds allowed to pass before the
> daemon process is shutdown and restarted when the daemon process has
> entered an idle state. For the purposes of this option, being idle
> means no new requests being received, or no attempts by current
> requests to read request content or generate response content for the
> defined period.

> This option exists to allow infrequently used applications running in
> a daemon process to be restarted, thus allowing memory being used to
> be reclaimed, with process size dropping back to the initial startup
> size before any application had been loaded or requests processed.

**deadlock-timeout=sss** (2.0+)
> Defines the maximum number of seconds allowed to pass before the
> daemon process is shutdown and restarted after a potential deadlock on
> the Python GIL has been detected. The default is 300 seconds.

> This option exists to combat the problem of a daemon process freezing
> as the result of a rouge Python C extension module which doesn't
> properly release the Python GIL when entering into a blocking or long
> running operation.

**shutdown-timeout=sss**
> Defines the maximum number of seconds allowed to pass when waiting
> for a daemon process to gracefully shutdown as a result of the maximum
> number of requests or inactivity timeout being reached, or when a user
> initiated SIGINT signal is sent to a daemon process. When this timeout
> has been reached the daemon process will be forced to exited even if
> there are still active requests or it is still running Python exit
> functions.

> If this option is not defined, then the shutdown timeout will be set
> to 5 seconds. Note that this option does not change the shutdown
> timeout applied to daemon processes when Apache itself is being stopped
> or restarted. That timeout value is defined internally to Apache as 3
> seconds and cannot be overridden.

**display-name=value**
> Defines a different name to show for the daemon process when using the
> 'ps' command to list processes. If the value is '%{GROUP}' then the
> name will be '(wsgi:group)' where 'group' is replaced with the name
> of the daemon process group.

> Note that only as many characters of the supplied value can be displayed
> as were originally taken up by 'argv0' of the executing process. Anything
> in excess of this will be truncated.

> This feature may not work as described on all platforms. Typically it
> also requires a 'ps' program with BSD heritage. Thus on Solaris UNIX
> the '/usr/bin/ps' program doesn't work, but '/usr/ucb/ps' does.

**receive-buffer-size=nnn**
> Defines the UNIX socket buffer size for data being received by the
> daemon process from the Apache child process.

> This option may need to be used to override small default values set by
> certain operating systems and would help avoid possibility of deadlock
> between Apache child process and daemon process when WSGI application
> generates large responses but doesn't consume request content. In
> general such deadlock problems would not arise with well behaved WSGI
> applications, but some spam bots attempting to post data to web sites
> are known to trigger the problem.

> The maximum possible value that can be set for the buffer size is
> operating system dependent and will need to be calculated through trial
> and error.

**send-buffer-size=nnn**
> Defines the UNIX socket buffer size for data being sent in the
> direction daemon process back to Apache child process.

> This option may need to be used to override small default values set by
> certain operating systems and would help avoid possibility of deadlock
> between Apache child process and daemon process when WSGI application
> generates large responses but doesn't consume request content. In
> general such deadlock problems would not arise with well behaved WSGI
> applications, but some spam bots attempting to post data to web sites
> are known to trigger the problem.

> The maximum possible value that can be set for the buffer size is
> operating system dependent and will need to be calculated through trial
> and error.

To delegate a particular WSGI application to run in a named set of daemon
processes, the WSGIProcessGroup directive should be specified in
appropriate context for that application. If WSGIProcessGroup is not used,
the application will be run within the standard Apache child processes.

If the WSGIDaemonProcess directive is specified outside of all virtual
host containers, any WSGI application can be delegated to be run within
that daemon process group. If the WSGIDaemonProcess directive is specified
within a virtual host container, only WSGI applications associated with
virtual hosts with the same server name as that virtual host can be
delegated to that set of daemon processes.

When WSGIDaemonProcess is associated with a virtual host, the error log
associated with that virtual host will be used for all Apache error log
output from mod\_wsgi rather than it appear in the main Apache error log.

For example, if a server is hosting two virtual hosts and it is desired
that the WSGI applications related to each virtual host run in distinct
processes of their own and as a user which is the owner of that virtual
host, the following could be used.

```
<VirtualHost *:80>
ServerName www.site1.com
CustomLog logs/www.site1.com-access_log common
ErrorLog logs/ww.site1.com-error_log

WSGIDaemonProcess www.site1.com user=joe group=joe processes=2 threads=25
WSGIProcessGroup www.site1.com

...
</VirtualHost>

<VirtualHost *:80>
ServerName www.site2.com
CustomLog logs/www.site2.com-access_log common
ErrorLog logs/www.site2.com-error_log

WSGIDaemonProcess www.site2.com user=bob group=bob processes=2 threads=25
WSGIProcessGroup www.site2.com

...
</VirtualHost>
```

Note that the WSGIDaemonProcess directive and corresponding features are
not available on Windows or when running Apache 1.3.

## WSGISocketPrefix ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Configure directory to use for daemon sockets. |
|:-----------------------------------------------------------------------------------------|:-----------------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)**           | `WSGISocketPrefix` _`prefix`_                  |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)**         | server config                                  |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                                    |

Defines the directory and name prefix to be used for the UNIX domain
sockets used by mod\_wsgi to communicate between the Apache child processes
and the daemon processes.

If the directive is not defined, the sockets and any related mutex lock
files will be placed in the standard Apache runtime directory. This is the
same directory that the Apache log files would normally be placed.

For some Linux distributions, restrictive permissions are placed on the
standard Apache runtime directory such that the directory is not readable
to others. This can cause problems with mod\_wsgi because the user that the
Apache child processes run as will subsequently not have the required
permissions to access the directory to be able to connect to the sockets.

When this occurs, a '503 Service Temporarily Unavailable' error response
would be received by the client. To resolve the problem, the
WSGISocketPrefix directive should be defined to point at an alternate
location. The value may be a location relative to the Apache root directory,
or an absolute path.

On systems which restrict access to the standard Apache runtime directory,
they normally provide an alternate directory for placing sockets and lock
files used by Apache modules. This directory is usually called 'run' and
to make use of this directory the WSGISocketPrefix directive would be set
as follows:

```
WSGISocketPrefix run/wsgi
```

Note, do not put the sockets in the system temporary working directory.
That is, do not go making the prefix '/tmp/wsgi'. The directory should be
one that is only writable by 'root' user, or if not starting Apache as
'root', the user that Apache is started as.

Note that the WSGISocketPrefix directive and corresponding features are not
available on Windows or when running Apache 1.3.

## WSGIProcessGroup ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Sets which process group WSGI application is assigned to. |
|:-----------------------------------------------------------------------------------------|:----------------------------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)**           | `WSGIProcessGroup %{GLOBAL}|%{ENV:variable}|name`         |
| **[Default:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Default)**         | `WSGIProcessGroup %{GLOBAL}`                              |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)**         | server config, virtual host, directory                    |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                                               |

The WSGIProcessGroup directive can be used to specify which process group a
WSGI application or set of WSGI applications will be executed in. All WSGI
applications within the same process group will execute within the context
of the same group of daemon processes.

The argument to the WSGIProcessGroup can be either one of two special
expanding variables or the actual name of a group of daemon processes setup
using the WSGIDaemonProcess directive. The meaning of the special variables
are:

**%{GLOBAL}**
> The process group name will be set to the empty string.

> Any WSGI applications in the global process group will always be
> executed within the context of the standard Apache child processes.
> Such WSGI applications will incur the least runtime overhead, however,
> they will share the same process space with other Apache modules such
> as PHP, as well as the process being used to serve up static file
> content. Running WSGI applications within the standard Apache child
> processes will also mean the application will run as the user that
> Apache would normally run as.

**%{ENV:variable}**
> The process group name will be set to the value of the named
> environment variable. The environment variable is looked-up via the
> internal Apache notes and subprocess environment data structures and
> (if not found there) via getenv() from the Apache server process.
> The result must identify a named process group setup using the
> WSGIDaemonProcess directive.

In an Apache configuration file, environment variables accessible using
the `%{ENV}` variable reference can be setup by using directives such as
[SetEnv](http://httpd.apache.org/docs/2.2/mod/mod_env.html#setenv) and
[RewriteRule](http://httpd.apache.org/docs/2.2/mod/mod_rewrite.html#rewriterule).

For example, to select which process group a specific WSGI application
should execute within based on entries in a database file, the following
could be used:

```
RewriteEngine On
RewriteMap wsgiprocmap dbm:/etc/httpd/wsgiprocmap.dbm
RewriteRule . - [E=PROCESS_GROUP:${wsgiprocmap:%{REQUEST_URI}}]

WSGIProcessGroup %{ENV:PROCESS_GROUP}
```

When using the WSGIProcessGroup directive, only daemon process groups
defined within virtual hosts with the same server name, or those defined at
global scope outside of any virtual hosts can be selected. It is not
possible to select a daemon process group which is defined within a
different virtual host. Which daemon process groups can be selected may be
further restricted if the WSGIRestrictProcess directive has been used.

Note that the WSGIProcessGroup directive and corresponding features are not
available on Windows or when running Apache 1.3.

## WSGIRestrictProcess ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Restrict which daemon process groups can be selected. |
|:-----------------------------------------------------------------------------------------|:------------------------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)**           | `WSGIRestrictProcess` _`group-1 group-2 ...`_         |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)**         | server config, virtual host, directory                |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                                           |

When using the WSGIProcessGroup directive, daemon process groups defined
within virtual hosts with the same server name, or those defined at global
scope outside of any virtual hosts can be selected. It is not possible to
select a daemon process group which is defined within a different virtual
host.

To further limit which of the available daemon process groups can be
selected, the WSGIRestrictProcess directive can be used to list a
restricted set of daemon process group names. This could be used for
example where %{ENV} substitution is being used to allow the daemon process
group to be selected from a .htaccess file for a specific user.

The main Apache configuration for this scenario might be:

```
WSGIDaemonProcess default processes=2 threads=25

<VirtualHost *:80>
ServerName www.site.com

WSGIDaemonProcess bob:1 user=bob group=bob threads=25
WSGIDaemonProcess bob:2 user=bob group=bob threads=25
WSGIDaemonProcess bob:3 user=bob group=bob threads=25

WSGIDaemonProcess joe:1 user=joe group=joe threads=25
WSGIDaemonProcess joe:2 user=joe group=joe threads=25
WSGIDaemonProcess joe:3 user=joe group=joe threads=25

SetEnv PROCESS_GROUP default
WSGIProcessGroup %{ENV:PROCESS_GROUP}

<Directory /home/bob/public_html>
Options ExecCGI
AllowOverride FileInfo
AddHandler wsgi-script .wsgi
WSGIRestrictProcess bob:1 bob:2 bob:3
SetEnv PROCESS_GROUP bob:1
</Directory>
</VirtualHost>
```

The .htaccess file within the users account could then delegate specific
WSGI applications to different daemon process groups using the
[SetEnv](http://httpd.apache.org/docs/2.2/mod/mod_env.html#setenv)
directive.

```
<Files blog.wsgi>
SetEnv PROCESS_GROUP bob:2
</Files>

<Files wiki.wsgi>
SetEnv PROCESS_GROUP bob:3
</Files>
```

Note that the WSGIDaemonProcess directive and corresponding features are
not available on Windows or when running Apache 1.3.

## WSGIImportScript ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Specify a script file to be loaded on process start. |
|:-----------------------------------------------------------------------------------------|:-----------------------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)**           | `WSGIImportScript` _`path`_ `[` _`options`_ `]`      |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)**         | server config                                        |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                                          |

The WSGIImportScript directive can be used to specify a script file to be
loaded when a process starts. Options must be provided to indicate the name
of the process group and the application group into which the script will
be loaded.

The options which must supplied to the WSGIImportScript directive are:

**process-group=name**
> Specifies the name of the process group for which the script file will
> be loaded.

> The name of the process group can be set to the special value
> '%{GLOBAL}' which denotes that the script file be loaded for the Apache
> child processes. Any other value indicates appropriate process group
> for mod\_wsgi daemon mode.

**application-group=name**
> Specifies the name of the application group within the specified
> process for which the script file will be loaded.

> The name of the application group can be set to the special value
> '%{GLOBAL}' which denotes that the script file be loaded within the
> context of the first interpreter created by Python when it is
> initialised. Otherwise, will be loaded into the interpreter for the
> specified application group.

Because the script files are loaded prior to beginning to accept any
requests, any delay in loading the script will not cause actual requests to
be blocked. As such, the WSGIImportScript can be used to preload a WSGI
application script file on process start so that it is ready when actual
user requests arrive. For where there are multiple processes handling
requests, this can reduce or eliminate the apparent stalling of an
application when performing a restart of Apache or a daemon mode process
group.

## WSGICallableObject ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Sets the name of the WSGI application callable. |
|:-----------------------------------------------------------------------------------------|:------------------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)**           | `WSGICallableObject %{ENV:variable}|name`       |
| **[Default:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Default)**         | `WSGICallableObject application`                |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)**         | server config, virtual host, directory, .htaccess |
| **[Override:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Override)**       | FileInfo                                        |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                                     |

The WSGICallableObject directive can be used to override the name of the
Python callable object in the script file which is used as the entry point
into the WSGI application.

When `%{ENV}` is being used, the environment variable is looked-up via the
internal Apache notes and subprocess environment data structures and (if
not found there) via getenv() from the Apache server process.

In an Apache configuration file, environment variables accessible using
the `%{ENV}` variable reference can be setup by using directives such as
[SetEnv](http://httpd.apache.org/docs/2.2/mod/mod_env.html#setenv) and
[RewriteRule](http://httpd.apache.org/docs/2.2/mod/mod_rewrite.html#rewriterule).

Note that the name of the callable object must be an object present at
global scope within the WSGI script file. It is not possible to use a dotted
path to refer to a sub object of a module imported by the WSGI script file.

## WSGIPassAuthorization ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Enable/Disable passing of authorisation headers. |
|:-----------------------------------------------------------------------------------------|:-------------------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)**           | `WSGIPassAuthorization On|Off`                   |
| **[Default:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Default)**         | `WSGIPassAuthorization Off`                      |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)** 1.X     | server config, virtual host, directory           |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)** 2.0+    | server config, virtual host, directory, .htaccess |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                                      |

**Note: Ability to use this option in .htaccess file was only added in 2.0c5.**

The WSGIPassAuthorization directive can be used to control whether HTTP
authorisation headers are passed through to a WSGI application in the
`HTTP_AUTHORIZATION` variable of the WSGI application environment when
the equivalent HTTP request headers are present. This option would need to
be set to `On` if the WSGI application was to handle authorisation
rather than Apache doing it.

Authorisation headers are not passed through by default as doing so could
leak information about passwords through to a WSGI application which should
not be able to see them when Apache is performing authorisation. If Apache
is performing authorisation, a WSGI application can still find out what
type of authorisation scheme was used by checking the variable
`AUTH_TYPE` of the WSGI application environment. The login name of the
authorised user can be determined by checking the variable
`REMOTE_USER`.

## WSGIAuthUserScript ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Specify script implementing an authentication provider. |
|:-----------------------------------------------------------------------------------------|:--------------------------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)**           | `WSGIAuthUserScript` _`path`_ `[` _`options`_ `]`       |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)** 2.0+    | directory, .htaccess                                    |
| **[Override:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Override)**       | AuthConfig                                              |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                                             |

The WSGIAuthUserScript directive can be used to specify a script which
implements an Apache authentication provider.

Such an authentication provider can be used where you want Apache to worry
about the handshaking related to HTTP Basic and Digest authentication and
you only wish to deal with supplying the user credentials for authenticating
the user.

If using at least Apache 2.2, other Apache modules implementing custom
authentication mechanisms can also make use of the authentication provider
if they are using the corresponding Apache C API for accessing them.

More detailed information on using the WSGIAuthUserScript directive can be
found in [Access Control Mechanisms](AccessControlMechanisms.md).

The options which can be supplied to the WSGIAuthUserScript directive are:

**application-group=name**
> Specifies the name of the application group within the specified
> process for which the script file will be loaded.

> If the 'application-group' option is not supplied, the special value
> '%{GLOBAL}' which denotes that the script file be loaded within the
> context of the first interpreter created by Python when it is
> initialised will be used. Otherwise, will be loaded into the
> interpreter for the specified application group.

Note that the script always runs in processes associated with embedded
mode. It is not possible to delegate the script such that it is run within
context of a daemon process.

## WSGIAuthGroupScript ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Specify script implementing group authorisation. |
|:-----------------------------------------------------------------------------------------|:-------------------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)**           | `WSGIAuthGroupScript` _`path`_ `[` _`options`_ `]` |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)** 2.0+    | directory, .htaccess                             |
| **[Override:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Override)**       | AuthConfig                                       |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                                      |

The WSGIAuthGroupScript directive provides a mechanism for implementing
group authorisation using the Apache 'Require' directive.

More detailed information on using the WSGIAuthGroupScript directive can be
found in [Access Control Mechanisms](AccessControlMechanisms.md).

The options which can be supplied to the WSGIAuthGroupScript directive are:

**application-group=name**
> Specifies the name of the application group within the specified
> process for which the script file will be loaded.

> If the 'application-group' option is not supplied, the special value
> '%{GLOBAL}' which denotes that the script file be loaded within the
> context of the first interpreter created by Python when it is
> initialised will be used. Otherwise, will be loaded into the
> interpreter for the specified application group.

Note that the script always runs in processes associated with embedded
mode. It is not possible to delegate the script such that it is run within
context of a daemon process.

## WSGIAccessScript ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Specify script implementing host access controls. |
|:-----------------------------------------------------------------------------------------|:--------------------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)**           | `WSGIAccessScript` _`path`_ `[` _`options`_ `]`   |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)** 2.0+    | directory, .htaccess                              |
| **[Override:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Override)**       | AuthConfig                                        |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                                       |

The WSGIAccessScript directive provides a mechanism for implementing host
access controls.

More detailed information on using the WSGIAccessScript directive can be
found in [Access Control Mechanisms](AccessControlMechanisms.md).

The options which can be supplied to the WSGIAccessScript directive are:

**application-group=name**
> Specifies the name of the application group within the specified
> process for which the script file will be loaded.

> If the 'application-group' option is not supplied, the special value
> '%{GLOBAL}' which denotes that the script file be loaded within the
> context of the first interpreter created by Python when it is
> initialised will be used. Otherwise, will be loaded into the
> interpreter for the specified application group.

Note that the script always runs in processes associated with embedded
mode. It is not possible to delegate the script such that it is run within
context of a daemon process.

## WSGIScriptReloading ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Enable/Disable detection of WSGI script file changes. |
|:-----------------------------------------------------------------------------------------|:------------------------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)**           | `WSGIScriptReloading On|Off`                          |
| **[Default:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Default)**         | `WSGIScriptReloading On`                              |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)**         | server config, virtual host, directory, .htaccess     |
| **[Override:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Override)**       | FileInfo                                              |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                                           |

The WSGIScriptReloading directive can be used to control whether changes to
WSGI script files trigger the reloading mechanism. By default script
reloading is enabled and a change to the WSGI script file will trigger
whichever reloading mechanism is specified by the WSGIReloadMechanism
directive.

## WSGIReloadMechanism ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Reload mechanism used when script changes. |
|:-----------------------------------------------------------------------------------------|:-------------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)** (1.X)     | `WSGIReloadMechanism Module|Interpreter`   |
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)** (2.0)     | `WSGIReloadMechanism Module|Process`       |
| **[Default:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Default)** 1.X, 2.0 (embedded) | `WSGIReloadMechanism Module`               |
| **[Default:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Default)** 2.0 (daemon) | `WSGIReloadMechanism Process`              |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)**         | server config, virtual host, directory, .htaccess |
| **[Override:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Override)**       | FileInfo                                   |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                                |

**Note: The 'Interpreter' option described here was removed in mod\_wsgi
version 2.0. The 'Process' option was only added in in mod\_wsgi
version 2.0. The complete WSGIReloadMechanism directive was thence
removed in mod\_wsgi 3.0 with the 'Process' option effectively being the
default and only choice for dameon mode.**

The WSGIReloadMechanism directive can be used to control which type of
reload mechanism is used when detection of changes to a WSGI script file
are enabled.

The default reload mechanism depends on the version of mod\_wsgi being used
and whether or not the WSGI application is running in embedded mode or
daemon mode. If using mod\_wsgi version 1.X, the default reload mechanism
is 'Module'. If using mod\_wsgi version 2.0+ (actually 2.0c5+) and the WSGI
application is running in embedded mode, the default reload mechanism is
'Module'. If however using mod\_wsgi version 2.0+ (actually 2.0c5+) and the
WSGI application is running in daemon mode, the default reload mechanism
is 'Process'.

When the 'Module' reload mechanism is used, the Python module which the
WSGI script file was loaded into will be destroyed when a change in the
script file is detected. The WSGI script file will then be reloaded into a
fresh Python module within the existing sub interpreter instance prior to
the application entry point it contains being executed.

An alternative to the 'Module' reload mechanism in mod\_wsgi version 1.X is
the 'Interpreter' reload mechanism. When this is used, the complete sub
interpreter associated with the application group the WSGI script is a part
of will be destroyed. A fresh sub interpreter instance will then be created
and the WSGI script file reloaded into a fresh Python module within the new
sub interpreter instance prior to the application entry point it contains
being executed.

An alternative to the 'Module' reload mechanism in mod\_wsgi version 2.0 and
later is the 'Process' reload mechanism. Because this involves restarting
of the whole process the WSGI application is running in, it is only
available for WSGI applications delegated to a mod\_wsgi daemon process
using the WSGIDaemonProcess and WSGIProcessGroup directives. When a change
to the WSGI script file has been detected, the daemon process in which the
application runs will be shutdown and restarted before the request is
passed to the application for processing.

Note that the 'Interpreter' option for the reload mechanism has no effect
on the first interpreter instance created by Python and indentified by
using the value '%{GLOBAL}' with the WSGIApplicationGroup directive. For
this interpreter the behaviour as defined for the 'Module' option always
applies. This is because this interpreter instance cannot be destroyed and
must be kept as is for the life of the process.

Similarly, if the 'Process' option is specified for a WSGI application
running in embedded mode, that is where WSGIProcessGroup is '%{GLOBAL}'
or not defined in that context, the 'Module' option always applies.

Also note that the 'Module' reload mechanism only applies to the Python
module the WSGI script file was loaded into, it does not apply to standard
Python modules. If you make changes to normal Python modules that have
already been loaded by an application, you will either need to use the
'Interpreter' option for WSGIReloadMechanism, or restart Apache in order to
see the changes.

When the 'Module' reload mechanism is being used, if a WSGI script file
makes modifications to `sys.path` or other global data structures and
the changes are additive, checks should first be made to ensure that the
data does not already exist, else duplicate data will be added every time
the WSGI script file is reloaded.

This means that when updating `sys.path`, instead of using:

```
import sys
sys.path.append('/usr/local/wsgi/modules')
```

you should use:

```
import sys
path = '/usr/local/wsgi/modules'
if path not in sys.path:
    sys.path.append(path)
```

## WSGICaseSensitivity ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Define whether file system is case sensitive. |
|:-----------------------------------------------------------------------------------------|:----------------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)**           | `WSGICaseSensitivity On|Off`                  |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)** 1.X     | server config, virtual host, directory, .htaccess |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)** 2.0+    | server config                                 |
| **[Override:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Override)**       | FileInfo                                      |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                                   |

**Note: From mod\_wsgi version 2.0 onwards this option will only be able to
be set in the main server config and will apply to the whole site. All
paths therefore would need to be located in a filesystem with the same case
convention.**

When mod\_wsgi is used on the Windows and MacOS X platforms, it will assume
that the filesystem in use is case insensitive. This is necessary to ensure
that the module caching system works correctly and only one module is
retained in memory where paths with different case are used to identify the
same script file. On other platforms it will always be assumed that a case
sensitive file system is used.

The WSGICaseSensitivity directive can be used explicitly to specify for a
particular WSGI application whether the file system the script file is
stored in is case sensitive or not, thus overriding the default for any
platform. A value of On indicates that the filesystem is case sensitive.

## WSGIPythonOptimize ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Enables basic Python optimisation features. |
|:-----------------------------------------------------------------------------------------|:--------------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)**           | `WSGIPythonOptimize [0|1|2]`                |
| **[Default:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Default)**         | `WSGIPythonOptimize 0`                      |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)**         | server config                               |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                                 |

Sets the level of Python compiler optimisations. The default is '0' which
means no optimisations are applied.

Setting the optimisation level to '1' or above will have the effect of
enabling basic Python optimisations and changes the filename extension for
compiled (bytecode) files from `.pyc` to `.pyo`.

On the Windows platform, optimisation level of '0' apparently results in
the same outcome as if the optimisation level had been set to '1'.

When the optimisation level is set to '2', doc strings will not be
generated and thus not retained. This may techically result in a smaller
memory footprint if all `.pyo` files were compiled at this optimisation
level, but may cause some Python packages which interrogate doc strings in
some way to fail.

Since all the installed `.pyo` files in your Python installation are
not likely to be installed with level '2' optimisation, the gain from using
this level of optimisation will probably be negligible if any. This is
because potentially only the Python code for your own application code will
be compiled with this level of optimisation. This will be the case as the
`.pyo` files will aready exist for modules in the standard Python
library and they will be used as is, rather than them being regenerated
with a higher level of optimisation than they might be. Use of level '2'
optimisation is therefore discouraged.

This directive will have no affect if mod\_python is being loaded into Apache
at the same time as mod\_wsgi as mod\_python will in that case be responsible
for initialising Python.

Overall, if you do not understand what the normal 'python' executable '-O'
option does, how the Python runtime changes it behaviour as a result, and
you don't know exactly how your application would be affected by enabling
this option, then do not use this option. In other words, stop trying to
prematurely optimise the performance of your application through shortcuts.
You will get much better performance gains by looking at the design of your
application and eliminating bottlenecks within it and how it uses any
database. So, put the gun down and back away, it will be better for all
concerned.

## WSGIPythonExecutable ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Absolute path to Python executable. |
|:-----------------------------------------------------------------------------------------|:------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)** 1.X       | `WSGIPythonExecutable` _`file-path`_ |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)**         | server config                       |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                         |

**Note: The WSGIPythonExecutable directive described here has been removed
in mod\_wsgi version 2.0. The WSGIPythonHome directive should be used
instead.**

Can be used to define the location of the Python executable. This should
only need to be defined where the Python executable is not in the
`PATH` of the user that Apache runs as, or where a system has multiple
versions of Python installed in different locations in the file system,
especially different installations of the same major/minor version, and the
installation that Apache finds in its `PATH` is not the desired one.

When this directive is used to define the location of the Python executable,
the Python executable is not actually executed. Instead, Python when
initialised uses this location as a starting point to try and determine
where its library files are installed.

For some platforms, this directive is the same as having set the
environment variable `PYTHONEXECUTABLE` in the environment of the user
that Apache executes as. If this directive is used it will override any
setting of `PYTHONEXECUTABLE` in the environment of the user that
Apache executes as.

This directive is not available on the Windows platform. Any problems on
the Windows platform should be resolved by appropriate changes to the
Windows registry.

This directive will have no affect if mod\_python is being loaded into Apache
at the same time as mod\_wsgi as mod\_python will in that case be responsible
for initialising Python.

## WSGIPythonHome ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Absolute path to Python prefix/exec\_prefix directories. |
|:-----------------------------------------------------------------------------------------|:---------------------------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)**           | `WSGIPythonHome` _`prefix|prefix:exec_prefix`_           |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)**         | server config                                            |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                                              |

Used to indicate to Python when it is initialised where its library files
are installed. This should be defined where the Python executable is not in
the `PATH` of the user that Apache runs as, or where a system has
multiple versions of Python installed in different locations in the file
system, especially different installations of the same major/minor version,
and the installation that Apache finds in its `PATH` is not the desired
one.

This directive can also be used to indicate a Python virtual environment
created using a tool such as `virtualenv`, to be used for the whole of
mod\_wsgi.

When this directive is used it should be supplied the prefix for the
directories containing the platform independent and system dependent Python
library files. The directories should be separated by a ':'. If the same
directory is used for both, then only the one directory path needs to be
supplied. Where the directories are the same, this can usually be
determined by looking at the value of the `sys.prefix` variable for the
version of Python being used.

Note that the Python installation being referred to using this directive
must be the same major/minor version of Python that mod\_wsgi was compiled
for. If you want to use a different version of major/minor version of
Python than currently used, you must recompile mod\_wsgi against the alternate
version of Python.

This directive is the same as having set the environment variable
`PYTHONHOME` in the environment of the user that Apache executes as. If
this directive is used it will override any setting of `PYTHONHOME` in
the environment of the user that Apache executes as.

This directive will have no affect if mod\_python is being loaded into Apache
at the same time as mod\_wsgi as mod\_python will in that case be responsible
for initialising Python.

This directive is not available on Windows systems. Note that mod\_wsgi 1.X
will not actually reject this directive if listed in the configuration,
however, it also will not do anything either. This is because on Windows
systems Python ignores the `PYTHONHOME` environment variable and always
seems to use the location of the Python DLL for determining where the
library files are located.

## WSGIPythonPath ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Additional directories to search for Python modules. |
|:-----------------------------------------------------------------------------------------|:-----------------------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)**           | `WSGIPythonPath` _`directory|directory-1:directory-2:...`_ |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)**         | server config                                        |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                                          |

**Note: The directive behaves differently between mod\_wsgi version 1.X and
version 2.0.**

Used to specify additional directories to search for Python modules. If
multiple directories are specified they should be separated by a ':' if
using a UNIX like system, or ';' if using Windows. If any part of a
directory path contains a space character, the complete argument string to
WSGIPythonPath must be quoted.

When using mod\_wsgi version 1.X, this directive is the same as having set
the environment variable `PYTHONPATH` in the environment of the user
that Apache executes as. If this directive is used it will override any
setting of `PYTHONPATH` in the environment of the user that Apache
executes as. The end result is that the listed directories will be added
to `sys.path`.

Note that in mod\_wsgi version 1.X this applies to all Python sub
interpreters created, be they in the Apache child processes when embedded
mode is used, or in distinct daemon processes when daemon mode is used. It
is not possible to define this differently for mod\_wsgi daemon processes.
If additional directories need to be added to the module search path for a
specific WSGI application it should be done within the WSGI application
script itself.

When using mod\_wsgi version 2.0, this directive does not have the same
affect as having set the environment variable `PYTHONPATH`. In fact, if
`PYTHONPATH` is set in the environment of the user that Apache is
started as, any directories so defined will still be added to
`sys.path` and they will not be overridden.

The difference with this directive when using mod\_wsgi 2.0 is that each
directory listed will be added to the end of `sys.path` by calling
`site.addsitedir()`. By using this function, as well as the directory
being added to `sys.path`, any '.pth' files located in the directories
will be opened and processed. Thus, if the directories contain Python eggs,
any associated directories corresponding to those Python eggs will in turn
also be added automatically to `sys.path`.

Note however that when using mod\_wsgi 2.0, this directive only sets up the
additional Python module search directories for interpreters created in the
Apache child processes where embedded mode is used. If directories need to
be specified for interpreters running in daemon processes, the
'python-path' option to the WSGIDaemonProcess directive corresponding to
that daemon process should instead be used.

In mod\_wsgi version 2.0, because directories corresponding to Python eggs
are automatically added to `sys.path`, the directive can be used to
point at the `site-packages` directory corresponding to a Python
virtual environment created by a tool such as `virtualenv`.

For mod\_wsgi 1.X, this directive will have no affect if mod\_python is being
loaded into Apache at the same time as mod\_wsgi as mod\_python will in that
case be responsible for initialising Python.

## WSGIPythonEggs ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Directory to use for Python eggs cache. |
|:-----------------------------------------------------------------------------------------|:----------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)** 2.0+      | `WSGIPythonEggs` _`directory`_          |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)**         | server config                           |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                             |

**Note: This directive was only added in mod\_wsgi version 2.0.**

Used to specify the directory to be used as the Python eggs cache directory
for all sub interpreters created within embedded mode. This directive
achieves the same affect as having set the `PYTHON_EGG_CACHE`
environment variable.

Note that the directory specified must exist and be writable by the user
that the Apache child processes run as. The directive only applies to
mod\_wsgi embedded mode. To set the Python eggs cache directory for mod\_wsgi
daemon processes, use the 'python-eggs' option to the WSGIDaemonProcess
directive instead.

## WSGIRestrictEmbedded ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Enable restrictions on use of embedded mode. |
|:-----------------------------------------------------------------------------------------|:---------------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)**           | `WSGIRestrictEmbedded On|Off`                |
| **[Default:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Default)** 2.0+    | `WSGIRestrictEmbedded Off`                   |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)**         | server config                                |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                                  |

The WSGIRestrictEmbedded directive determines whether mod\_wsgi embedded
mode is enabled or not. If set to 'On' and the restriction on embedded mode
is therefore enabled, any attempt to make a request against a WSGI
application which hasn't been properly configured so as to be delegated to
a daemon mode process will fail with a HTTP internal server error response.

This option does not exist on Windows, or Apache 1.3 or any other
configuration where daemon mode is not available.

## WSGIRestrictStdin ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Enable restrictions on use of STDIN. |
|:-----------------------------------------------------------------------------------------|:-------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)**           | `WSGIRestrictStdin On|Off`           |
| **[Default:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Default)**         | `WSGIRestrictStdin On`               |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)**         | server config                        |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                          |

A well behaved Python WSGI application should never attempt to read any
input directly from `sys.stdin`. This is because ways of hosting WSGI
applications such as CGI use standard input as the mechanism for receiving
the content of a request from the web server. If a WSGI application were to
directly read from `sys.stdin` it could interfere with the operation of
the WSGI adapter and result in corruption of the input stream.

In the interests of promoting portability of WSGI applications, mod\_wsgi
restricts access to `sys.stdin` and will raise an exception if an
attempt is made to use `sys.stdin` explicitly.

The only time that one might want to remove this restriction is if the Apache
web server is being run in debug or single process mode for the purposes of
being able to run an interactive Python debugger such as `pdb`.

## WSGIRestrictStdout ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Enable restrictions on use of STDOUT. |
|:-----------------------------------------------------------------------------------------|:--------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)**           | `WSGIRestrictStdout On|Off`           |
| **[Default:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Default)**         | `WSGIRestrictStdout On`               |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)**         | server config                         |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                           |

A well behaved Python WSGI application should never attempt to write any
data directly to `sys.stdout` or use the `print` statement without
directing it to an alternate file object. This is because ways of hosting
WSGI applications such as CGI use standard output as the mechanism for
sending the content of a response back to the web server. If a WSGI
application were to directly write to `sys.stdout` it could interfere
with the operation of the WSGI adapter and result in corruption of the
output stream.

In the interests of promoting portability of WSGI applications, mod\_wsgi
restricts access to `sys.stdout` and will raise an exception if an
attempt is made to use `sys.stdout` explicitly.

The only time that one might want to remove this restriction is purely out
of convencience of being able to use the `print` statement during
debugging of an application, or if some third party module or WSGI
application was errornously using `print` when it shouldn't. If
restrictions on using `sys.stdout` are removed, any data written to
it will instead be sent through to `sys.stderr` and will appear in
the Apache error log file.

## WSGIRestrictSignal ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Enable restrictions on use of signal(). |
|:-----------------------------------------------------------------------------------------|:----------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)**           | `WSGIRestrictSignal On|Off`             |
| **[Default:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Default)**         | `WSGIRestrictSignal On`                 |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)**         | server config                           |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                             |

A well behaved Python WSGI application should not in general register any
signal handlers of its own using `signal.signal()`. The reason for this
is that the web server which is hosting a WSGI application will more than
likely register signal handlers of its own. If a WSGI application were to
override such signal handlers it could interfere with the operation of the
web server, preventing actions such as server shutdown and restart.

In the interests of promoting portability of WSGI applications, mod\_wsgi
restricts use of `signal.signal()` and will ensure that any attempts
to register signal handlers are ignored. A warning notice will be output
to the Apache error log indicating that this action has been taken.

If for some reason there is a need for a WSGI application to register some
special signal handler this behaviour can be turned off, however an
application should avoid the signals `SIGTERM`, `SIGINT`,
`SIGHUP`, `SIGWINCH` and `SIGUSR1` as these are all used by
Apache.

Apache will ensure that the signal `SIGPIPE` is set to `SIG_IGN`.
If a WSGI application needs to override this, it must ensure that it is
reset to `SIG_IGN` before any Apache code is run. In a multi threaded
MPM this would be practically impossible to ensure so it is preferable that
the handler for `SIG_PIPE` also not be changed.

Apache does not use `SIGALRM`, but it is generally preferable that
other techniques be used to achieve the same affect.

Do note that if enabling the ability to register signal handlers, such a
registration can only reliably be done from within code which is
implemented as a side effect of importing a script file identified by the
WSGIImportScript directive. This is because signal handlers can only be
registered from the main Python interpreter thread, and request handlers
when using embedded mode and a multithreaded Apache MPM would generally
execute from secondary threads. Similarly, when using daemon mode, request
handlers would executed from secondary threads. Only code run as a side
effect of WSGIImportScript is guaranteed to be executed in main Python
interpreter thread.

## WSGIAcceptMutex ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Specify type of accept mutex used by daemon processes. |
|:-----------------------------------------------------------------------------------------|:-------------------------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)**           | `WSGIAcceptMutex Default|method`                       |
| **[Default:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Default)**         | `WSGIAcceptMutex Default`                              |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)**         | server config                                          |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                                            |

The WSGIAcceptMutex directive sets the method that mod\_wsgi will use to
serialize multiple daemon processes in a process group accepting requests
on a socket connection from the Apache child processes. If this directive
is not defined then the same type of mutex mechanism as used by Apache for
the main Apache child processes when accepting connections from a client
will be used. If set the method types are the same as for the Apache
[AcceptMutex](http://httpd.apache.org/docs/2.2/mod/mpm_common.html#acceptmutex)
directive.

Note that the WSGIAcceptMutex directive and corresponding features are
not available on Windows or when running Apache 1.3.

## WSGILazyInitialization ##

| **[Description:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Description)** | Enable/disable lazy initialisation of Python. |
|:-----------------------------------------------------------------------------------------|:----------------------------------------------|
| **[Syntax:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Syntax)** 3.0+      | `WSGILazyInitialization On|Off`               |
| **[Default:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Default)** 3.0+    | `WSGILazyInitialization On`                   |
| **[Context:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Context)**         | server config                                 |
| **[Module:](http://httpd.apache.org/docs/2.2/mod/directive-dict.html#Module)**           | mod\_wsgi.c                                   |

**Note: This directive was only added in mod\_wsgi version 3.0.**

The WSGILazyInitialization directives sets whether or not the Python
interpreter is preinitialised within the Apache parent process or whether
lazy initialisation is performed, and the Python interpreter only
initialised in the Apache server processes or mod\_wsgi daemon processes
after they have forked from the Apache parent process.

In versions of mod\_wsgi prior to version 3.0 the Python interpreter was
always preinitialised in the Apache parent process. This did mean that
theoretically some benefit in memory usage could be derived from delayed
copy on write semantics of memory inherited by child processes that was
initialised in the parent. This memory wasn't significant however and was
tempered by the fact that the Python interpreter when destroyed and then
reinitialised in the Apache parent process on an Apache restart, would with
some Python versions leak memory. This meant that if a server had many
restarts performed, the Apache parent process and thus all forked child
processes could grow in memory usage over time, eventually necessitating
Apache be completely stopped and then restarted.

This issue of memory leaks with the Python interpreter reached an extreme
with Python 3.0, where by design, various data structures would not be
destroyed on the basis that it would be reused when Python interpreter was
reinitialised within the same process. The problem is that when an Apache
restart is performed, mod\_wsgi and the Python library are unloaded from
memory, with the result that the references to that memory would be lost
and so a real memory leak, of significant size and much worse that older
versions of Python, would result.

As a consequence, with mod\_wsgi 3.0 and onwards, the Python interpreter is
not initialised by default in the Apache parent process for any version of
Python. This avoids completely the risk of cummulative memory leaks by the
Python interpreter on a restart into the Apache parent process, albeit with
potential for a slight increase in child process memory sizes. If need be,
the existing behaviour can be restored by setting the directive with the
value 'Off'.

A further upside of using lazy initialisation is that if you are using
daemon mode only, ie., not using embedded mode, you can completely turn off
initialisation of the Python interpreter within the main Apache server
child process. Unfortunately, because it isn't possible in the general case
to know whether embedded mode will be needed or not, you will need to
manually set the configuration to do this. This can be done by setting:

```
WSGIRestrictEmbedded On
```

With restrictions on embedded mode enabled, any attempt to run a WSGI
application in embedded mode will fail, so it will be necessary to ensure
all WSGI applications are delegated to run in daemon mode. Although WSGI
applications will be restricted from being run in embedded mode and the
Python interpreter therefore not initialised, it will fallback to being
initialised if you use any of the Python hooks for access control,
authentication or authorisation providers, or WSGI application dispatch
overrides.

Note that if mod\_python is being used in the same Apache installation,
because mod\_python takes precedence over mod\_wsgi in initialising the
Python interpreter, lazy initialisation cannot be done and so Python
interpreter will continue to be preinitialised in the Apache parent process
regardless of the setting of WSGILazyInitialization. Use of mod\_python will
thus perpetuate the risk of memory leaks and growing memory use of Apache
process. This is especially the case since mod\_python doesn't even properly
destroy the Python interpreter in the Apache parent process on a restart
and so all memory associated with the Python interpreter is leaked and not
just that caused by the Python interpreter when it is destroyed and doesn't
clean up after itself.