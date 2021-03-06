.. highlight:: console


=====
Usage
=====

To see the available help run::

    $ dirhunt --help


Example::

    Usage: dirhunt [OPTIONS] [URLS]...

      Find web directories without bruteforce

    Options:
      -t, --threads INTEGER           Number of threads to use.
      -x, --exclude-flags TEXT        Exclude results with these flags. See
                                      documentation.
      -i, --include-flags TEXT        Only include results with these flags. See
                                      documentation.
      -e, --interesting-extensions TEXT
                                      The files found with these extensions are
                                      interesting
      -f, --interesting-files TEXT    The files with these names are interesting
      --stdout-flags TEXT             Return only in stdout the urls of these
                                      flags
      --progress-enabled / --progress-disabled
      --timeout INTEGER
      --max-depth INTEGER             Maximum links to follow without increasing
                                      directories depth
      --not-follow-subdomains         The subdomains will be ignored
      --exclude-sources TEXT          Exclude source engines. Possible options:
                                      robots, virustotal, google
      -p, --proxies TEXT              Set one or more proxies to alternate between
                                      them
      -d, --delay FLOAT               Delay between requests to avoid bans by the
                                      server
      --not-allow-redirects           Redirectors will not be followed
      --version
      --help                          Show this message and exit.



Find directories
----------------
You can define one or more urls, from the same domain or different. It is better if you put urls with complete
routes. This way Dirhunt will have easier to find directories.

.. code::

    $ dirhunt <url 1>[ <url 2>]

For example::

    $ dirhunt http://domain1/blog/awesome-post.html http://domain1/admin/login.html http://domain2/


Interesting extensions
----------------------
By default, Dirhunt will notify you if it find one of these extension file names: ``php``, ``zip``, ``sh``, ``asp``,
``csv`` and ``log``. You can change these extensions using the parameter ``--interesting-extensions`` (``-e``).
The files found with these extensions will be shown as they are discovered.

.. code::

    $ dirhunt <url> -e <ext 1>[,<ext 2>]

For example::

    $ dirhunt http://domain1/blog/ -e php,zip,sh

It is also possible to read extensions from files. `See "Comma separated files" <#id3>`_.


Interesting files
-----------------
Dirhunt can warn you if it finds a specific file name. By default Dirhunt will warn you if you find one of these files:
``access_log``, ``error_log``, ``error``, ``logs``, ``dump``. You can change these files using the parameter
``--interesting-files`` (``-f``). The files found will be shown as they are discovered.

.. code::

    $ dirhunt <url> -f <name 1>[,<name 2>]

For example::

    $ dirhunt http://domain1/blog/ -f access_log,error_log

You can also load file names from one or more local files::

    $ dirhunt http://domain1/blog/ -f /home/user/dict.txt,./files.txt

You can read more about this `here <#id3>`_


Exclude
-------
Filter the results using the ``--exclude-flags`` (``-x``) parameter (see the `flags section <#Flags>`_ to
see how you can filter the results).

.. code::

    $ dirhunt <url> -x <flags comma separated>

For example::

    $ dirhunt http://domain1/blog/ -x http,not_found,index_of.nothing,300-500

It is also possible to read excludes from files. See `"Comma separated files" <#id3>`_


Include
-------
This is the opposite to *exclude*. ``--include-flags`` (``-i``) allows you to show only the
results that are in the defined flags::

    $ dirhunt <url> -i <flags comma separated>

For example::

    $ dirhunt http://domain1/blog/ -i html,300-500

See the `flags section <#Flags>`_ to see how you can filter the results.

It is also possible to read includes from files. See `"Comma separated files" <#id3>`_


Flags
-----
The results are cataloged with one or several flags. Results with a **status code** include a flag with the status
number. For example, a successful response with status code ``200`` includes as flag ``200``. When filtered, ranges
of response codes can be defined. For example, ``401-500``.

The processor used to process the result is also included as a flag. The names of the processors are:

* ``generic``
* ``redirect``
* ``not_found``
* ``html``
* ``index_of``
* ``blank``

Also, some processors may have some extra flags:

* ``index_of.nothing``: 'Index Of' without interesting files.
* ``not_found.fake``: Fake 404 directory.

Other flags:

* ``wordpress``: The page belongs to a wordpress.


Threads
-------
Dirhunt makes multiple simultaneous requests using threads by default. The default number of threads is
``cpu count * 5``. If you use the ``--delay`` option, the *simultaneous requests mode* is disabled and the number of
threads by default is ``number of proxies``. If you do not use proxies in ``--delay`` mode, the default threads
number is 1. You can change the threads count using ``--threads <count>`` (``-t <count>``). Usage::

    $ dirhunt <url> --threads <count>

For example::

    $ dirhunt http://domain1/blog/ --threads 10


Delay
-----
This mode deactivates *simultaneous requests mode* and it activates a waiting time from the end of a request
and the next one begins. By default delay is **disabled**. Use this mode only if the server is restricting requests.
You can improve the performance of this option using multiple proxies. Usage::

    $ dirhunt <url> --delay <float>

For example::

    $ dirhunt http://domain1/blog/ --delay 0.1


Proxies
-------
You can use one or multiple proxies for your requests using ``--proxies`` option. Dirhunt will balance the load
between proxies. If you are not restricting requests using ``--delay`` option then dirhunt will use the proxy that is
not in use. If there is no free proxy available then dirhunt will use a random proxy. Usage::

    $ dirhunt <url> --proxies <proxy 1>[, <proxy 2>]

If you use "none" as a proxy then Dirhunt will not use a proxy. This is useful if you want to combine
proxies and your real internet connection. For example::

    $ dirhunt http://domain1/blog/ --proxies http://localhost:3128,none


Dirhunt includes an alias called ``tor`` for ``socks5://127.0.0.1:9150``. For example::

    $ dirhunt http://domain1/blog/ --proxies http://localhost:3128,none

The proxies option allows you to improve the performance of the ``--delay`` option. The delay time is independent
for each proxy. Use multiple proxies to improve your work.


Timeout
-------
By default Dirhunt only waits up to 10 seconds for each url. You can increase or decrease this time using
``--timeout``::

    $ dirhunt <url> --timeout <seconds>

For example::

    $ dirhunt http://domain1/blog/ --timeout 15


Max follow links depth
----------------------
Maximum links to follow without increasing directories depth. By default 3. For example in redirects
``/index.php > /about.php > /map.php > /contactus.php`` the last page can not redirect to another page at the same
directory level because it has exceeded the default limit of 3. Usage::

    $ dirhunt <url> --max-depth <number>

For example::

    $ dirhunt http://domain1/blog/ --max-depth 3


Not follow subdomains
---------------------
Dirhunt by default will follow all the subdomains of the domain urls. For example if Dirhunt finds webmail.site.com
on site.com dirhunt will follow the link. You can disable this feature using the flag ``--not-follow-subdomains``.
Usage::

    $ dirhunt <url> --not-follow-subdomains

For example::

    $ dirhunt http://domain1/blog/ --not-follow-subdomains


Exclude sources
---------------
Dirhunt by default will get urls from different sources. You can disable some or all of the engines using the
``--exclude-sources`` option. Usage::

    $ dirhunt <url> --exclude-sources <sources comma separated>

For example::

    $ dirhunt http://domain1/blog/ --exclude-sources robots,virustotal


Not allow redirectors
----------------------
Dirhunt by default will follow redirectors within the website (HTTP Redirectors). You can disable this feature using
the flag ``--not-allow-redirectors``. Usage::

    $ dirhunt <url> --not-allow-redirectors

For example::

    $ dirhunt http://domain1/blog/ --not-allow-redirectors


Comma separated files
---------------------
In those parameters with arguments separated by commas, it is possible to read values from one or more local files.

.. code::

    $ dirhunt <url> --<parameter> <file 1>,<file 2>

Example for **interesting files** (``-f``)::

    $ dirhunt http://domain1/blog/ -f /path/to/file1.txt,./file2.txt

It is necessary to put the complete path to the file, or the relative using ``./``. Each value of the files must be
separated by newlines.


Progress bar
------------
By default Dirhunt displays a progress bar while loading results if possible. If the progress bar causes problems, you
can disable it using ``--progress-disabled``. By default ``--progress-enabled``.

.. code::

    $ dirhunt <url> --progress-disabled

For example::

    $ dirhunt http://domain1/blog/ --progress-disabled


Version
-------
To see the Dirhunt installed version se ``--version``::

    $ dirhunt --version
    You are running Dirhunt v0.3.0 using Python 3.6.3.
    This is the latest release
    Installation path: /home/nekmo/Workspace/dirhunt/dirhunt
    Current path: /home/nekmo/Workspace/dirhunt


If you have issues with Dirhunt and you are going to open a ticket, paste this output on the issue.
Also use this command to see if Dirhunt is out of date.

.. code::

    $ dirhunt --version
    You are running Dirhunt v0.3.0 using Python 3.6.5.
    There is a new version available: 0.4.0. Upgrade it using: sudo pip install -U dirhunt
    Installation path: /home/nekmo/Workspace/dirhunt/dirhunt
    Current path: /home/nekmo/Workspace/dirhunt


External programs
-----------------
Folders that have been found can be redirected to the standard output::

    dirhunt www.domain.com/path > directories.txt

You can use standard output to run other programs to use brute force::

    for url in $(dirhunt www.domain.com/path); do
        other.py -u "$url";
    done

You can define the type of results that will be returned using flags::

    dirhunt www.domain.com/path --stdout-flags blank,not_found.fake,html > directories.txt
