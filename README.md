git-deploy
==========

git-deploy is a script that automatically creates a new git repository
on a remote SSH server and then points the local git repository to it.


Installation
------------

You'll need to copy it so that it's in your path (I put it in
`/usr/local/bin` on my Mac)


Usage
-----

You use it like this (assume you are working in a directory called `mygit`):

    $ git init .
    $ git add .
    $ git commit -a
    $ git deploy you@yourserver.com:~

The result is that your git repository will be copied to your SSH server
`you@yourserver.com:~/mygit.git` in this example, and will be set as the
origin so that you can just do `git push` and `git pull` with no
parameters and it will be applied against this new repository:

    $ git pull
    $ git push

If you put a third parameter:

    $ git deploy you@yourserver.com:~ backup

Then it will do the same, but not call the remote repository `origin`
but will call it `backup` (in this example).

This means you can easily add additional remotes, which is useful for
backups. You can synchronize your backup with these commands:

    $ git push backup
    $ git pull backup


Automatic remote push
---------------------

If you want to automatically do a `git push` on every commit, sort of
what SVN does, you can use the `-a` option.

For example:

    $ git deploy -a you@yourserver.com:~
    $ echo "blah" >> test.txt
    $ git commit -a -m "This should auto push after commit"
    Counting objects: 5, done.
    Writing objects: 100% (3/3), 298 bytes, done.
    Total 3 (delta 0), reused 0 (delta 0)
    To you@yourserver.com:~/test.git
       283643c..15536a4  master -> master
    Created commit 15536a4: This should auto push after commit
    1 files changed, 1 insertions(+), 0 deletions(-)

So the very act of committing a file will run a `git push` after the
commit. This is useful to keep the remote repository in sync (for team
development or backup purposes).

This is implemented by putting a `git push` command in the
`.git/hooks/post-commit` file.

The default is that this hook *isn't added*. So you need to add the `-a`
option to get the automatic synchronization.

