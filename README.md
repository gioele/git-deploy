git-deploy
==========

git-deploy creates a mirror of the local git repository on a remote SSH
server and then points the local git repository to it.


Installation
------------

You'll need to copy it so that it's in your path (I put it in
`/usr/local/bin` on my Mac).


Usage
-----

You use it like this:

    $ mkdir my_proj; cd my_proj
    $ git init .
    $ git add .
    $ git commit -a
    $ git deploy user@example.com/home/user/

The result is that your git repository will be copied to your SSH
server, `user@example.com/home/user/my_proj.git` in this example, and
will be set as the origin so that you can just do `git push` and
`git pull` with no parameters and it will be applied against this new
repository:

    $ git push 
    $ git pull

If you put a third parameter:

    $ git deploy user@example.com/home/user/ backup

Then it will do the same, but will call the remote repository `backup`
(in this example).

This means you can easily add additional remotes, which is useful for
backups. You can synchronize your backup with these commands:

    $ git pull backup
    $ git push backup


Automatic remote push
---------------------

If you want to automatically do a `git push` on every commit, sort of
what Subversion does, you can use the `-a` option.

For example:

    $ git deploy -a user@example.com/home/user
    $ echo "blah" >> test.txt
    $ git commit -a -m "This should auto push after commit"
    Counting objects: 5, done.
    Writing objects: 100% (3/3), 298 bytes, done.
    Total 3 (delta 0), reused 0 (delta 0)
    To git+ssh://user@example.com/home/user/my_proj.git
       283643c..15536a4  master -> master
    Created commit 15536a4: This should auto push after commit
    1 files changed, 1 insertions(+), 0 deletions(-)

The very act of committing a file will run a `git push` after the
commit. This is useful to keep the remote repository in sync (for team
development or backup purposes).

This is implemented by putting a `git push` command in the
`.git/hooks/post-commit` file.

The default is that this hook *isn't added*. So you need to add the `-a`
option to get the automatic synchronization.

