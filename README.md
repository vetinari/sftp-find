# NAME

sfind - search for files in an SFTP accessible directory hierarchy

# SYNOPSIS

    sfind --key ~/.ssh/id_dsa user@remote:/path/to -type f -name '\.pl$' \
       -mtime +5 -print0

# DESCRIPTION

__sfind__ lets you search for files in a remote location which is accessible
via [ssh(1)](http://man.he.net/man1/ssh) / [sftp(1)](http://man.he.net/man1/sftp). It behaves similar to [find(1)](http://man.he.net/man1/find), though some 
restrictions are given, because not all information is available via sftp's
[stat(2)](http://man.he.net/man2/stat) method.

Note: this is __much__ slower than calling _find_ on the remote host because
__all__ stat info passes the network. If you have the permission to execute
_find_ on the remote host, use it...

# OPTIONS

The options are divided into two parts, the first one configures where to 
connect and how to authenticate at the remote system. The second part is
about selecting files and print them.

## SSH Options

- \-a
- \--agent

Try authentication with ssh-agent

- \-i FILE
- \--key FILE
- \--identity-file FILE

Uses FILE.pub as public key file and FILE as private key file to authenticate
at the remote system.

- \--pub-key FILE

Uses FILE as the public key for authentication. Note that this option requires
also a `--sec-key` option and `--key` must not be present.

- \--sec-key FILE

Uses FILE as the secret key for authentication. Note that this option requires
also a `--pub-key` option and `--key` must not be present.

- \-p PORT
- \--port PORT

Use port PORT to connect to instead of the default port 22.

- \-l NAME
- \--user NAME 
- \--login NAME

The authenication will be tried as user NAME at the remote system. Note that
a user part in the remote system name will override this setting. If neither
\--user / --login is given nor the user part exists the NAME will be set to 
whatever is in the environment variable `USER`

- \--real-path

Files will be printed with full path instead of relative, uses [sftp(1)](http://man.he.net/man1/sftp)s
`realpath` call.

- \--no-host-key-check

Do not attempt to verify the remote host from _~/.ssh/known\_hosts_.

- \-d
- \--debug

Be verbose about ssh connection

## Filter Options

- \-not

Negates the test of the next option, i.e. to find all regular files ending 
with `.pl` and not owned by the user with uid 1000 use something like

    sfind user@remote:dir -type f -not -uid 1000 -name '\.pl$'

- \-type TYPE

Selects files of the given type. Valid types are

    - f 

    regular __f__iles

    - d 

    __d__irectories

    - p 

    FIFOs (named __p__ipes)

    - b

    __b__lock devices

    - c

    __c__haracter devices

    - l

    symbolic __l__inks

    - s

    __s__ockets

- \-name REGEX

Only files matching the perl regex REGEX

- \-iname REGEX

Same as `-name`, case insensitive.

- \-regex REGEX

Like `-name`, but matches on the full `path/file` instead of just the
file name.
NOTE: the path is the one given on the command line UNLESS the `--real-path`
option is used

- \-uid NUMBER

Files' user id is equal to NUMBER

- \-gid NUMBER

Files' group id is equal to NUMBER

- \-empty 

Shortcut for `-size 0`, note that it does not match empty directories

- \-mtime TIME

If time starts with a `+`, files modification time older than TIME days.
A leading `-` selects files newer than TIME days. Without any prefix: files
which are TIME days old.

NOTE: Currently this option behaves like `-daystart` was given to [find(1)](http://man.he.net/man1/find).

- \-atime TIME

Like `-mtime`, only for atime (access time).

- \-mmin TIME

Like `-mtime`, but in minutes

- \-amin TIME

Like `-atime`, but in minutes

- \-size SIZE

If SIZE starts with a `+`, files with a size greater than SIZE are selected.
A leading `-` selects files smaller than SIZE. Without any prefix: files
which are exactly SIZE bytes.

- \-mode MODE

If MODE starts with a `+`, files with a permissions of at least MODE are
selected. Without any prefix: files which have permissions MODE bytes.

NOTE: MODE must be an octal number



## Tree Traversal Options

- \-maxdepth DEPTH

Only descend DEPTH levels deep.

- \-depth

Does a depth first search, i.e. directories are descended as soon as one is 
found. Default is to process contents first, then descend.

## Output Options

- \-print

Prints the name of all matching files separated by a newline `\n`, this 
is the default if no other output option is given.

- \-print0

Prints the names of all matching files spearated by a `\0`, useful for files
which may contain spaces or newlines. Use with [xargs(1)](http://man.he.net/man1/xargs)'s `-0` option.

- \-printf FORMAT

Prints the wanted information in the given FORMAT

- \-ls

A [ls(1)](http://man.he.net/man1/ls) like output, due to missing stat info not compatible to [find(1)](http://man.he.net/man1/find)'s
`-ls` option. This is a shortcut for `-printf '%M %u %g %s %TI %p%l\n'`

## Unsupported options from find

- \-ctime
- \-cmin
- \-user
- \-group

# SEE ALSO

[ssh(1)](http://man.he.net/man1/ssh), [sftp(1)](http://man.he.net/man1/sftp), [find(1)](http://man.he.net/man1/find)

# DOWNLOAD

[http://ankh-morp.org/code/sfind/sfind](http://ankh-morp.org/code/sfind/sfind) or
[SVN](http://svn.ankh-morp.org:8080/tools/sfind/)

# AUTHOR

Hanno Hecker <vetinari@ankh-morp.org>

# COPYRIGHT AND LICENSE

Copyright (C) 2012 by Hanno Hecker; all rights reserved.

This script is free software; you can redistribute it and/or modify it
under the same terms as Perl itself, either Perl version 5.8.0 or, at
your option, any later version of Perl 5 you may have available.
