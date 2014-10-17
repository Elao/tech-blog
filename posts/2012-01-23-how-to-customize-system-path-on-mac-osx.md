
Hi guys !

I just figured out how to handle system path customization in an "OSX way".
From here to now I used to add my path customization into system profile `/etc/profile` or my local profile `~/.bash_profile` and my `.bashrc` file (wich is a common meaning on a UNIX system).

**For example:**

```
export PATH=$PATH:/usr/local/zend/bin
```

But, I came across some postings whereby file `/etc/paths` and directory `/etc/paths.d` are described as the places where search path action takes place now on OSX.

Fact is in Leopard, Apple has introduced a new mechanism for managing your system path, and we no longer have to modify those files.
Instead, we just have to put a simple text file containing a path entry (or entries) into `/etc/paths.d/`.

```
-rw-rw-r-- 1 root wheel 19B Sep 17 2009 git
-rw-r--r-- 1 root wheel 60B Jan 23 14:39 sublime
```

**For example:**

We can create a file named `git` wich contain path to git binaries:

*Content of /etc/paths.d/git*

```
/usr/local/git/bin
```