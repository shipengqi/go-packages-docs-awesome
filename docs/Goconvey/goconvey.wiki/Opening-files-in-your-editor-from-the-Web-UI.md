You may have noticed when your tests fail, GoConvey provides a link directly to the failing line in the file:

![](http://i.imgur.com/6n5Hn8I.gif)

To enable these links, you need to register a custom URL Scheme handler for your platform. The name of the custom URL Scheme is `goconvey` (clever, huh?).

## OSX

In this example, I will be using MacVim. If you're using Sublime, you will want to change the path to your Sublime installation.

* Find the application you want to handle the `goconvey` URL protocol handler: `/Applications/MacVim.app`
* Edit the `Info.plist` file. There are two ways to do this:
    * Using your terminal:
        * Edit `/Applications/MacVim.app/Contents/Info.plist`
        * Find the section that has `<key>CFBundleURLTypes</key>`
        * Add a new `<dict>` entry that looks like this (lines 1198 - 1207):

        ![](http://i.imgur.com/b5HR4VZ.gif)

        If you don't have an `<array>` section, then create it.

    * Using the GUI
        * In Finder, navigation to the Applications folder and right click on your application and click "Show Package Contents":

        ![](http://i.imgur.com/RyAfLio.gif)

        * Open up the Contents folder and double click on the `Info.plist` file.
        * Find the section labeled `URL types`:

        ![](http://i.imgur.com/HeiAYnz.png)

        * Expand the section and create an entry (by clicking on the "plus" icon) that looks like this:

        ![](http://i.imgur.com/WPxI9lC.png)

        You want to select `Editor` as the `Document Role`, and the string entry inside the `URL Schemes` section **must** be `goconvey`.

        * After you edit this file, you must "update" your application: `touch /Applications/MacVim.app/`

If everything went well, you should click on the `Line #` link in GoConvey's web UI and get a prompt from your browser:

![](http://i.imgur.com/j2kpKwy.png)

Click "Launch Application" (and you'll probably want to check the "Remember..." checkbox as well.

## Windows

Until someone comes up with a good tutorial, here are some resources:

- http://msdn.microsoft.com/en-us/library/aa767914(v=vs.85).aspx
- http://stackoverflow.com/questions/389204/how-do-i-create-my-own-url-protocol-e-g-so

## Linux
To install a customer schema handler, at least in Ubuntu, you can use the script below. It will install a script that handles the links in GoConvey's web GUI and formats it to work with the browsers, at least Visual Studio Code and Sublime. It will also register a desktop handler for the goconvey:// schema.

It uses your default editor if the environment variable EDITOR is set.
If you like to use a separate editor just for goconvey links, you can set GOCONVEY_EDITOR.
Some editor need extra flags to handle filenames with line number. These flags can be set in GOCONVEY_EDITOR_FLAGS.
If no editor environment variables are set it defaults to Visual Studio Code.

This script is tested on Ubuntu 16.04 with Unity.

Save the content below in a file and run it as root.
```bash
#!/usr/bin/env bash

cat <<EOF-HANDLER > /usr/bin/goconvey-handler
#!/usr/bin/env bash
request="\${1#*://}"             # Remove schema from url (goconvey://)
request="\${request#*?url=}"     # Remove open?url=
request="\${request#*://}"       # Remove file://
request="\${request//%2F//}"     # Replace %2F with /
request="\${request/&line=/:}"   # Replace &line= with :
request="\${request/&column=/:}" # Replace &column= with :
if [ -n "\${GOCONVEY_EDITOR}" ]; then
    \$GOCONVEY_EDITOR \$GOCONVEY_EDITOR_FLAGS "\$request"    # Launch specified goconvey editor
elif [ -n "\${EDITOR}" ]; then
    \$EDITOR \$GOCONVEY_EDITOR_FLAGS "\$request"             # Launch default editor
else
    code -g "\$request"                                    # Launch code as editor
fi
EOF-HANDLER

cat <<EOF-DESKTOP > /usr/share/applications/goconvey-handler.desktop
[Desktop Entry]
Name=GoConvey URL Handler
GenericName=Text Editor
Comment=Handle URL Scheme goconvey://
Exec=/usr/bin/goconvey-handler %u
Terminal=false
Type=Application
MimeType=x-scheme-handler/goconvey;
Icon=code
Categories=TextEditor;Development;Utility;
Name[en_US]=GoConvey URL handler
EOF-DESKTOP

chmod +x /usr/bin/goconvey-handler
update-desktop-database
xdg-mime default /usr/share/applications/goconvey-handler.desktop x-scheme-handler/goconvey
```

Here are some resources:

- https://stackoverflow.com/questions/411544/custom-protocol-in-linux
- https://askubuntu.com/questions/527166/how-to-set-subl-protocol-handler-with-unity
- https://gist.github.com/peterrosell/bf44a86cf0c6df920ce88a01c828cfdc