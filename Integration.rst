.. _integration:

===========
Integration
===========

Integrating Stash into a project consists of three components- Initializing the Drivers and Pool, making the subsequent
Pool available for consumption, and actually utilizing Stash to cache data.

The easiest way to integrate Stash is to use an existing framework that takes care of the Initialize and Availability
portions of integration- the TedivmStashBundle for Symfony is one example of this. This is by no means a requirement
though, and integrating Stash can be a rather straight forward process.

This page has general pointers that everyone should consider when working with Stash.


Typehinting
===========

There will be times when Stash objects need to be passed around. A class may have a setCache($pool) function, for
example, and as a developer it makes sense to typehint for a Pool object. In instances like this it is important to
typehint against the supplied Interfaces themselves, rather than the specific classes, in order to facilitate
interoperability with code that extends Stash.

Interfaces are all in the Stash\\Interfaces namespace and consist of-

* Stash\\Interfaces\\PoolInterface
* Stash\\Interfaces\\ItemInterface
* Stash\\Interfaces\\DriverInterface


DriverList
==========

The Stash\\DriverList class contains static functions that ease integration.

* *getAllDrivers()* - Returns an array of drivers and their respective classes.
* *getAvailableDrivers()* - Returns the above list, filtered by drivers that can run on the current system.
* *getDriverClass($name)* - Takes the shortname of a Driver and returns it's class name.
* *registerDriver($name, $classname)* - Registers new Drivers with the class.

Configuration
=============

All Drivers have a "setOptions" function that takes in options as Key => Value pairs in an array. This makes converting
saved configurations (such as from an XML, YAML, or INI file) a fairly straight forward process of taking that "options"
sections and converting it to an array. Complete configuration options for each Driver are available for use on any
additional validation that developers may want to provide.


Driver Wrapping
===============

There is almost never a reason not to use the Composite Driver to put the Ephemeral Driver in front of the primary
caching Driver. It reduces load on the caching system and increases performance by reducing duplicate requests. It also
helps normalize development as everything can be left the same except that the persistent drivers are left off.


Logging
=======

Stash supports logging through third party logging libraries such as monolog. It does this by following PSR-3, with the
Pool class utilizing the `PSR-3 LoggerAwareInterface <http://www.php-fig.org/psr/3/>`_. PSR-3 compliant logging libraries
can be injected into the Pool class through the setLogger method.

Logging is a powerful feature to give system admins, as it gives them access to errors that occur in with the logging
system. Stash suppresses many types of errors to allow processes to continue as normal, making logging an important part
of integration.


Maintenance Actions
===================

Some drivers require that maintenance action be performed regular. The FileSystem and SQLite drivers, as an example,
need to remove old data as they can't do it automatically. These operations are all abstracted away behind Pool->purge()
but will need to be called regularly by the integrating applications.

The Pool->purge() function should be called as a stand alone operation. Depending on the Driver being used this can take
longer than your typical action and should not be tied to a user request. It is also recommended that this be run from a
command line where possible, such as in an automated cron job, in order to improve performance.


Standards Compliance
====================

In order to make interaction with other projects easier, Stash complies with:

* `PSR-1 <http://www.php-fig.org/psr/1/>`_.,
* `PSR-2 <http://www.php-fig.org/psr/2/>`_.,
* `PSR-3 <http://www.php-fig.org/psr/3/>`_.
* `PSR-4 <http://www.php-fig.org/psr/4/>`_.
* `PSR-6 <http://www.php-fig.org/psr/6/>`_.
* `semver 2.0.0 <http://semver.org/spec/v2.0.0.html>`_.
