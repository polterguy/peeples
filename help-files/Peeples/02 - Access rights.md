## Access rights

The access system of Phosphorus Five is extremely powerful, and allows you to fine grain access
control, to every single part of your system. This includes granting or denying read and write access
to specific files and/or folders, in addition to granting or denying access to specific modules in your
system. In order to modify its access objects, you'll need to declare your access  objects using Hyperlambda.
However, there exists some few helper methods with a graphical interface, to perform the most rudimentary
and common tasks. Below is a screenshot of how it might look like if you choose to grant access to Hyper
IDE to your _"user"_ role.

https://phosphorusfive.files.wordpress.com/2018/03/peeples-access-rights-screenshot.png

An access object such as the above, would grant all users belonging to the _"user"_ role access to Hyper IDE.

By default, only the _"root"_ user has access to any parts of your system, and all non-root users,
including the _"guest"_ account, cannot in any ways interact with your system. This type of logic is
often referred to as _"secure by default"_, and require you to explicitly grant access to your system,
to other roles, unless you want your user (or _"root"_ accounts to be specific) to be the only user
that's allowed to do anything in your system.

The access system is also _"cascading"_. If you for instance grant access to some role to write to the
folder called _"/foo/bar/"_, then the role you grant write access to this folder, will also have write
access to all folders and files beneath the _"/foo/bar/"_ folder, unless you explicitly deny access to
parts beneath that folder with an additional access object.

In the above screenshot you can see the role that has been assigned an access right as _"user"_. The
GUID after the role name, is just a random ID, which is necessary to be able to uniquely identify
a particular access object. If you want to, you can exchange this _"ID"_ with a more friendly name,
such as _"user-has-access-to-hyper-ide"_ for instance.

Beneath the _"user"_ parts, you'll find **[p5.module.allow]**. The `p5.module` parts is the access
object type, which implies _"module access"_. The `allow` parts, implies that the role is granted
access to the module `/modules/hyper-ide`, which again is simply the path to the module you want to
grant access to. If you exchange `allow` with `deny`, you'll instead of granting access, deny access,
which of course is the default access for all users in your system, except the _"root"_ role.

### Wild card access objects

If you want to grant access to all roles, you can use the asterix (wild card) role name. By combining
`deny` and `allow` with the `*` role, you can for instance grant access to some part of your system,
to every role, __except__ some particular role you don't want to have access. Below is some Hyperlambda
that would achieve this.

```hyperlambda
/*
 * Grants access to Hyper IDE for all roles.
 */
*:grants-access-to-hyper-ide-to-all-roles
  p5.module.allow:/modules/hyper-ide/

/*
 * Denies access to "guest" account.
 */
guest:denies-access-to-hyper-ide-to-guest-account
  p5.module.deny:/modules/hyper-ide/
```

The above would result in that everybody who are logged into your system, can access Hyper IDE, but
not some random visitor, without a username/password combination. The reason why the above would
be working, is because explicitly named access objects (our _"guest"_ object from above), have precedence
above wild card access objects. If you instead wanted to for instance grant write file access to all logged
in users, to some _"/foo/bar/"_ folder, except the _"guest"_ account, you could achieve such a thing with
the following.

```hyperlambda
/*
 * Grants access to write to all "/foo/bar/" files
 * and folders for all roles.
 */
*:grants-write-access-to-all-roles
  p5.io.write-file.allow:/foo/bar/

/*
 * Denies access to "guest" account.
 */
guest:denies-write-access-to-guest-account
  p5.io.write-file.deny:/foo/bar/
```

The above would allow all users that are logged in, to write to all files inside of your _"/foo/bar/"_ folder.
If you want to protect some specific folder inside of your _"/foo/bar/"_ folder, you could additionally
add up yet another `deny` object, with something resembling the following.

```hyperlambda
/*
 * Grants access to write to all "/foo/bar/" files
 * and folders for all roles.
 */
*:grants-write-access-to-all-roles
  p5.io.write-file.allow:/foo/bar/

/*
 * Denies access to all roles to the "some-protected-folder".
 */
*:denies-write-access-to-all-roles
  p5.io.write-file.deny:/foo/bar/some-protected-folder/
```

The above would allow all users that are logged in, to write to all files and folders inside of
your _"/foo/bar/"_ folder, **except** the _"/foo/bar/some-protected-folder/"_. The reason why the last
example works, is because access objects are _"cascading"_, which implies that they'll be alphabetically
sorted, before traversed sequentially, to determine access rights, on their _"value"_ parts - Which is
the `/foo/bar/` and the `/foo/bar/some-protected-folder/` from above. Since `/foo/bar/` comes before
`/foo/bar/some-protected-folder/` when sorted alphabetically, and the last access object is of type `deny`,
this will not allow the user to write to files inside of your `/foo/bar/some-protected-folder/` folder.

**Notice**, the user can still delete the entire `/foo/bar/` folder, even though he cannot write to
the `/foo/bar/some-protected-folder/` folder or its files.

### Access object types

Listing all the possible access types in your system, is impossible, since the access object
system is extendible. However, by default your system should at the very least have these 10 different
access objects types.

* __[p5.module.allow]__ - Grants access to a module
* __[p5.module.deny]__ - Denies access to a module
* __[p5.io.read-file.allow]__ - Grants access to read a specific file or folder
* __[p5.io.read-file.deny]__ - Denies access to read a specific file or folder
* __[p5.io.write-file.allow]__ - Grants access to write to a specific file or folder
* __[p5.io.write-file.deny]__ - Denies access to write to a specific file or folder
* __[p5.hyper-core.allow]__ - Allows access to some HTTP REST based URL for Hyper Core
* __[p5.hyper-core.deny]__ - Denies access to some HTTP REST based URL for Hyper Core
* __[p5.system.platform.execute-file.allow]__ - Allows execute shell file permission for some specified path
* __[p5.system.platform.execute-file.deny]__ - Denies execute shell file permission for some specified path

### Default access

By default, your system is setup such that a _"root"_ account can do everything, and in fact, explicitly
denying access to parts of the system for _"root"_ accounts, is not even possible. All other account
types have read access to all files, **except** the _"auth.hl"_, and the _"web.config"_ file, and the
entire _"/db/"_ folder. The latter is necessary in order to allow the system to read files, which it
needs to execute for some reasons. So contrary to on Linux, there doesn't exist any explicitly
_"execute file"_ Hyperlambda permission access object types. This would not be possible either,
since Hyperlambda is a functional programming language, and as long as some user can read a file,
he can simply read that file's content, transform it into Hyperlambda, and then evaluate the resulting
lambda object.

For all non-root account types, the only files the user can write to, are his personal files, inside
of the user's _"home"_ folder, in the _"/users/xxx-username-xxx/"_ folder, in addition to all files
inside of the _"/common/"_ folder, which are files common to all users.

**Notice**, for a _"guest"_ account, his _"home"_ folder actually **is** the _"/common/"_ folder.

In addition, no modules are accessible for any accounts, except the _"root"_ account.
All other access objects must be explicitly created in order to grant access to your system.

**Notice** also that even though a root account can write to the web.config file, the web server will
still be restarted if you do this, invalidating sessions and such, if you write to this file. This
is a restriction inherited from the core of .Net, and not possible to avoid.
