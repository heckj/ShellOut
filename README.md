
# 🐚 ShellOut

Welcome to ShellOut, a simple package that enables you to easily “shell out” from a Swift script or command line tool.

Even though you can accomplish most of the tasks you need to do in native Swift code, sometimes you need to invoke the power of the command line from a script or tool - and this is exactly what ShellOut makes so simple.

## Fork Notes

This repo is a fork of the original ShellOut, with patches applied that had been submitted to the original, specifically:
- https://github.com/JohnSundell/ShellOut/pull/32
- https://github.com/JohnSundell/ShellOut/pull/42

To resolve an exception on Linux and to enable asynchronous handlers.

This fork is primarily for my own purposes, and hasn't been tested/validated across all platforms (IOS, MacOS, Linux, let alone Windows).

Possibly worth mentioning, somewhat comparable work/code that *doesn 't* use the
Foundation Pipe and Process classes exists in [SwiftPM](https://github.com/apple/swift-package-manager),
specifically in the package 'Basic'
(ref: [Basic/Process](https://github.com/apple/swift-package-manager/blob/master/Sources/Basic/Process.swift)).

I don't have a reference for the information, but my understanding is that SwiftPM wanted/needed a means to
invoke processes that didn't close the filehandles (which apparently happened with the Foundation libraries).
The SwiftPM Process code doesn't use Pipe at all, and instead offers API to either capture the output or
process it directly with a closure.

There's been some talk of externalizing the code in SwiftPM, but as of this fork and creation, it hasn't been
broken out, so for some tasks and needs this fork may be a more convenient option.

## Usage

Just call `shellOut()`, and specify what command you want to run, along with any arguments you want to pass:

```swift
let output = try shellOut(to: "echo", arguments: ["Hello world"])
print(output) // Hello world
```

You can also easily run a series of commands at once, optionally at a given path:
```swift
try shellOut(to: ["mkdir NewFolder", "echo \"Hello again\" > NewFolder/File"], at: "~/CurrentFolder")
let output = try shellOut(to: "cat File", at: "~/CurrentFolder/NewFolder")
print(output) // Hello again
```

In case of an error, ShellOut will automatically read `STDERR` and format it nicely into a typed Swift error:
```swift
do {
    try shellOut(to: "totally-invalid")
} catch {
    let error = error as! ShellOutError
    print(error.message) // Prints STDERR
    print(error.output) // Prints STDOUT
}
```

## Pre-defined commands

Another way to use ShellOut is by executing pre-defined commands, that enable you to easily perform common tasks without having to construct commands using strings. It also ships with a set of such pre-defined commands for common tasks, such as using Git, manipulating the file system and using tools like [Marathon](https://github.com/JohnSundell/Marathon), [CocoaPods](https://cocoapods.org) and [fastlane](https://fastlane.tools).

### Use Git

```swift
try shellOut(to: .gitInit())
try shellOut(to: .gitClone(url: repositoryURL))
try shellOut(to: .gitCommit(message: "A scripted commit!"))
try shellOut(to: .gitPush())
try shellOut(to: .gitPull(remote: "origin", branch: "release"))
try shellOut(to: .gitSubmoduleUpdate())
try shellOut(to: .gitCheckout(branch: "my-feature"))
```

### Handle files, folders and symlinks

```swift
try shellOut(to: .createFolder(named: "folder"))
try shellOut(to: .createFile(named: "file", contents: "Hello world"))
try shellOut(to: .moveFile(from: "path/a", to: "path/b"))
try shellOut(to: .copyFile(from: "path/a", to: "path/b"))
try shellOut(to: .openFile(at: "Project.xcodeproj"))
try shellOut(to: .readFile(at: "Podfile"))
try shellOut(to: .removeFile(from: "path/a"))
try shellOut(to: .createSymlink(to: "target", at: "link"))
try shellOut(to: .expandSymlink(at: "link"))
```

*For a more powerful and object-oriented way to handle Files & Folders in Swift, check out [Files](https://github.com/JohnSundell/Files)*

### Use [Marathon](https://github.com/JohnSundell/Marathon)

```swift
try shellOut(to: .runMarathonScript(at: "~/scripts/MyScript", arguments: ["One", "Two"]))
try shellOut(to: .updateMarathonPackages())
```

### Use [The Swift Package Manager](https://github.com/apple/swift-package-manager)

```swift
try shellOut(to: .createSwiftPackage(withType: .executable))
try shellOut(to: .updateSwiftPackages())
try shellOut(to: .generateSwiftPackageXcodeProject())
try shellOut(to: .buildSwiftPackage())
try shellOut(to: .testSwiftPackage())
```

### Use [fastlane](https://fastlane.tools)

```swift
try shellOut(to: .runFastlane(usingLane: "appstore"))
```

### Use [CocoaPods](https://cocoapods.org)

```swift
try shellOut(to: .updateCocoaPods())
try shellOut(to: .installCocoaPods())
```

Don't see what you're looking for in the list above? You can easily define your own commands using `ShellOutCommand`. If you've made a command you think should be included among the built-in ones, feel free to [open a PR](https://github.com/JohnSundell/ShellOut/pull/new/master)!

## Installation

### For scripts

- Install [Marathon](https://github.com/johnsundell/marathon).
- Add ShellOut to Marathon using `$ marathon add https://github.com/JohnSundell/ShellOut.git`.
- Alternatively, add `https://github.com/JohnSundell/ShellOut.git` to your `Marathonfile`.
- Write your script, then run it using `$ marathon run yourScript.swift`.

### For command line tools

- Add `.package(url: "https://github.com/JohnSundell/ShellOut.git", from: "2.0.0")` to your `Package.swift` file's `dependencies`.
- Update your packages using `$ swift package update`.

## Help, feedback or suggestions?

- [Open an issue](https://github.com/JohnSundell/ShellOut/issues/new) if you need help, if you found a bug, or if you want to discuss a feature request.
- [Open a PR](https://github.com/JohnSundell/ShellOut/pull/new/master) if you want to make some change to ShellOut.
- Contact [@johnsundell on Twitter](https://twitter.com/johnsundell) for discussions, news & announcements about ShellOut & other projects.
