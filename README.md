Android Topeka App with Jenkins Pipeline
===================

This is a simple app, which demonstrates how to make use of Jenkins Pipelines in Android development. As a source app was chosen [**Topeka**][1].

----------

Flow
-------------

These steps are specified in `Jenkinsfile`

 1. Checkout from source control management, e.g. git, bitbucket
 2. `assemble` (assembles only build type that was specified in as a parameter of Jenkins job), e.g. `assembleDebug`
 3. Unit tests
 4. Instrumented tests

Also sends a detailed email when jenkins job fails.

----------

Instrumented Tests
----------

Runs specified test classes on specified emulators. E.g. you have multiple emulators and you want to run particular tests on particular emulators.

This assumes, that emulators are already started on Jenkins machine on specified ports:

> emulator -ports 5554,5555 @emu1_nexus_10_2560_1600_api_21 -verbose -no-boot-anim -no-window  

Then `5554` is being set as a value of variable `PORT_EMU_1` of Jenkins job. See usage of these variable in `Jenkinsfile`.

![enter image description here](http://i.imgur.com/kVebIcm.png)

----------
Also sharing Jenkins configuration

![enter image description here](http://i.imgur.com/l6CiM5b.png)
![enter image description here](http://i.imgur.com/3OwKigA.png)
![enter image description here](http://i.imgur.com/AGKPaEN.png)
![enter image description here](http://i.imgur.com/7STZyjk.png)
  [1]: https://github.com/googlesamples/android-topeka "Topeka"
