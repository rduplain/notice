notice - dead simple user logging utility for the command-line user
-------------------------------------------------------------------

license
-------

(C) Copyright 2009 Ron DuPlain <ron.duplain@gmail.com>

This software package is freely available under the GNU Public License.
See COPYING for license details of the GNU Public License version 3.

overview
--------

`notice` provides the command-line user of Unix-like systems with a simple
logging utility of thoughts and command output.  It is meant to be dead simple:
just a short command-line script with a bare logging format.  `notice` is
virtually featureless so that it can be extended easily using command-line
utilities.

Just type at the command line:

    notice log this somewhere...

and get, in your notice log file (safely tucked away at $NOTICE_LOG):

    Tue Aug 18 13:49:37 EDT 2009 -- log this somewhere...

The default location for the notice log is `~/.notice`.

installation
------------

Put the enclosed `notice` script somewhere in your `$PATH`.

`notice` is a stand-alone script, with dependencies on:

 * GNU bash, though `sh` might work, too.
   (Just change `#!/bin/bash` to `#!/bin/sh`.)
 * `cat` command (likely /bin/cat)
 * `date` command (likely /bin/date)

These dependencies are given on any Unix-like system, as they are part of the
Unix standard.

configuration
-------------

`notice` evaluates the following environment variables:

 * `$NOTICE_DATE` -- text to use for timestamp of `notice`. (default: get
    output of `date`) note that this is static text when provided, so repeated
    calls to `notice` will use the same text given in `$NOTICE_DATE`.
 * `$NOTICE_DATE_FORMAT` -- format to use for `date` output. see `man date`
    (default: default of `date`). not used if `$NOTICE_DATE` is set.
 * `$NOTICE_SEP` -- separator between date and content (default: ` -- `).
 * `$NOTICE_LOG` -- path of `notice` log file (default: `$HOME/.notice`).
 * `$HOME` -- if `$NOTICE_LOG` is not given, `notice` will evaluate the
    location given by `$HOME/.notice`.

See the section titled "example" for a type script of examples on how to use
these configuration variables.

format
------

The basic format of the `notice` log is, when args are given:

    ${NOTICE_DATE}${NOTICE_SEP}${args}

The basic format of the `notice` log is, when reading from stdin:

    ${NOTICE_DATE}${NOTICE_SEP}${stdin}${NOTICE_SEP}

With `notice` defaults, a call of `notice Hello, world!` will look like:

    Sat Aug 15 19:15:39 EDT 2009 -- Hello, world!

where the date would match the output of `date` on the local system.
(See defaults in section titled "configuration.")

example
-------

Here is an example of using `notice` in a `bash` session (cwd: $HOME):

    $ notice Hello, world!
    $ notice follow shell semantics or it won't work
    > '
    $ notice follow shell semantics or it won\'t work
    $ notice this example is in bash
    $ date && cat .notice
    Sat Aug 15 20:04:16 EDT 2009
    Sat Aug 15 20:03:36 EDT 2009 -- Hello, world!
    Sat Aug 15 20:03:58 EDT 2009 -- follow shell semantics or it wont work

    Sat Aug 15 20:04:04 EDT 2009 -- follow shell semantics or it won't work
    Sat Aug 15 20:04:10 EDT 2009 -- this example is in bash
    $ notice
    This is read from stdin.
    We no longer have to worry about the shell, but we do have to worry about
    editing stdin (we can't, really).
    (Hit ctrl-D to provide EOF and close stdin.)
    $ cat .notice
    Sat Aug 15 20:03:36 EDT 2009 -- Hello, world!
    Sat Aug 15 20:03:58 EDT 2009 -- follow shell semantics or it wont work
  
    Sat Aug 15 20:04:04 EDT 2009 -- follow shell semantics or it won't work
    Sat Aug 15 20:04:10 EDT 2009 -- this example is in bash
    Sat Aug 15 20:04:32 EDT 2009 --
    This is read from stdin.
    We no longer have to worry about the shell, but we do have to worry about
    editing stdin (we can't, really).
    (Hit ctrl-D to provide EOF and close stdin.)
     --
    $ 
    $ export NOTICE_DATE= NOTICE_SEP=
    $ notice lines no longer have a date or separator
    $ tail -1 .notice
    lines no longer have a date or separator
    $ export NOTICE_DATE=log NOTICE_SEP=': '
    $ notice lines now start with 'log: '
    $ tail -1 .notice 
    log: lines now start with log: 
    $ 
    $ unset NOTICE_DATE NOTICE_SEP 
    $ export NOTICE_LOG=log
    $ notice notice now inserts content into file at 'log'
    $ cat log 
    Sat Aug 15 20:09:26 EDT 2009 -- notice now inserts content into file at log
    $ notice go forth and log abundantly
    $ cat log 
    Sat Aug 15 20:09:26 EDT 2009 -- notice now inserts content into file at log
    Sat Aug 15 20:09:48 EDT 2009 -- go forth and log abundantly
    $ 
    $ notice pipe into notice to log command output
    $ date | notice
    $ tail -4 log
    Sat Aug 15 20:14:16 EDT 2009 -- pipe into notice to log command output
    Sat Aug 15 20:14:22 EDT 2009 -- 
    Sat Aug 15 20:14:22 EDT 2009
     -- 
    $ 

how simple?
-----------

The first cut of `notice` was a single line of bash:

`echo ${NOW:-`date`}${DELIMITER:-" -- "}${@} >> ${NOTICE_LOG:-"$HOME/.notice"}`
