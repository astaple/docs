.. _mongodump:

.. default-domain:: mongodb

.. binary:: mongodump

===========================
:program:`mongodump` Manual
===========================

Synopsis
--------

:program:`mongodump` is a utility for creating a binary export of the
contents of a database. Consider using this utility as part an
effective :doc:`backup strategy </administration/backups>`. Use in
conjunction with :program:`mongorestore` to provide restore
functionality.

.. seealso:: ":program:`mongorestore`" and ":doc:`/administration/backups`".

Options
-------

.. program:: mongodump

.. option:: --help

   Returns a basic help and usage text.

.. option:: --verbose, -v

   Increases the amount of internal reporting returned on the command
   line. Increase the verbosity with the ``-v`` form by including the
   option multiple times, (e.g. ``-vvvvv``.)

.. option:: --version

   Returns the version of the :program:`mongodump` utility.

.. option:: --host <hostname><:port>

   Specifies a resolvable hostname for the :program:`mongod` that you wish to
   use to create the database dump. By default :program:`mongodump` will
   attempt to connect to a MongoDB process ruining on the localhost
   port number ``27017``.

   Optionally, specify a port number to connect a MongboDB instance
   running on a port other than ``27017``.

   To connect to a replica set, use the :option:`--host <mongodump
   --host>` argument with a setname, followed by a slash and a comma
   separated list of host and port names. The :program:`mongodump`
   utility will, given the seed of at least one connected set member,
   connect to primary member of that set. this option would resemble:

   .. code-block:: javascript

      mongodump --host repl0/mongo0.example.net,mongo0.example.net,27018,mongo1.example.net,mongo2.example.net

   You can always connect directly to a single MongoDB instance by
   specifying the host and port number directly.

.. option:: --port <port>

   Specifies the port number, if the MongoDB instance is not running
   on the standard port. (i.e. ``27017``) You may also specify a port
   number using the :option:`--host <mongodump --host>` command.

.. option:: --ipv6

   Enables :term:`IPv6` support to allow :program:`mongodump` to
   connect to the MongoDB instance using IPv6
   connectivity. All MongoDB programs and processes, including
   :program:`mongodump`, disable IPv6 support by default.

.. option:: --username <username>, -u <username>

   Specifies a username to authenticate to the MongoDB instance, if
   your database requires authentication. Use in conjunction with the
   :option:`--password <mongodump --password>` option to supply a
   password.

.. option:: --password <password>

   Specifies a password to authenticate to the MongoDB instance. Use
   in conjunction with the :option:`--username <mongodump --username>`
   option to supply a username.

.. option:: --dbpath <path>

   Specifies the directory of the MongoDB data files. If used, the
   :option:`--dbpath` option enables :program:`mongodump` to attach
   directly to local data files and copy the data without the
   :program:`mongod`. To run with :option:`--dbpath`,
   :program:`mongodump` needs to restrict access to the data
   directory: as a result, no :program:`mongod` can be access the same
   path while the process runs.

.. option:: --directoryperdb

   Use the :option:`--directoryperdb` in conjunction with the
   corresponding option to :program:`mongod`. This option allows
   :program:`mongodump` to read data files organized with each
   database located in a distinct directory. This option is only
   relevant when specifying the :option:`--dbpath` option.

.. option:: --journal

   Allows :program:`mongodump` operations to use the durability
   :term:`journal` to ensure that the export is in a
   consistent state. This option is only relevant when specifying the
   :option:`--dbpath` option.

.. option:: --db <db>, -d <db>

   Use the :option:`--db` option to specify a database for
   :program:`mongodump` to backup. If you do not specify a DB,
   :program:`mongodump` copies all databases in this instance into the
   dump files. Use this option to backup or copy a smaller subset of
   your data.

.. option:: --collection <collection>, -c <c>

   Use the :option:`--collection` option to specify a collection for
   :program:`mongodump` to backup. If you do not specify a collection,
   this options copies all collections in the specified database or
   instance to the dump files. Use this option to backup or copy a
   smaller subset of your data.

.. option:: --out <path>, -o <path>

   Specifies a path where :program:`mongodump` and store the output the
   database dump. If you want to output the the database dump to
   standard output, specify a '``-``" rather than a path.

.. option:: --query <json>, -q <json>

   Provides a :term:`JSON` query to limit (optionally) the documents
   included in the output of :program:`mongodump`.

.. option:: --oplog

   Use this option to ensure that the database backup you create is a
   consistent point-in-time snapshot of the state of a
   database. Without this option, changes made to the database during
   the update process may cause the backup to reflect an inconsistent
   status.

   :option:`--oplog` has no effect when running :program:`mongodump`
   against a :program:`mongos` instance to dump the entire contents of
   a shard cluster. However, you can use :option:`--oplog` to dump
   individual shards.

   .. note::

      :option:`--oplog` only works against nodes that maintain a
      :term:`oplog`. This includes all members of a replica set, as
      well as :term:`master` nodes in master/slave replication
      deployments.

.. option:: --repair

   Use this option to run an repair option in addition to dumping the
   database. The repair option attempts to repair a database that may
   be an inconsistent state as a result of an improper shutdown or
   :program:`mongod` crash.

.. option:: --forceTableScan

   Forces :program:`mongodump` to scan the data store directly:
   typically, :program:`mongodump` saves entries as they appear in the
   index of the ``_id`` field. Use :option:`--forceTableScan` to skip
   the index and scan the data directly. Typically there are two cases
   where this behavior is preferable to the default:

   1. If you have key sizes over 800 bytes that would not be present
      in the "``_id``" index.
   2. Your database uses a custom "``_id``" field.

Usage
-----

See the ":ref:`backup guide section on database dumps <database-dumps>`"
for a larger overview of :program:`mongodump` usage. Also see the
":doc:`mongorestore`" document for an overview of the
:program:`mongorestore`, which provides the related inverse
functionality.

The following command, creates a dump file that contains only the
collection named "``collection``" in the database named "``test``". In
this case the database is running on the local interface on port
``27017``:

.. code-block:: sh

   mongodump --collection collection --database test

In the next example, :program:`mongodump` creates a backup of the
database instance stored in the ``/srv/mongodb`` directory on the
local machine. This requires that no :program:`mongod` instance is
using the ``/srv/mongodb`` directory.

.. code-block:: sh

   mongodump --dbpath /srv/mongodb

In the final example, :program:`mongodump` creates a database dump
located at ``/opt/backup/mongodumpm-2011-10-24``, from a database
running on port "``37017``" on the host "``mongodb1.example.net`` and
authenticating using the username "``user``" and the password
"``pass``", as follows:

.. code-block:: sh

   mongodump --host mongodb1.example.net --port 37017 --username user --password pass /opt/backup/mongodumpm-2011-10-24
