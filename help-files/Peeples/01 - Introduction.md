## Introduction to Peeples

Peeples allows you to grant or deny access in your system to different users, according to which role your
users belongs to. In addition, it also allows you to create and maintain your users. It contains three
different parts. One part that allows you to create new users, another part that allows you to edit
existing users, and a third part that allows you to grant or deny access to specific parts of your system,
to roles your users belongs to. Below is a screenshot emphasizing these three different parts.

https://phosphorusfive.files.wordpress.com/2018/03/peeples-screenshot.png

To create a new user, simply fill out a username and an initial password, and click _"Add"_. This will create
a new user in your system, belonging to the role _"user"_. If you want to create a user belonging to a different
role, make sure you change the name of the role the user belongs to before you click _"Add"_. When that user
later logs into your system, he or she can change the password to whatever they want.

To edit a user, simply click the user in the datagrid in the top right corner of your Peeples module, at which
point a modal window will show, and you can edit your user's role and/or password.

### About users

Each user has their own folder in the system for their own files. This folder can be found in _"/users"/_ folder,
on the root of your web server's main folder. This is typically where users will store their personal
files, that they own themselves. When you create a new user with Peeples, a folder with the same name as the
username you chose will be automatically created, with a structure for both public files, private files, and
temporary files.

Inside this folder, your user has (almost) 100% control over what can be legally done, such as creating additional
folders, or new files, etc. You can think of this folder as the equivalent of the _"My Documents"_ folder
in a Windows system, or the _"Home"_ folder in a Linux system.

**Notice**, you cannot change your user's username after having initially created your user, so take care
that you use some intuitive username as you create your users. Also, due to that each user gets his own
folder, with the folder's name matching the username, this creates some restrictions for which characters
you are legally allowed to use in your usernames. Also please realise that Windows and Linux have different
semantics on files and folders. On Linux for instance, the folder _"Foo"_ is actually another physical folder
than the folder _"foo"_ - This is **not** true for Windows. So please try to avoid creating users where the
only difference is captializations on characters, since this creates all sorts of weird troubles as you
move your system to and from Windows/Linux/Mac. In general, I'd encourage you to exclusively use lower case
characters as you create your users. And in fact, Peeples will deny you to use CAPITAL letters as you create
your users for the above reasons.

### About roles

Phosphorus Five's role system is fairly _"naive"_. Among other things, there are no role objects in the system,
and the sum of roles, is actually the list of distinct roles your users belongs to. This has some advantages,
and some disadvantages. Among other things, creating a new role, simply imply making sure one of your users
belongs to the role you want to _"create"_. If you want to create the new role of _"foo-bar"_ for instance,
simply edit one of your users, and make sure he or she belongs to the _"foo-bar"_ role. To delete that role,
simply edit the user again, and assign it to a different role.

**Notice** the _"root"_ role is special, and always has 100% access to the entire system. You should in general
only have one user of type _"root"_ in your system. The _"guest"_ role is also special, since it implies a
visitor, that is not logged into your system. Be careful when granting access to the _"guest"_ role, since it
allows any random visitor to your page, to do whatever you assign the _"guest"_ role to be allowed to do.
Hence, you cannot explicitly assign or create a user that belongs to the _"guest"_ role, since this would
invalidate your system.

**Notice**, although this is extremely unlikely for your system, at least in theory, the name of your _"guest"_
account role might have been changed to something else. If it has, the name of your guest account role can
be found in your web.config file, under the _"phosphorus"_ section. It will be an attribute with the name
of _"defaultContextRole"_. It is also illegal to create a user with the username matching the
_"defaultContextUsername"_ from your web.config, which by default is _"guest"_.

Basically, any visitor, which is not logged in on your system, will be impersonating the username
_"guest"_ with the role being _"guest"_.

### About access

Access to your system is determined from which role a user belongs to, and you can grant or deny access to
specific parts of your system, according to roles. This implies that different users have different things
that they are allowed to do. For instance, the _"root"_ role is always allowed to do everything, and some tasks
in your system is only legally allowed to be performed by a root user. Typically, this includes for instance
creating new users among other things.

Although granting or denying access requires some basic Hyperlambda knowledge, there is a GUI helper that
allows you to grant access declaratively, without knowing Hyperlambda. If you click the _"+"_ button in the
bottom right corner of Peeples, you can graphically grant access to some specific module in your system, to
whatever role you want to be granted access to that module.

If you want to grant access to for instance Hyper IDE, to the _"user"_ role, you can do this with something
resembling the following.

https://phosphorusfive.files.wordpress.com/2018/03/peeples-grant-access-screenshot.png
