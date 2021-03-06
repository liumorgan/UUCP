# UUCP

WHAT IT IS

This is the complete source code for a Unix UUCP package.  It provides
everything you need to make a UUCP connection.  It includes versions
of uucico, uusched, uuxqt, uux, uucp, uustat, uulog, uuname, uuto,
uupick, and cu, as well as uuchk (a program to check configuration
files), uuconv (a program to convert from one type of configuration
file to another) and tstuu (a test harness for the package).

This is the standard UUCP package of the Free Software Foundation.

The package currently supports the 'f', 'g' (in all window and packet
sizes), 'G', 't' and 'e' protocols, as well a Zmodem protocol, the FX
UUCICO 'y' protocol, and two new bidirectional protocols.  If you have
a Berkeley sockets library, it can make TCP connections.  If you have
TLI libraries, it can make TLI connections.  It supports a new
configuration file mechanism which I like (but other people dislike).

The package has a few advantages over regular UUCP:

    You get the source code.

    It uses significantly less CPU time than many UUCP packages.

    You can specify a chat script to run when a system calls in,
    allowing adjustment of modem parameters on a per system basis.

    You can specify failure strings for chat scripts, allowing the
    chat script to fail immediately if the modem returns ``BUSY''.

    If you are talking to another instance of the package, you can use
    the new bidirectional protocol for rapid data transfer in both
    directions at once.  You can also restrict file transfers by size
    based on the time of day and who placed the call.

On the other hand:

    It only runs on Unix.  The code is carefully divided into system
    dependent and system independent portions, so it should be
    possible to port it to other systems.  It would not be trivial.

    You don't get uuclean, uusend, uuq, uusnap, uumonitor, uutry,
    uupoll, etc.  If you have current copies of these programs, you
    may be able to use them.  Shell scripts versions of uuclean and
    uutry are provided, with most, if not all, of the functionality of
    the usual programs.  I believe the supplied uustat program allows
    you to do everything that uuq, uusnap and uumonitor do.  uupoll
    could be written as a shell script.

    The package does not read modemcap or acucap files, although you
    can use V2 configuration files with a BNU Dialers file or a dialer
    file written in my new configuration file format.

    The package cannot use SCO dialer programs directly, although it
    can with a simple shell script interface.

If you start using this package, I suggest that you join the mailing
list (see above) to keep up to date on patches and new versions.  I am
also open to suggestions for improvements and modifications.

DOCUMENTATION

The documentation is in the file uucp.texi, which is a Texinfo file.
Texinfo is a format used by the Free Software Foundation.  You can
print the documentation using TeX in combination with the file
texinfo.tex.

See the TODO file for things which should be done.  Please feel free
to do them, although you may want to check with me first.  Send me
suggestions for new things to do.

The compilation instructions are in uucp.texi.  Here is a summary.

    Type ``sh configure''.  You can pass a number of arguments in the
    environment (using bash or sh, enter something like ``CC=gcc
    configure''; using csh, enter something like ``setenv CC gcc; sh
    configure''):
        CC: C compiler to use; default is gcc if it exists, else cc
        CFLAGS: Flags to pass to $CC when compiling; default -g
        LDFLAGS: Flags to pass to $CC when only linking; default none
        LIBS: Library arguments to pass to $CC; default none
        INSTALL: Install program; default install -c or cp
    The configure script will compile a number of test programs to see
    what is available on your system, so if your system is at all
    unusual you will need to pass in $CC and $LIBS correctly.  You can
    also pass other arguments on the command line.  Use configure
    --help for a complete list.  Of particular interest:
        --prefix=DIRNAME
            The directory under which all files are installed.
            Default /usr/local.
        --with-newconfigdir=DIRNAME
            The directory in which to find new style configuration
            files.  Default PREFIX/conf/uucp.
        --with-oldconfigdir=DIRNAME
            The directory in which to find old style configuration
            files.  Default /usr/lib/uucp

    The configure script will create config.h from config.h.in and
    Makefile from Makefile.in.  It will also create config.status,
    which is a shell script which actually creates the files.  Please
    report any configuration problems, so that they can be fixed in
    later versions.

    Igor V. Semenyuk provided this (lightly edited) note about ISC
    Unix 3.0.  The configure script will default to passing -posix to
    gcc.  However, using -posix changes the environment to POSIX, and
    on ISC 3.0, at least, the default for POSIX_NO_TRUNC is 1.  This
    means nothing for uucp, but can lead to a problem when uuxqt
    executes rmail.  IDA sendmail has dbm configuration files named
    mailertable.{dir,pag}.  Notice these names are 15 characters long.
    When uuxqt compiled with -posix executes rmail, which in turn
    executes sendmail, the later is run under POSIX environment too!
    This leads to sendmail bombing out with 'error opening 'M'
    database: name too long' (mailertable.dir).  It's rather obscure
    behaviour, and it took me a day to find out the cause.  I don't
    use -posix, instead I run gcc with -D_POSIX_SOURCE, and add
    -lcposix to LIBS.

    On some versions of BSDI there is a bug in the shell which causes
    the default value for CFLAGS to be set incorrectly.  If ``echo
    ${CFLAGS--g}'' echoes ``g'' rather than ``-g'', then you must set
    CFLAGS in the environment before running configure.  There is a
    patch available from BSDI for this bug.  (Reported by David
    Vrona).

    On AIX 3.2.5, and possibly other versions, cc -E does not work,
    reporting ``Option NOROCONST is not valid.''  Test this before
    running configure by doing something like
	touch /tmp/foo.c
	cc -E /tmp/foo.c
    This may give a warning about the file being empty, but it should
    not give the ``Option NOROCONST'' warning.  The workaround is to
    remove the ",noroconst" entry from the "options" clause in the
    "cc" stanza in /etc/xlc.cfg.  (Reported by Chris Lewis).

    Examine config.h and Makefile to make sure they're right.

    Edit policy.h for your local system.    

    Type ``make''.

    Use ``uuchk'' to check configuration files.  You can use
    ``uuconv'' to convert between configuration file formats.

    Type ``make install'' to install.  Note that by default the
    programs are compiled with debugging information, and they are not
    stripped when they are installed.  Read the man page for strip for
    more information.

    On older System V based systems which do not have the setreuid
    system call, problems may arise if ordinary users can start an
    execution of uuxqt, perhaps indirectly via uucp or uux.  UUCP jobs
    may wind up executing with a real user ID of the user who invoked
    uuxqt, which can cause problems if the UUCP job checks the real
    user ID for security purposes.  On such systems, it is safest to
    put ``run-uuxqt never'' in the `config' file, so that uucico never
    starts uuxqt, and invoke uuxqt directly from cron.
