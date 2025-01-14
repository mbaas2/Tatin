[parm]:title             = 'Tatin Intro'
[parm]:leanpubExtensions = 1
[parm]:collapsibleTOC    = 1
[parm]:toc               = 2 3 4
[parm]:numberHeaders     = 2 3 4 5 6



# Publishing Packages

## Groups, names and version numbers

A package ID consists of at least three parts:

* A group name
* A package name
* A version number

Optionally such an ID may also comprehend a build number which is separated from the version number by a plus (`+`).

### The Group

A group can be anything really:

* If you act on behalf of a company it might be the name of that company, like "Dyalog"
* If you are a freelancer it might be your name, or just your first name, like "davin"
* It might be a term used to indicate something, anything, like "aplteam"

Once you have chosen a group name, and published a package with that group name to the [principal Tatin Registry](https://tatin.dev "Link to tatin.dev"), you are advised to create a "home page" for that group. Use that for anything useful like background, motivation, contact details, whatever.

When a group is listed in a browser , the name of a group is converted into a link to that home page.

Of course group-related home pages do not always make sense, therefore by default they are not active; see `GroupHomePages` in the INI file.

### The name

The name should give the user an idea of what a package might do. Group+name must be unique, but you might well use a name that is already used under a different group name, though this is not recommended.

### The version number

The version number must consist of three parts separated by dots:

* The major version number: the "1" in **1**.2.3
* The minor version number: the "2" in 1.**2**.3
* The patch number: the "3" in 1.2.**3**

Optionally additional information might be added. Examples are:

* 1.2.3-beta-1
* 1.2.3-fix-for-the-foo-problem
* 1.2.3.issue-234

There is one restriction: a `+` cannot be part of the name of a beta version. The reason for that is that finally a build number might be added to a version number, and a build number is separated from the rest of the version number by a plus (`+`) character. Examples:

* 1.2.3+453
* 1.2.3-beta-1+911

Build numbers are mostly now shown by Tatin.


## Precondition: API key

Whether you want to publish to the principal Tatin server at <https://tatin.dev> or your own Tatin Server or a Tatin Server that someone in your company runs, first you need an API key. "API key" is just a fancy expression for a password that is used by an application.

API keys are required for publishing --- and possibly deleting --- packages, but not for consuming them.

They are saved in the user settings --- for details see the document "Tatin User Settings".


### The Server


#### The principal Tatin Server

If you want to publish on `https://tatin.dev` you need to ask [tatin.dev@gmail.com](mailto:tatin.dev@gmail.com) for an API key.

You must provide these pieces of information:

* Your desired group name (case insensitive)
* Your real name
* If it's connected to a company, the company's name


#### Credentials for the Tatin Test Server

The Tatin Test Server publishes an API key on its website; it's "Tatin-Test-API-Key". It allows you to publish anything you want, including any group name except a few reserved group names: "aplteam" and "dyalog".

Notes:

* Be aware that the Tatin Test Server is reset every now and then, so whatever you publish will disappear sooner or later
* You cannot publish anything under the group names "aplteam" and "dyalog"


#### Where are API keys saved?

##### Client 

API keys are saved in the user settings file. The quickest and yet save way to edit that file is via the user command:

```
      ]TatIN.UserSettings -edit      
```

##### Server

API keys are saved in the file "Credentials.txt" in the `Registry\` folder in the server's home folder.

#### Credentials for your own Tatin Server 

If you run your own Tatin Server we suggest that you create a UUID and use that as an API key. 

In order for an API key to be accepted by a Tatin Server it must be added to the file `Credentials.txt` in the Registry's root directory. The file might or might not exist, and  if it exists it might be empty.

Make sure that you specify it as either

```
group1={api-key}
```

or

```
*={api-key}
```

In the first case somebody who provides that API key may publish packages only for the group <group1>.

In the second case it's a kind of master password: it allows the creation of packages with _any_ group name.

The two scenarios can be mixed:

```
group1='abc'
group2='xyz'
*='other'
```

This means that one can only publish packages with the groupname <group1> with the API key "abc", packages with the groupname <group2> with the API key "xyz", and anything else with the API key "other".

Note that `*=` means that no API key is required. On its own it's the same as having no credentials file, but it can be useful together with other group names:

```
group1='abc'
group2='xyz'
*=
```

This is interpreted as "require API keys for the groups <group1> and <group2> but allow anything else without an API key".

Finally you can allow anybody to publish packages under a particular group name without providing an API key:

```
group1='abc'
group2=
group3=''
*='other'
```

This means:

* You must provide "abc" as an API key for the group <group1>
* You may publish packages to the groups <group2> and <group3> without an API key 
* For any group name but <group1>, <group2> and <group3> you must specify <other> as API key


## Publishing


### Preparing

#### How to structure a package

A Tatin package requires all source code (functions, operators, classes, interfaces and namespace scripts) to be separated from other files (like CSS files, EXEs, DLLs etc.).

Tatin calls the former "source" and the latter "assets". 

What is what is defined in the package configuration file which _must_ exist to make a folder a Tatin package.


#### The package configuration file

What is required and how to create and change a package configuration file is discussed in the document [PackageConfiguration](./PackageConfiguration.html ).


#### The dependency file

This file is only required when the package to be published has dependencies.

Note that in case you specify a dependency that does not (yet) exist on the Registry then this has no consequences: the server will not reject such a package. 

That might come as a surprise, but there is a very good reason for this: when a bunch of packages is published then there might well be mutual or worse circular dependencies. Insisting on dependencies already being published would not work out well then.

Usually you will specify just a full package ID as a dependency. If you wish  you may also specify either a URL following the http[s]:// protocol or a zip file following the file:// protocol. 

Notes:

* Specifying a dependency with the file:// protocol means that you won't be able to publish that package to a Tatin server because it would for obvious reasons be rejected with "400 --- bad request".

  That is good enough a reason to use this with care. Only when messing around with packages not designed to be published to a Tatin server on your local machine may this be used.

* Using the http[s]:// protocol defeats Tatin's scan strategy: normally when a dependency is required Tatin will scan all defined Registries by their priority, unless the http[s]:// protocol is used. Again this should be used very carefully, if at all.


### Final step

Once the preparation is done the final step is easy. Specify the folder hosting the package:

```
]TATIN.PublishPackage /path2package [tatin]
```

The folder may or may not carry a trailing slash (`/`).

This would attempt to publish the package found in `path2package/` to the principal Tatin Server represented by the alias `[tatin]`.

You can also create a ZIP file with the `]TATIN.Pack` command and specify the ZIP file as argument:

```
]TATIN.PublishPackage /path2package/group-name-1.2.3.zip [tatin]
```


## Deleting packages

Whether you can delete a package once it was published depends on the policy the server operates:

* "Any": You may delete any package
* "JustBetas": You may delete only beta versions
* "None": You cannot delete any packages at all

Note that anything that carries something that is not a digit after the second dot in the version number and before the build separator (either a plus ("`+`", recommended) or a --- now deprecated --- dot ("`.`")) qualifies as a beta version.

Examples:

```
apltree-Foo-1.0.0                 ⍝ Not a beta
apltree-Foo-1.0.0+123             ⍝ Not a beta
apltree-Foo-1.0.0-alpha-1         ⍝ A beta
apltree-Foo-1.0.0-alpha-1+123     ⍝ A beta
```

You can find out which policy the server operates in two ways:

* Execute this user command:

  ```
  ]Tatin.GetDeletePolicy [tatin]
None
  ```

Or visit its web site, for example <https://tatin.dev>: at the bottom of the home page the server mentions the "delete" policy it operates.

## Development strategies

Let's imagine that you are supposed to develop Tatin packages for your employer, the company "XYZ".

* You run a Tatin server on your local machine, known by the alias `[my]`.

  This is just for you, nobody else. The purpose of this server is to publish packages to it at a very early stage.

  It has the highest priority of all Registries, ensuring that in case a package lives on several Registries it is found on `[my]` first.

* Your team runs a Tatin Server on the Intranet of your company. It carries the alias `[my-team]`.

  This is used to publish beta versions that your team might want to use. This has the second-highest priority.

  This is used to publish beta versions that your team might want to use. This has the second-highest priority.

* Your company also runs a Tatin Server used for productive packages.

* Your packages may depend on packages published on Tatin's principal server, known as `[tatin]`.

  Of the four Registries this one should probably have the lowest priority.

* You might also have the test Registry defined in your user settings, but with a priority of `0` so that it will be ignored when Registries are scanned for packages, but you could still use it.

You can now develop a package `Foo` and publish it on `[my]`, probably several times until it is stable.

You would then publish it on `[my-team]`. At the same time you would either delete the package from `[my]` or, if you want the Registry `[my]` to be ignored altogether, set its priority to zero.

When all is good the beta is promoted to an official release and published to the Tatin company server. At the same time the package will most likely be deleted from the Team server.

