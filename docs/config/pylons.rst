Configuring Pylons
==================

WSGI Middleware
---------------

A Pylons-specific middleware exists to enable easy configuration from settings:

::

    from paste.deploy import loadapp, appconfig
    from raven.contrib.pylons import Sentry

    config_filepath = '/path/to/config.ini'
    from paste.script.util.logging_config import fileConfig
    fileConfig(config_filepath)
    config = 'config:%s' % config_filepath
    application = Sentry(loadapp(config), appconfig(config).local_conf)

Configuration needs to be added into the app's namespace:

.. code-block:: ini

    [app:main]
    sentry.dsn=http://public:secret@example.com/1
    sentry.include_paths=my.package,my.other.package,
    sentry.exclude_paths=my.package.crud

Remember to set `full_stack=false` in your configuration.

Logger setup
------------

Add the following lines to your project's `.ini` file to setup `SentryHandler`:

.. code-block:: ini

    [loggers]
    keys = root, sentry

    [handlers]
    keys = console, sentry

    [formatters]
    keys = generic

    [logger_root]
    level = INFO
    handlers = console, sentry

    [logger_sentry]
    level = WARN
    handlers = console
    qualname = sentry.errors
    propagate = 0

    [handler_console]
    class = StreamHandler
    args = (sys.stderr,)
    level = NOTSET
    formatter = generic

    [handler_sentry]
    class = raven.handlers.logging.SentryHandler
    args = ('SENTRY_DSN')
    level = NOTSET
    formatter = generic

    [formatter_generic]
    format = %(asctime)s,%(msecs)03d %(levelname)-5.5s [%(name)s] %(message)s
    datefmt = %H:%M:%S

.. note:: You may want to setup other loggers as well.


