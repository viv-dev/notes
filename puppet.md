# puppet

Module is a folder containing a set of manifest files.

A class is a named block for grouping resources inside a manifest file.

A resource is a puppet-defined declarative command that does 'stuff' on the machine to be configured (e.g. ensure packages a present, execute a command, change file permissions). Resource types can be found [here](https://puppet.com/docs/puppet/7/lang_resources.html#lang_resources).

A role is simply a module that includes multiple other modules (profiles).

The variables applied to a particular resource description defined by a role can be overwritted by heira.
