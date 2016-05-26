These are the steps I take to package a jar file for the mac app store. In this repo I'm using a sample Swing app called Nightpad. Replace it with your own jar and edit `build.xml` with your own info. Then do the following:

These commands will create the app bundle and add/remove some Java stuff:

```bash
export JAVA_HOME=`/usr/libexec/java_home`
ant
find Nightpad.app/Contents/PlugIns/ -type f \( -name "libgstreamer-lite.dylib" -or -name "libjfxmedia*.dylib" \) -exec rm {} \;
cp -r /Library/Java/JavaVirtualMachines/jdk1.8.0_40.jdk/Contents/MacOS Nightpad.app/Contents/PlugIns/jdk1.8.0_40.jdk/Contents/MacOS
```

Now is a good time to open `Nightpad.app/Contents/Info.plist` and edit some things. At the very least, update the `CFBundleVersion` to the appropriate number. You should also add the following so it doesn't look like crap on high res displays:

```xml
<key>NSHighResolutionCapable</key>
<true/>
```

Then you need to sign it. The strings containing "YOUR NAME HERE" can be found in the keychain access app, assuming you've added your application and installer certificates:

```bash
find Nightpad.app/Contents/ -type f \( -name "*.jar" -or -name "*.dylib" -or -name "jspawnhelper" \) -exec codesign --deep -v -f -s "3rd Party Mac Developer Application: YOUR NAME HERE" --entitlements generic.entitlements {} \;
codesign --deep -v -f -s "3rd Party Mac Developer Application: YOUR NAME HERE" Nightpad.app/Contents/PlugIns/*
codesign --deep -v -f -s "3rd Party Mac Developer Application: YOUR NAME HERE" --entitlements app.entitlements Nightpad.app
productbuild --component Nightpad.app /Applications --sign "3rd Party Mac Developer Installer: YOUR NAME HERE" Nightpad.pkg
```

Now you should have a pkg file that you can upload to the MAS with the application loader app.
