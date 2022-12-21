Developing ipcsocket requires the use of the
[sbt](https://www.scala-sbt.org/1.x/docs/index.html) build tool. To generate a
new version of the library for testing, run
```
sbt publishLocal
```
(or run `publishLocal` in the sbt command shell).  This will generate a snapshot
version that you can add to another sbt project with:
```
libraryDependencies += "org.scalasbt" % "ipcsocket" % "1.0.1-SNAPSHOT"
```
Project tests can be run with the `test` command.

#### JNI library

There are two implementations of both the `UnixDomain*Socket*` and the
`Win32Named*Socket*` classes. One implementation uses
[jna](https://en.wikipedia.org/wiki/Java_Native_Access#External_links) while the
other uses [jni](https://en.wikipedia.org/wiki/Java_Native_Interface). In order
to test the latter implementation, it is necessary to compile native code. This
requires a working installation of [gcc](https://gcc.gnu.org) (for posix
systems) and/or [mingw-w64](http://mingw-w64.org/doku.php) (for windows) in the
sbt library path. MingW is used to build windows native libraries and is
available for mac and linux as part of the standard package management systems.
Install on mac with homebrew using:
```
brew install mingw-w64
```
Install on ubuntu/debian with:
```
sudo apt install mingw-w64
```
There is a [chocolatey](https://chocolatey.org) package available for windows,
but it requires more work to get the toolchain to work correctly with the sbt
build. As of now, it is assumed that compiling on windows is possible, but has
not been successfully been done by the author so further instructions for
compiling on windows from a contributor would be welcome. For now, the building
windows native library is verified to work on linux and mac. The
`buildWin32` task is disabled on windows but can be re-enabled by setting:
```
buildWin32 / skip := false
```

#### Releasing

Each release should include the latest binaries. The binaries are built during
each CI run on appveyor. To include them, go to the latest passing appveyor
build at https://ci.appveyor.com/project/sbt/ipcsocket/history. The required
artifacts can be found be clicking on the "Image: Ubuntu" and "Image: macOS"
links. On each PR page is a tab called "Artifacts". Click on that tab and there
will be download links for the generated artifacts. The ubuntu image provides:
* src/main/resources/linux/x86_64/libsbtipcsocket.so
* src/main/resources/linux/aarch64/libsbtipcsocket.so
* src/main/resources/win32/x86_64/sbtipcsocket.dll

while the mac os image provides:
* src/main/resources/darwin/x86_64/libsbtipcsocket.dylib

Download each of these files and overwrite the existing library in the project's
`src/main/resources` directory. Check in the overwritten libraries and push the
changes in a new PR that updates the binaries. Once that PR is merged, it is
safe to make a release off of that commit.
