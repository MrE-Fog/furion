Furion Socks5 SSL Proxy
=======================

Furion is an encrypted proxy written in Python. In essence, it's just socks5 server with chaining and ssl support. It's often used with upstream Furion servers to avoid censorship.

A few upstream servers are also included (see Installation\_ section below), which can be used directly for people who just want to get things working ASAP. These upstream servers are assigned solely for this purpose and should be safe in general. So feel free to use them, but DO NOT ABUSE. If in doubt, get a VPS and setup a Furion server of your own.

As a disclaimer, I take absolutely no responsibilities for anything that happened to your data or you for using this software.

Project is hosted at [GitHub](https://github.com/keli/furion), mirrored on [BitBucket](https://bitbucket.org/keli/furion).

Features
--------

-   Automatic upstream fail over (when multiple upstream servers are available).
-   Built-in latency check for choosing the fastest upstream.
-   Periodical upstream updates from a designated central registry.
-   Builtin DNS server/proxy to avoid poisoning.
-   Limit what ports that clients are allowed to connect to.
-   Easy account management on the server side.

Dependencies
------------

Furion has no external dependencies other than a standard Python 2.x (\>2.5) installation. Python 3.x is not yet supported.

Installation
------------

By default, when running as a client Furion creates a socks5 proxy on 127.0.0.1:11080, unless you configure otherwise in furion.cfg. There's also a simple DNS server on 127.0.0.1:15353.

### For Windows

There is a win32 binary available for download with every [release](https://github.com/keli/furion/releases), configured as client for immediate use.

If you want to build yourself, a python installation must be present, personally I used [ActivePython](http://www.activestate.com/activepython). Then install [pyinstaller](http://www.pyinstaller.org) to C:\\\\pyinstaller and use [pyinstaller.bat](https://github.com/keli/furion/blob/master/pyinstaller/pyinstaller.bat) to build.

### Automated Installation

You can use [install.sh](https://github.com/keli/furion/blob/master/install.sh) for quick installation on Mac or Linux (only tested on Debian for now):

-   If you have git or hg on your system, you can download just the install.sh script:

        curl -L -O https://github.com/keli/furion/raw/master/install.sh

-   ... Or if you don't, download the source zip file instead:

        curl -L -O https://github.com/keli/furion/archive/master.zip
        unzip master.zip
        cd furion-master

-   Now run the script to install as a client:

        sudo bash install.sh client

-   ... Or if you have your own VPS and want to install Furion as server on it:

        sudo bash install.sh server

The script will clone/copy the code to /usr/local/furion, copy appropriate furion.cfg from the examples directory, generate a new cert (if you chose to install as server), and try to run it as well as making it start at boot time. The same script can be run again in the future to upgrade to the latest master branch.

### Manual Installation

If you can't use the installation script, manual installation is straight forward too. You need at least a furion.cfg file in the same directory with the executable/script. For client, a upstream.json file is also needed for upstream checking to work.

Read configuration files in [examples](https://github.com/keli/furion/blob/master/examples) directory for more information.

Use Cases
---------

Here are a few use cases that I have found useful (all examples are for Mac by default):

### SSH Proxy to bitbucket.org

Bitbucket servers are sometimes blocked in China and I can't clone/commit via ssh. Here is what I do on a Mac:

Install [connect](https://bitbucket.org/gotoh/connect/) via [HomeBrew](http://mxcl.github.io/homebrew/) (for Debian GNU/Linux user connect is also available as "connect-proxy"):

    brew intsall connect

Configure ssh to use "connect" and furion together for bitbucket:

    # Append to the end of .ssh/config
    Host bitbucket.org
    ProxyCommand connect -a none -S localhost:11080 %h %p

Now I can clone code via ssh from bitbucket without problem:

    hg clone ssh://hg@bitbucket.org/keli/furion

### Create A Secure HTTP Proxy with Polipo

Install polipo:

    brew install polipo

Create a config file at /etc/polipo/config with the following content:

    socksParentProxy = "127.0.0.1:11080"
    socksProxyType = socks5

Reload polipo:

    launchctl unload ~/Library/LaunchAgents/homebrew.mxcl.polipo.plist
    launchctl load ~/Library/LaunchAgents/homebrew.mxcl.polipo.plist

Then you can use localhost:8123 as http proxy. This is useful when your application is only able to use a http proxy, which is the case for many console utilities (wget, pip, etc.). Many times when I cannot pip install something from terminal because of the GFW, I enter the following and voila!:

    export http_proxy=http://127.0.0.1:8123
    export ALL_PROXY=$http_proxy

### Visit Blocked Sites in Chrome with SwitchyOmega

### Visit Blocked Sites in Firefox