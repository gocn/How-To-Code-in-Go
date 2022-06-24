# How to Distribute Go Modules

### Introduction

Many modern programming languages allow developers to distribute ready-made libraries for others to use in their programs, and Go is no exception. While some languages use a central repository to install these libraries, Go distributes them from the same version control repository used to create the libraries. Go also uses a versioning system called [semantic versioning](https://semver.org/) to show users when and what kinds of changes have been made. This helps users know whether a newer version of a module is safe to quickly upgrade to and helps ensure their software continues to work with the module going forward.

In this tutorial, you will create and publish a new module, learn to use semantic versioning, and publish a semantic version of your module.

## Prerequisites

-   Go version 1.16 or greater installed. To set this up, follow the [How To Install Go](https://www.digitalocean.com/community/tutorials/how-to-install-go-on-ubuntu-20-04) tutorial for your operating system.
-   An understanding of Go modules, which you can find in the [How To Use Go Modules](https://www.digitalocean.com/community/tutorials/how-to-use-go-modules) tutorial.
-   Familiarity with Git, which you can get by following [How To Use Git: A Reference Guide](https://www.digitalocean.com/community/cheatsheets/how-to-use-git-a-reference-guide).
-   An empty public GitHub repository named `pubmodule` for your published module. To get started, follow the [GitHub documentation for creating a repository](https://docs.github.com/en/get-started/quickstart/create-a-repo).

## Creating a Module to Publish

Unlike many other programming languages, a Go module is distributed directly from the source code repository it lives in instead of an independent package repository. This makes it easier for users to find modules referenced in their code and for module maintainers to publish new versions of their module. In this section, you’ll create a new module, which you will then publish to make it available for other users.

To start creating your module, you’ll use `git clone` on the empty repository you created as part of the prerequisites to download the initial repository. This repository can be cloned anywhere you’d like on your computer, but many developers tend to have a directory for their projects. In this tutorial, you’ll use a directory named `projects`.

Make the `projects` directory and navigate to it:

```
mkdir projects
cd projects
```

From the `projects` directory, run `git clone` to clone your repository to your computer:

```
git clone git@github.com:your_github_username/pubmodule.git
```

Cloning the module will download your empty module into the `pubmodule` directory inside your `projects` directory. You may get a warning that you’ve cloned an empty repository, but this isn’t anything to worry about:

```
Output
Cloning into 'pubmodule'...
warning: You appear to have cloned an empty repository.
```

Next, change into the directory you downloaded:

```
cd pubmodule
```

Once you’re in the module’s directory, you’ll use `go mod init` to create your new module and pass in the repository’s location as the module name. Ensuring the module name matches the repository location is important because this is how the `go` tool finds where to download your module when it’s used in other projects:

```
go mod init github.com/your_github_username/pubmodule
```

Go will confirm your module is created by letting you know it’s created the `go.mod` file:

```
Output
go: creating new go.mod: module github.com/your_github_username/pubmodule
```

Lastly, use your favorite text editor, such as `nano`, to create and open a file with the same name as your repository: `pubmodule.go`.

```
nano pubmodule.go
```

The name of this file can be anything, but using the same name as the package makes it easier to know where to start when working with an unfamiliar package. The package name itself, though, should be the same as your repository name. This way, when someone references a method or type from your package, it matches the repository, such as `pubmodule.MyFunction`. This will make it easier for them to know where the package came from in case they need to refer to it later.

Next, add a `Hello` method to your package that will return the string `Hello, You!`. This will be the function available to anyone importing your package:

projects/pubmodule/pubmodule.go

```go
package pubmodule

func Hello() string {
  return "Hello, You!"
}
```

You’ve now created a new module using `go mod init` with a module name that matches your remote repository (`github.com/your_github_username/pubmodule`). You’ve also added a file named `pubmodule.go` to your module with a function called `Hello` that users of your module can call. Next, you’ll publish your module to make it available to others.

## Publishing the Module

Once you’ve created a local module and you’re ready to make it available to other users, it’s time to publish your module. Since Go modules are distributed from the same code repositories they’re stored in, you’ll commit your code to your local Git repository and push it to your repository at `github.com/your_github_username/pubmodule`.

Before you commit your code to your local Git repository, it’s a good idea to make sure you won’t be committing any files you don’t expect to commit, which would then be published publicly when you push the code to GitHub. Using the `git status` command inside the `pubmodule` directory will show you all the files and changes that will be committed:

```
git status
```

The output will look similar to this:

```
Output
On branch main

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
go.mod
pubmodule.go
```

You should see the `go.mod` file the `go mod init` command created, and the `pubmodule.go` file you created the `Hello` function in. Depending on how you created your repository, you may have a different branch name than this output. Most commonly, the names will be either `main` or `master`.

When you’re sure you have only the files you’re looking for, you can then stage the files with `git add` and commit them to the repository with `git commit`:

```
git add .
git commit -m "Initial Commit"
```

The output will look similar to this:

```
Output
[main (root-commit) 931071d] Initial Commit
 2 files changed, 8 insertions(+)
 create mode 100644 go.mod
 create mode 100644 pubmodule.go
```

Finally, use the `git push` command to push your module to the GitHub repository:

```
git push
```

The output will look similar to this:

```
Output
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (4/4), 367 bytes | 367.00 KiB/s, done.
Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
To github.com:your_github_username/pubmodule.git
 * [new branch]      main -> main
```

After running the `git push` command, your module will be pushed to your repository and will now be available for anyone else to use. If you don’t have any versions published, Go will use the code in your repository’s default branch as the code for your module. It doesn’t matter if your default branch is named `main`, `master`, or something else, only what your repository’s default branch is set to.

In this section, you took the local Go module you created and published it to your GitHub repository to make it available for other people to use. While you now have a published module, another part of maintaining a public module is ensuring users of your module can use a stable version of it. You’ll likely want to make changes and add features to your module going forward, but if you make those changes without using versions in your module, you could accidentally break the code of someone using your module. To solve this problem, you can add versions to your module when you reach a new milestone in development. When adding new versions, though, be sure to choose a meaningful version number so your users know whether it’s safe for them to upgrade right away or not.

## Semantic Versioning

A meaningful version number gives your users an idea of how much the public interface, or API, they interact with has changed. Go conveys these changes through a versioning scheme known as [semantic versioning](https://semver.org/), or “SemVer” for short. (Semantic versioning uses the version string to convey meaning about code changes, which is where Semantic Versioning gets its name.) Go’s module system follows SemVer to determine which versions are newer than the version you’re currently using, as well as whether a newer version of a module is safe to upgrade to automatically.

Semantic versioning gives each number in a version string a meaning. A typical version in SemVer contains three primary numbers: the major version, the minor version, and the patch version. Each of these numbers is combined with a `.` to form the version, such as `1.2.3`. The numbers are ordered with the major version first, the minor version second, and the patch version last. This way, when looking at a version, you can see which one is newer because the number in a specific spot is higher than previous versions. For example, the version `2.2.3` is newer than `1.2.3` because the major version is higher. Likewise, the version `1.4.3` is newer than `1.2.10` because the minor version is higher. Even though `10` is higher than `3` in the patch version, the minor version `4` is higher than `2` so that version takes precedence. When a number in the version string increases, all the other parts of the version following it reset to `0`. For example, increasing the minor version of `1.3.10` would result in `1.4.0` and increasing the major version of `2.4.1` would result in `3.0.0`.

Using these rules allows Go to determine which version of a module to use when you run `go get`. As an example, suppose you have a project using version `1.4.3` of the module, `github.com/your_github_username/pubmodule`. If you depend on `pubmodule` being stable, you may only want to automatically upgrade the patch version (the `.3`). If you run the command `go get -u=patch github.com/your_github_username/pubmodule`, Go would see that you want to upgrade the patch version of the module and would only look for new versions with `1.4` as the major and minor part of the version.

When creating a new release of your module, it’s important to consider how the public API of your module has changed. Each part of a semantic version string conveys the scope of API change to both you and your users. These types of changes typically fall into three different categories, lining up with each component of the version. The smallest changes increase the patch version, medium-sized changes increase the minor version, and the largest changes increase the major version. Using these categories to determine which version number to increase will help you avoid breaking your own code and the code of anyone else who relies on your module.

### Major Version Numbers

The first number in a SemVer version is the major version number (`1.4.3`). The major version number is the most important number to consider when releasing a new version of your module. A major version change is used to signal backward-incompatible changes to your public API. A backward-incompatible change would be any change in your module that would cause someone’s program to break if they upgraded without making any other changes. Breaking could mean anything from a failure to build because a function name has changed, or a change in how the library works that results in the same method returning `"v1"` instead of `"1"`. This is only for your public API, though, meaning any exported types or methods someone else could use. If the version only includes improvements a user of your library would not notice, it doesn’t need a major version change. A way to remember which changes fit into this category might be that anything considered an “update” or a “delete” would be a major version increase.

**Note:** Unlike the other types of numbers in SemVer, the major version `0` has an additional special significance. The major version `0` is considered the “in development” version. Any SemVer with a major version `0` is not considered stable and anything can change in the API at any time. When you create a new module it’s best to start with major version `0` and only update minor and patch versions until you’ve finished initial development of your module. Once your module’s public API is done changing and considered stable for your users, it’s time to start with version `1.0.0`.

Take the following code as an example of what a major version change might look like. You have a function called `UserAddress` that currently accepts a `string` as a parameter and returns a `string`:

```go
func UserAddress(username string) string {
	// return user address as a string
}
```

While the function currently returns a `string`, you may determine it would be better for you and your users if the function returned a `struct` like `*Address`. This way you can include additional data already split apart, such as a postal code:

```go
type Address struct {
	Address    string
	PostalCode string
}

func UserAddress(username string) *Address {
	// return user address and postal code struct
}
```

This would be an example of a major version change because it would require your users to make changes to their own code in order to use it. The same would be true if you decided to remove `UserAddress` completely because your users would need to update their code to use the replacement.

Another example of a major version change would be adding a new parameter to the `UserAddress` function, even if it still returns a `string`:

```go
func UserAddress(username string, uppercase bool) string {
	// return user address as a string, uppercase if bool is true
}
```

Since this change also requires your users to update their code if they’re using the `UserAddress` function, it would also require a major version increase.

Not all changes you make to your code will be as drastic, though. Sometimes you’ll make changes to your public API that add new functions or values, but that don’t change any existing ones.

### Minor Version Numbers

The second number in a SemVer version is the minor version number (`1.4.3`). A minor version change is used to signal backward-compatible changes to your public API. A backward-compatible change would be any change that doesn’t affect code or projects currently using your module. Similar to the major version number, this only affects your public API. A way to remember which changes fit in this category might be anything considered an “addition”, but not an “update”.

Using the same example from the major version number, imagine you have a method named `UserAddress` that returns a `string`:

```go
func UserAddress(username string) string {
	// return user address as a string
}
```

This time, though, instead of updating `UserAddress` to return `*Address`, you decide to add a completely new method named `UserAddressDetail`:

```go
type Address struct {
	Address    string
	PostalCode string
}

func UserAddress(username string) string {
	// return user address as a string
}

func UserAddressDetail(username string) *Address {
	// return user address and postal code struct
}
```

Adding this new `UserAddressDetail` function doesn’t require changes by your users if they update to this version of your module, so it would be considered a minor version number increase. They can continue using `UserAddress` and would only need to update their code if they’d rather include the additional information from `UserAddressDetail`.

Public API changes likely aren’t the only time you’ll release a new version of your module, though. Bugs are an inevitable part of software development and the patch version number is there to cover up those holes.

### Patch Version Numbers

The patch version number is the last number in a SemVer version (`1.4.3`). A patch version change is any change that **doesn’t affect** the module’s **public API**. Changes that don’t affect a module’s public API tend to be things like bug fixes or security fixes. Using the `UserAddress` function from the previous examples again, suppose a release of your module is missing part of an address in the `string` the function returns. If you release a new version of your module to fix that bug, it would only increase the patch version. The release wouldn’t include any changes to how a user uses the `UserAddress` public API, only the correctness of the data returned.

As you’ve seen in this section, carefully choosing a new version number is an important way to earn the trust of your users. Using semantic versioning shows users the amount of work required to update to a new version, and you won’t accidentally surprise them with an update that breaks their program. After considering the changes you’ve made to your module and determining the next version number to use, you can publish the new version and make it available to your users.

## Publishing a New Module Version

Before you publish a new version of your module, you’ll need to update your module with the changes you’re planning to make. Without any changes, you won’t be able to determine which part of the semantic version to increase. For the module in this tutorial, you’ll add a new `Goodbye` method to complement the `Hello` method, and then you’ll publish that new version for users to use.

First, open the `pubmodule.go` file and add the new `Goodbye` method to your public API:

pubmodule/pubmodule.go

```go
package pubmodule

func Hello() string {
  return "Hello, You!"
}

func Goodbye() string {
  return "Goodbye for now!"
}
```

Once you’ve saved your change, you’ll want to check which changes are expected to be committed by running `git status`:

```
git status
```

The output will look similar to this, showing that the only change in your module is the method you added to `pubmodule.go`:

```
Output
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
modified:   pubmodule.go

no changes added to commit (use "git add" and/or "git commit -a")
```

Next, add the change to the staged files and commit the change to your local repository with `git add` and `git commit`:

```
git add .
git commit -m "Add Goodbye method"
```

The output will look similar to this:

```
Output
[main 3235010] Add Goodbye method
 1 file changed, 4 insertions(+)
```

After the changes are committed, you’ll need to push them to your GitHub repository. In a larger software project, or when working with other developers on a project, this step would commonly be slightly different. When doing development on a new feature, a developer would create a Git branch to put changes in until the new feature is stable and ready to be released. Once that happens, another developer would review the changes in the branch to add a second pair of eyes that might catch issues the first developer may have missed. Once the review is finished, the branch would then be merged into the default branch (such as `master` or `main`). Between releases, the default branch would accumulate these types of changes until it’s time to publish a new release.

Since your module here doesn’t go through this process, pushing the changes you’ve made to the repository will simulate the accumulation of changes instead:

```
git push
```

The output will look similar to this:

```
Output
numerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 369 bytes | 369.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To github.com:your_github_username/pubmodule.git
   931071d..3235010  main -> main
```

The output shows the new code is ready for users to use in the default branch.

Up to this point, everything you’ve done has been the same as initially publishing your module. However, now an important part of releasing a new version comes up: choosing a new version number.

If you look at the changes you’ve made to the module, the only change to the public API (or really any change) is adding the `Goodbye` method to your module. Since a user could update from the previous version, which only had the `Hello` function, without making changes on their part, this change would be a backward-compatible change. In semantic versioning, a backward-compatible change to the public API would mean an increase in the minor version number. This is the first version of your module being published, though, so there’s no previous version to increase. If you consider `0.0.0` to be “no version” then incrementing the minor version would lead you to version `0.1.0`, the next version of your module.

Now that you have a version number to give to the release of your module, you can use it, paired with Git tags, to publish a new version. When developers use Git to keep track of their source code, even in languages other than Go, a common convention is to use Git’s tags to keep track of which code was released for a specific version. This way, if they ever need to make changes to an old version, they can use the tag. Since Go is already downloading modules from the source repositories, it takes advantage of this practice by using those same version tags.

To publish a new version of your own module using these tags, you will tag the code you’re releasing with the `git tag` command. As an argument to the `git tag` command, you’ll also need to provide the version tag. To create the version tag, start with the prefix `v`, for version, and add your SemVer immediately after it. In the case of your module, your final verison tag would be `v0.1.0`. Now, run `git tag` to tag your module with the version tag:

```
git tag v0.1.0
```

Once the version tag is added locally, you’ll still need to push the tag to your GitHub repository, which you can do using `git push` with `origin`:

```
git push origin v0.1.0
```

After the `git push` command succeeds, you’ll see that a new tag, `v0.1.0`, has been created:

```
Output
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0
To github.com:your_github_username/pubmodule.git
 * [new tag]         v0.1.0 -> v0.1.0
```

The output above shows that your tag has been pushed and your GitHub repository has a new `v0.1.0` tag available for users of your module to reference.

Now that you’ve published a new version of your module with `git tag`, whenever a user runs `go get` to get the latest version of your module, it will no longer download a version based on the latest commit hash from the default branch. Once a module has a released version, the `go` tool will start using those versions to determine the best way to update the module. Paired with semantic versioning, this allows you to iterate and improve your modules while also providing your users with a consistent and stable experience.

## Conclusion

In this tutorial, you created a public Go module and published it to a GitHub repository so that other people can use it. You also used semantic versioning to determine the best version number for your module. Finally, you expanded your module’s functionality and, using semantic versioning, published the new version with the confidence that you won’t be breaking programs that depend on it.

If you’d like more information on semantic versioning, including how to add information other than numbers to your version, the [Semantic Versioning website](https://semver.org/) goes into great detail. The Go documentation also has a [module version numbering](https://golang.org/doc/modules/version-numbers) page that explains how Go specifically uses SemVer.

For more information on Go modules, the Go project has [a series of blog posts](https://blog.golang.org/using-go-modules) detailing how Go tools interact with and understand modules. The Go project also has a very detailed and technical reference for Go modules in the [Go Modules Reference](https://golang.org/ref/mod).

This tutorial is also part of the [DigitalOcean](https://www.digitalocean.com/) [How to Code in Go](https://www.digitalocean.com/community/tutorial_series/how-to-code-in-go) series. The series covers a number of Go topics, from installing Go for the first time to how to use the language itself.