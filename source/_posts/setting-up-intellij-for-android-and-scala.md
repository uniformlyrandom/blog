---
title: "Setting Up IntelliJ For Android And Scala On Ubuntu"
categories: ["code"] 
tags: ["intellij","android","scala"]
date: 2012/02/10
---

### Prerequisites

#### Android SDK

 1. get the latest [SDK](https://developer.android.com/sdk/index.html)

 2. after extracting, run `./tools/android`

 3. from the list select the Android version your afer, right now its **Android 4.0.3 (API 15)** (I also installed Android 2.3.3), click on **Install packages** to get the installation started

 4. setup your env ``echo "export ANDROID_HOME=~/Applications/android-sdk-linux/" >> ~/.bashrc``

 5. refresh your currect session `source ~/.bashrc`

#### Adding SBT support to IntelliJ

 1. open **File** > **Settings** > **Plugin**

 2. click on **Browser repositories**

 3. find **SBT**, right click and select **Download and install**

#### Setup [SBT](https://github.com/harrah/xsbt/wiki/Getting-Started-Setup)

 1. create folder `~/bin/` (if you dont have `~/bin` in your PATH, then you can do this: `echo "PATH=$PATH:~/bin" >> ~/.bashrc` (refresh your currect session `source ~/.bashrc`)

 2. [download](https://github.com/harrah/xsbt/wiki/Getting-Started-Setup) `sbt-launcher.jar` and place it under `~/bin`

 3. create launcher: `touch ~/bin/sbt` and then ``echo 'java -Xmx512M -jar `dirname $0`/sbt-launch.jar "$@"' >> ~/bin/sbt``

 4. make it executable: `chmod u+x ~/bin/sbt`
 
#### Setup [android-plugin](https://github.com/jberkel/android-plugin/wiki/Getting-started) for Scala support

 1. install giter8 `curl https://raw.github.com/n8han/conscript/master/setup.sh | sh`

 2. run it `~/bin/cs n8han/giter8`

#### Setup [sbt-idea](https://github.com/mpeltonen/sbt-idea)

 1. create folder (if it doesnt exist) `mkdir -p ~/.sbt/plugins/`

 2. create the file `~/.sbt/plugins/build.sbt` and add the following lines to it:

		resolvers += "sbt-idea-repo" at "http://mpeltonen.github.com/maven/"
		addSbtPlugin("com.github.mpeltonen" % "sbt-idea" % "1.0.0")

### Finally, now that you have all the tools you need, proceed to creating a new project

 1. run the android plugin project setup tool: `~/bin/g8 jberkel/android-app`, follow the onscreen questions (or press return for defaults)  
    this will create the project folder and create the files needed to build an android project

 2. open the new folder `cd <project name>`

 3. setup IntelliJ project support `sbt gen-idea`

 4. open the project in IntelliJ 

