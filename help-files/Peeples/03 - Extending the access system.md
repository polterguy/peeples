## Extending the access system

The access system is as previously said extendible. This implies that you can create your own access object
extensions. And in fact, most of the existing access object types that exists, are simply such extensions.
For instance, when a module is loaded by the main desktop, it will check to see if the user has access to
that module using the Adtive Event **[p5.auth.has-access]**.

This Active Event takes a default boolean value, a **[filter]** argument, and a **[path]** argument. The
path typically is a URL or a file path, and the filter is the access object type, such as `p5.module`, which
then will be traversed for `p5.module.allow` and `p5.module.deny` for each access object. Implying adding
the verbs `allow` and `deny` to the filter argument. Below is a piece of Hyperlambda snippet that checks to
see if your user have access to Hyper IDE to illustrate the idea. This event will always check access
for the currently logged in user's role.

```hyperlambda-snippet
/*
 * Checking if you have access to Hyper IDE.
 */
p5.auth.has-access:bool:false
  filter:p5.module
  path:/module/hyper-ide/

/*
 * Showing an information bubble window, according to
 * results of above invocation.
 */
if:x:/@p5.auth.has-access?value

  /*
   * You have access.
   */
  micro.windows.info:You have access to Hyper IDE
    class:micro-windows-info success

else

  /*
   * You don't have access.
   */
  micro.windows.info:You don't have access to Hyper IDE
    class:micro-windows-info warning
```

To understand the above, it helps to understand what happens beneath the hoods of the access object system.
In our above invocation, what actually goes on, is that the Active Event first checks to see if the
currently logged in user is a _"root"_ account, and if it is, it will always return boolean _"true"_
immediately.

Then it will retrieve all access objects starting with the string _"p5.module"_, which can also be found
at the starting index of your **[path]** argument. This implies that it would return for instance both
an access object with a value of _"/modules/"_ and an access object with a value of _"/modules/hyper-ide/"_,
as long as they are of either `p5.module.allow` or `p5.module.deny` types. Then it will remove all
access objects from our previous result, that are not relevant for the currently logged in user. At which
point it will be left with only wild card access objects, and access objects which are explicitly mentioning
the user's role.

Finally, it will sort these alphabetically according to their values, making sure any wild card access
objects comes before any access objects for specific roles. The last point, makes sure access objects
explicitly mentioning a role has precedence above wild card access objects.

When it has done all of the above steps, all that is really left to do, is to check whether or not the
last access object in that list is of either type `deny` or `allow`, which determines whether or not
the user has access to the object or not. And if there are no access objects left, it will return the
default value, provided by its caller, which for our above invocation would imply _"false"_. If there are
any access objects left in our list after the above logic has been evaluated, it will return _"true"_ if
the last access object in that list is `allow` and _"false"_ if is is `deny`.

This implies that you can for instance give access to **all** modules to some role with something resembling
the following.

```hyperlambda
/*
 * Grants all "developer" users access to all modules.
 */
developer:grant-all-developers-access-to-all-modules
  p5.module.allow:/modules/
```

Now the `p5.module` parts above can be exchanged with really anything you wish, allowing for you to
create your own access object types, which is used in for instance Hyper Core, to determine access
to HTTP REST based endpoints, allowing you to use the access object system, to declare whether or
not a user has insert, select, delete and update SQL access to some database table.

Please see the code for Hyper Core as an example of how to implement a custom access type in your app.
