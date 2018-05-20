`homebrew cask` doesn't offer the ability to upgrade a package, as `brew cask upgrade <pkg-name>` is not even available. [TODO: why, link]
the closest alternative is `homebrew cask reinstall <pkg-name>`

why it doesn't work
a `jdk` for mac comes in two component: 

'Java Preference Panel' give us the path:
`/Library/Internet\ Plug-Ins/JavaAppletPlugin.plugin/Contents/Home`
`/usr/libexec/java_home -V` gives us:
`/Library/Java/JavaVirtualMachines/jdk1.8.0_131.jdk/Contents/Home`
A `homebrew cask install` will download the latest package at `/usr/local/Caskroom/java/`, then install to both directorys, whereas if you install the package you downloaded from java.com, or upgrade the package from 'Java Preference Panel', it only installs/upgrades the Web Applet plugin.

There's also `/System/Library/Frameworks/JavaVM.framework/Versions/Current/Commands/` folder, which maintains a copy of bin commands in java_home(I think), and it will be updated when a new version of jdk is installed.

// ref content:https://discussions.apple.com/thread/7630285?start=0&tstart=0
When you download Java from Oracle, it only installs the Web Applet Plugin.
If you installed the Java 1.8 JDK previously, then later installed the applet plugin, they could be at two different versions.

There are two parts to Java on OS X. There is a web plugin and a JVM. They are entirely separate components.
The direct download at Java.com will only install the web plugin.
The JDK download will install both the JVM and the web plugin.

The following is the output of uninstall java:
```
➜  ~ brew cask uninstall java
==> Uninstalling Cask java
==> Running uninstall process for java; your password may be necessary
==> Removing launchctl service com.oracle.java.Helper-Tool
==> Removing launchctl service com.oracle.java.Java-Updater
==> Quitting application ID com.oracle.java.Java-Updater
==> Quitting application ID net.java.openjdk.cmd
==> Uninstalling packages:
com.oracle.jdk8u131
com.oracle.jre
==> Removing files:
/Library/Internet Plug-Ins/JavaAppletPlugin.plugin
/Library/Java/JavaVirtualMachines/jdk1.8.0_131.jdk/Contents
/Library/PreferencePanes/JavaControlPanel.prefPane
/Library/Java/Home
==> Purging files for version 1.8.0_131-b11,d54c1d3a095b4ff2b6607d096fa80163 of Cask java
```
This shows a brew cask uninstall uninstalls both jdk and jre.

Then I installed latest java8 using `brew cask install java8`(`brew tap caskroom/versions` first)
Then all 3 locations show the latest version
- `/Library/Internet\ Plug-Ins/JavaAppletPlugin.plugin/Contents/Home/bin/java -version`
- `/Library/Java/JavaVirtualMachines/jdk1.8.0_152.jdk/Contents/Home/bin/javac -version`
- `/System/Library/Frameworks/JavaVM.framework/Versions/A/Commands/javac -version`


# Install and maintain multiple java versions including java 7
- manually download java 7. Reason: https://github.com/caskroom/homebrew-cask/issues/37772
- manually install the dmg file
Find: it seems this install is pretty non-destructive.
one more entry is added
```
➜  ~ /usr/libexec/java_home -V
Matching Java Virtual Machines (2):
    1.8.0_152, x86_64:	"Java SE 8"	/Library/Java/JavaVirtualMachines/jdk1.8.0_152.jdk/Contents/Home
    1.7.0_80, x86_64:	"Java SE 7"	/Library/Java/JavaVirtualMachines/jdk1.7.0_80.jdk/Contents/Home
```
other than that, nothing is changed
```
~ java -version
java version "1.8.0_152"

➜  ~ echo $JAVA_HOME
/Library/Java/JavaVirtualMachines/jdk1.8.0_152.jdk/Contents/Home
```

- install jenv
```
➜  ~ brew update
➜  ~ brew install jenv
/usr/local/Cellar/jenv/0.4.4: 78 files, 66KB, built in 1 second
➜  ~ which jenv
/usr/local/bin/jenv
➜  ~ jenv --version
jenv 0.4.4

➜  ~ echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.zshrc
➜  ~ echo 'eval "$(jenv init -)"' >> ~/.zshrc
```
- add jdk to jenv
```
➜  ~ jenv add /Library/Java/JavaVirtualMachines/jdk1.8.0_152.jdk/Contents/Home
or with `java_home` command:
➜  ~ jenv add $(/usr/libexec/java_home -v 1.8)
oracle64-1.8.0.152 added
1.8.0.152 added
1.8 added
➜  ~ jenv add /Library/Java/JavaVirtualMachines/jdk1.7.0_80.jdk/Contents/Home
or with `java_home` command:
➜  ~ jenv add $(/usr/libexec/java_home -v 1.7)
oracle64-1.7.0.80 added
1.7.0.80 added
1.7 added
```
- configure jenv
```
➜  ~ jenv global 1.8
➜  ~ jenv enable-plugin export
You may restart your session to activate jenv export plugin echo export plugin activated

restart manually...

➜  ~ echo $JAVA_HOME
/Users/lzhou/.jenv/versions/1.8

➜  ~ $JAVA_HOME/bin/java -version
java version "1.8.0_152"
Java(TM) SE Runtime Environment (build 1.8.0_152-b16)
Java HotSpot(TM) 64-Bit Server VM (build 25.152-b16, mixed mode)
```
