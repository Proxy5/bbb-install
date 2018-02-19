
# bbb-install 

Want to setup your own [BigBlueButton 2.0-beta](http://docs.bigbluebutton.org/2.0/20overview.html) (referred hereafter as BigBlueButton 2.0) server?  Got a Ubuntu 16.04 64-bit server that is not behind a firewall?  (If your server is behind a firewall, then there are additional steps, such as configuring you firewall, that are beyond the scope of a script -- such as changing the settings on your firewall. These steps are covered in detail in the [install documentation](http://docs.bigbluebutton.org/2.0/20install.html#step-by-step-install).)

`bbb-install.sh` is a shell script that automates the [install steps](http://docs.bigbluebutton.org/2.0/20install.html#step-by-step-install) for installing BigBlueButton 2.0.

Many companies that offer virtual and bare metal servers can provide a Ubuntu 16.04 64-bit server with single public IP address (no firewall).  [Digital Ocean](https://www.digitalocean.com/) is an example of such companies.  For these servers, you can use `bbb-install.sh` to setup a new BigBlueButton 2.0 server in a few minutes.

Furthermore, if you configure a fully qualified domain name (FQDN), such as `bbb.my-server.com`, then resolves to the public IP address of your server, then `bbb-install.sh` can also use Let's Encrypt to generate a 4096 bit secure socket layers (SSL) certificate and configure BigBlueButton to use it to serve content via HTTPS.  We recommend using a SSL certificate for production servers.  Chome require HTTPS before allowing users to share their microphone via web real-time communications (WebRTC).

If you are quickly setting up a BigBlueButton server for testing, you can still configure the server with only an IP address.  If so, test the WebRTC with FireFox (at the time of this writing it still let's the user use WebRTC without a HTTPS).

## Overview

If you have a server that meets the [minimual server requirements](http://docs.bigbluebutton.org/install/install.html#minimum-server-requirements) and that server has a single IP address with no firewall, then you can use `bbb-install.sh` to install the latest build of BigBlueButton 2.0 with a single command.

If you have a fully qualified domain name (FQDN), such as `bbb.my-server.com`, that resolves to the server, then you can use `bbb-install.sh` to install
  * an SSL certificate from Let's Encrypt, 
  * the latest developer build of the HTML5 client, and/or
  * the GreenLight front-end.

To run this script, you can fork [this github repository](https://github.com/bigbluebutton/bbb-install) an execute `bbb-install.sh` at the command line.  

We've also host the script at `https://ubuntu.bigbluebutton.org/bbb-install.sh` so you can use `wget` to install BigBlueButton 2.0 with a single command on any server that meets the above requirements.

## Intallation Video

We put together this [YouTube Video](https://youtu.be/D1iYEwxzk0M) that shows using `bbb-install.sh` to setup a BigBlueButton server with all the above options.  For the video, we used a virtual server from Digital Ocean.

## Usage

To install BigBlueButton on a Ubuntu 16.04 64-bit server with an external IP address:

~~~
wget -qO- https://ubuntu.bigbluebutton.org/bbb-install.sh | bash -s -- -v xenial-200 
~~~

That's it.  After about 10 minutes (depending on the server's internet connection) you'll have the latest build of BigBlueButton 2.0 running and ready to use.  The installation should finish with the following message:

~~~
** Potential problems described below **

......
# Warning: The API demos are installed and accessible from:
#
#    http://xxx.xxx.xxx.xxx/demo/demo1.jsp
#
# These API demos allow anyone to access your server without authentication
# to create/manage meetings and recordings. They are for testing purposes only.
# If you are running a production system, remove them by running:
#
#    sudo apt-get purge bbb-demo
~~~

Notice since we didn't specify a hostname for the installation, the `bbb-install.sh` script configured BigBlueButton server to use the servers public IP address.  The script also installs the `bbb-demo` package, so you can immediately test out the install.  Since the server does not have a SSL certificate, use FireFox (which at the time of this writing does not require SSL to use WebRTC audio).

If you want to remove the API demos, do the command

~~~
sudo apt-get purge bbb-demo
~~~

If you want to use this server with a front-end, such as Moodle, you can get the server's URL and shared secret with the command `sudo bbb-conf --secret`.

~~~
# bbb-conf --secret

       URL: http://xxx.xxx.xxx.xxx4/bigbluebutton/
    Secret: yyy

      Link to the API-Mate:
      http://mconf.github.io/api-mate/#server=http://xxx.xxx.xxx.xxx/bigbluebutton/&sharedSecret=yyy
~~~

While the core BigBlueButton server can run using just an IP address, you really want to have the server configured with a SSL certificate.  

Let's say you have configured the domain name `bbb.my-server.com` to resolve to the public IP address of your server.  That is, the command `dig bbb.my-server.com @8.8.8.8` resolves to the IP address of your server.  And you have a valid e-mail address, such as `info@my-server.com`, to receive updates from Let's Encrypt.  Then, with these two pieces of information, you can use `bbb-install.sh` to configure BigBlueButton server with an SSL certificate with a single command.

~~~
wget -qO- https://ubuntu.bigbluebutton.org/bbb-install.sh | bash -s -- -v xenial-200 -s bbb.my-server.com -e info@my-server.com
~~~

The `bbb-install.sh` script will also install a cron job for you to automatically renew the Let's Encrypte certifcate so it doesn't expire. 

Later on, you can install the latest developer build of the [HTML5 client](http://docs.bigbluebutton.org/html/html5-overview.html)  with the `-t` option (the HTML5 client needs SSL installed on the server).

~~~
wget -qO- https://ubuntu.bigbluebutton.org/bbb-install.sh | bash -s -- -v xenial-200 -t
~~~

After the HTML5 client is installed, you can use an Android or iOS (iOS 11+) phone/tablet to access your BigBlueButton server at `https://bbb.my-server.com/demo/demo1.jsp` and join using the HTML5 client.  BigBlueButton will detect the mobile browser and automatically load the HTML5 client instead of the default Flash client.  Note: the HTML5 client is under [active development](http://docs.bigbluebutton.org/html/html5-overview.html) and is not ready (yet) for production.

If you want a more sophisticated front-end to your BigBlueButton server, you can install [GreenLight](http://docs.bigbluebutton.org/install/green-light.html) with the `-g` option (GreenLight needs SSL installed on the server).

~~~
wget -qO- https://ubuntu.bigbluebutton.org/bbb-install.sh | bash -s -- -v xenial-200 -g
~~~

You can go to `https://<hostname>/` (where <hostname> is the host name for your BigBlueButton server) to launch the GreenLight interface.  To give users the ability to create and manage recorded meetings, See the GreenLight documentation for [setting up OAuth2 authentication](http://docs.bigbluebutton.org/install/green-light.html#6-configure-oauth2-optional).

Lastly, you could do all the above with a single command:

~~~
wget -qO- https://ubuntu.bigbluebutton.org/bbb-install.sh | bash -s -- -v xenial-200 -s bbb.my-server.com -e info@my-server.com -t -g
~~~

# Troubleshooting and Feedback

If you have feedback on the script, or need help using it, please post to the [BigBlueButton Setup](https://bigbluebutton.org/support/community/) mailing list and we'll help you there.

If you encouner an error with this script, please open an [issue](https://github.com/bigbluebutton/bbb-install/issues) and provide steps to reproduce the issue.


# Limitations

This script has the following limitations:

  * It will not configure your firewall (hence the requirement that your server have a single public IP address)
  * Currently, HTML5 client does not launch from GreenLight

