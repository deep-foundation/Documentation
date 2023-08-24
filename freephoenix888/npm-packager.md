[источник](https://github.com/deep-foundation/documentation/wiki/Npm-Packager)
# пакетный менеджер npm
* ДАННАЯ СТРАНИЦА НАХОДИТСЯ НА ДОРАБОТКЕ


---
# FAQ
## How to publish a package?
1. Create PackageNamespace with string value that represents the package name
2. Create PackageVersion from PackageNamespace to Package with string value that represents the package version
3. Create Publish from Package to PackageQuery


## How to specify package dependencies ?
Now there is only one way to establish a dependency on another package.
Let's use link notation for the example. Let 0 be the absence of a link to another link.
```
(from type to)
```

So by using a link by another link, I mean the following:
Suppose there is a link `a`.
Then the other link can use it like this:
```
(a 0 0) - as from
(0 a 0) - as type
(0 0 a) - as to
```

So, if the `a` link is in another package, that is, there is a link `(package1 Contain a)`, then `package1` will be a dependency of package `package2` if one of the following links exists:
```
(package2 Contain (a 0 0))
(package2 Contain (a 0 a 0))
(package2 Contain (0 0 a))
```

Two problems follow from this:
We currently have no mechanism for specifying dependencies between packages in any other way. Which means if `package2` does not explicitly use links from `package1` as shown above, then `package1` will not become a dependency of package `package2` in any way.
If you use those packages in the handler which are not dependent on `package2` then `deep.id` has the risk of not detecting them, since they won't install automatically when you install the package.

And the question is this - do you already have a situation that falls under the problem or not?

And yes, all this time you have been using `@deep-foundation/core` package exactly as a dependency, it is exactly the same package as any other, the only difference is that it is installed immediately in Deep migrati