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

