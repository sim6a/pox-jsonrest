#POX JSON-REST API

##JSON-REST API

JSON-REST is a web interface that provides some RESTful services to communicate
with the POX controller and its managed resources through HTTP methods over
URL. JSON-REST is based on the micro web-framework Bottle (included as
[pox.lib.bottle](https://github.com/festradasolano/pox/blob/master/pox/lib/bottle.py)).

JSON-REST enables to set web host and port to deploy RESTful services. For
example, to listen any host in port 8082, invoke this:

    $ ./pox.py web.jsonrest --host=0.0.0.0 --port=8082

Some services depend on other modules to perform their tasks. If you do not
launch these modules, JSON-REST does it. Following these modules:
 - [pox.openflow.discovery](https://github.com/festradasolano/pox/blob/master/pox/openflow/discovery.py)
 - [pox.host_tracker](https://github.com/festradasolano/pox/blob/master/pox/host_tracker/__init__.py)

Sometimes you may need a forwarding module to enable communication among hosts
and switches from the network. For example, to deploy a layer 2 learning switch
application, do the following:

    $ ./pox.py forwarding.l2_learning web.jsonrest

Full example: run the POX controller to listen OpenFlow messages in port 6633.
The POX controller deploys a layer 2 learning switch application and a RESTful
interface to listen any host in port 8082; see information level logging data.
To perform this, invoke as follows:

    $ ./pox.py log.level --INFO openflow.of_01 --port=6633 forwarding.l2_learning web.jsonrest --host=0.0.0.0 --port=8082

###RESTful web services

Following the implemented RESTful web services to interact with the POX
controller:

 - **URI:** `/web/jsonrest/of/controller/info`. **Method:** GET.
 **Description:** retrieves information about the controller. This includes:
  - Listen address (IP and port)
 - **URI:** `/web/jsonrest/discovery/links`. **Method:** GET. **Description:**
 retrieves a list of all inter-switch discovered links (note that these are
 only for switches connected to the controller). Requires to launch
 [pox.openflow.discovery](https://github.com/festradasolano/pox/blob/master/pox/openflow/discovery.py).
 This includes:
  - Data layer source/destination
  - Port source/destination

|URI                                                                     |Method|Description|
|------------------------------------------------------------------------|------|-----------|
|`/web/jsonrest/of/switches`                                             |GET   |Returns a list of all switches connected to the controller|
|`/web/jsonrest/of/switch/<switchDpid>/<statType>`                       |GET   |Returns per switch stats. **statType:** aggregate, desc, flows, ports, queues, tables|
|`/web/jsonrest/of/switch/<switchDpid>/record/<recordType>/<lastRecords>`|GET   |Returns per switch last recorded stats. **recordType:** aggports. **lastRecords:** number of last records to request|
|`/web/jsonrest/host_tracker/devices`                                    |GET   |Returns a list of all hosts tracked by the controller|

Note: **switchDpid** is the switch DPID in format XX:XX:XX:XX:XX:XX:XX:XX.

The POX JSON-REST API is based on the [POX Controller](https://github.com/noxrepo/pox),
a [NOXRepo.org](http://www.noxrepo.org/) project:

##POX

POX is a networking software platform written in Python.

POX started life as an OpenFlow controller, but can now also function
as an OpenFlow switch, and can be useful for writing networking software
in general.

POX officially requires Python 2.7 (though much of it will work fine
fine with Python 2.6), and should run under Linux, Mac OS, and Windows.
(And just about anywhere else -- we've run it on Android phones,
under FreeBSD, Haiku, and elsewhere.  All you need is Python!)
You can place a pypy distribution alongside pox.py (in a directory
named "pypy"), and POX will run with pypy (this can be a significant
performance boost!).

POX currently communicates with OpenFlow 1.0 switches and includes
special support for the Open vSwitch/Nicira extensions.

###pox.py

pox.py boots up POX. It takes a list of module names on the command line,
locates the modules, calls their launch() function (if it exists), and
then transitions to the "up" state.

Modules are looked for everywhere that Python normally looks, plus the
"pox" and "ext" directories.  Thus, you can do the following:

    $ ./pox.py forwarding.l2_learning

You can pass options to the modules by specifying options after the module
name.  These are passed to the module's launch() funcion.  For example,
to set the address or port of the controller, invoke as follows:

    $ ./pox.py openflow.of_01 --address=10.1.1.1 --port=6634

pox.py also supports a few command line options of its own which should
be given first:

    --verbose      print stack traces for initialization exceptions
    --no-openflow  don't start the openflow module automatically
