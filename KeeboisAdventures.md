# Getting Ready for OSGi #

## Setting up Eclipse ##

### Download Eclipse (Indigo 3.7) ###

http://www.eclipse.org/indigo/

Note: First; DO NOT USE THE SOFTWARE CENTER TO INSTALL ECLIPSE. Eclipse's update mechanism conflicts with Ubuntu's. 
Any update done via Eclipse's Equinox p2 repos will castrate the living hell out of your Eclipse setup. YOU HAVE BEEN 
WARNED. Second; if you wish to use EIK (Eclipse Integration for Karaf) use Indigo (3.7), otherwise, you're free to 
use any version of Eclipse.

Installation (For those not man enough)

Untar...

	tar xzf eclipse-${SEMANTIC-VERSIONING-BROS}.tar.gz

Move Eclipse to /opt/ (much cleaner this way)

	mv eclipse /opt/
	sudo chown -R root:root eclipse
	sudo chmod -R +r eclipse

Make an executable for Eclipse

	sudo touch /usr/bin/eclipse
	sudo chmod 755 /usr/bin/eclipse
	sudo vi /usr/bin/eclipse	

And paste this

	#!/bin/sh
	#export MOZILLA_FIVE_HOME="/usr/lib/mozilla/"
	export ECLIPSE_HOME="/opt/eclipse"

	$ECLIPSE_HOME/eclipse $*

DO NOT OMMIT THE MOZZILA_FIVE_HOME LINE, it's important.

Creating a Gnome menu item (For Gnome-based distros)

	sudo vi /usr/share/applications/eclipse.desktop

and paste this:

	[Desktop Entry]
	Encoding=UTF-8
	Name=Eclipse
	Comment=Eclipse IDE
	Exec=eclipse
	Icon=/opt/eclipse/icon.xpm
	Terminal=false
	Type=Application
	Categories=GNOME;Application;Development;
	StartupNotify=true	

Happy Eclipse-ing!

	/opt/eclipse/eclipse -clean &

## Setting Up Karaf ##

Just freakin' download and untar

http://karaf.apache.org/index/community/download.html
http://karaf.apache.org/manual/latest-2.2.x/users-guide/installation.html

Setting Up EIK (Eclipse Integration for Karaf)

Real men use IDEs, and that's why I use Eclipse (sorry NetBeans)

Download EIK here:

http://code.google.com/a/eclipselabs.org/p/eik/downloads/list

Note: Make sure you are using the right version of Eclipse. Juno (4.2) is not yet supported. After downloading
the jar file, install it via Help > Install New Software > Add New Archive. (If you're using Indigo 3.7, download
the latest SNAPSHOT build, I've confirmed that the 0.5.3 version doesn't work with 3.7)

Note: Again, you have to configure your Eclipse if you are behind a proxy. I'll add a short tutorial next revision.

"That's when I told her I love you girl but I'm not the answer to the questions that you still have." - Savior, Rise Against

Open Preferences and type in "OSGi Frameworks", check Apache Karaf on Equinox. Once done, open the Karaf Perspective, 
select Create New > Apache Karaf Installation...  you know what, just follow the tutorial:

http://code.google.com/a/eclipselabs.org/p/eik/wiki/UsingEIK

And that's it; now you can run Karaf inside Eclipse. (It's not in any way better than running Karaf via the terminal, but hey, it's free
and open source.)

Creating a Pax Wicket application

Create a wicket application via Apache Wicket Quickstart - http://wicket.apache.org/start/quickstart.html

Note: Make sure you select the proper wicket version (in our case, 1.5.7 since 6.0.3-BETA is not yet supported). 
Also, dubstep has some sick tunes to listen to while you setup Pax Wicket and all the other crap.

After maven is done creating the archetype, edit the pom.xml and add the following dependencies:

<!-- Pax Wicket Core -->
<dependency>
<groupId>org.ops4j.pax.wicket</groupId>
<artifactId>org.ops4j.pax.wicket.service</artifactId>
<version>1.0.1</version>
</dependency>
<!-- Pax Wicket Test Utilities -->
<dependency>
<groupId>org.ops4j.pax.wicket</groupId>
<artifactId>org.ops4j.pax.wicket.test</artifactId>
<version>1.0.1</version>
</dependency>

PAX WICKET DEPENDENCIES BROS, das what I'm tawkin' bouuut.

Note: If your working behind a very annoying proxy, make sure you configure maven to point to your proxy Maven 
settings (Ubuntu 12.04) can be found in 

/etc/maven2/settings.xml 

Apache is kind enough to give us a template to edit.

Once you've finished configuring your maven settings, configure your Karaf mvn settings to point to your mvn 
settings (settings-ception).  You can find it in:

${YOUR-FREAKIN'-KARAF-DIRECTORY}/etc/org.ops4j.pax.url.mvn.cfg (edit it with Vi if you're a real man)

I spent an hour trying to figure out why I couldn't do the next step. Proxies are truly productivity killers.

Right, so now time to add the Pax Wicket repo to Karaf, start your Karaf ${YOUR-FREAKIN'-KARAF-DIRECTORY}/bin/karaf and add the following:

features:addurl mvn:org.ops4j.pax.wicket/features/1.0.1/xml/features
features:addurl mvn:org.ops4j.pax.wicket.samples/features/1.0.1/xml/features

Note: I'm using Pax Wicket 1.0.1 (which uses Wicket 1.5.7) check http://team.ops4j.org/wiki/display/paxwicket/Download 
for the appropriate version. Unfortunately, Wicket 6.x.x-Beta aint supported yet bros.

To check if you've properly added the repos to your Karaf setup, run the command 

features:list 

should give you these buggers:

... some other features and stuff
[uninstalled] [1.0.1] wicket-samples-base                       pax-wicket-samples-features-1.0.1 
[uninstalled] [1.0.1] wicket-samples-plain-simple               pax-wicket-samples-features-1.0.1 
[uninstalled] [1.0.1] wicket-samples-blueprint-simple           pax-wicket-samples-features-1.0.1 
[uninstalled] [1.0.1] wicket-samples-blueprint-wicketproperties pax-wicket-samples-features-1.0.1 
[uninstalled] [1.0.1] wicket-samples-blueprint-mount            pax-wicket-samples-features-1.0.1 
[uninstalled] [1.0.1] wicket-samples-blueprint-filter           pax-wicket-samples-features-1.0.1 
[uninstalled] [1.0.1] wicket-samples-blueprint-injection-simple pax-wicket-samples-features-1.0.1 
[uninstalled] [1.0.1] wicket-samples-spring-simple              pax-wicket-samples-features-1.0.1 
[uninstalled] [1.0.1] wicket-samples-spring-injection-simple    pax-wicket-samples-features-1.0.1 
[uninstalled] [1.0.1] wicket                                    pax-wicket-features-1.0.1         
[uninstalled] [1.0.1] wicket-spring                             pax-wicket-features-1.0.1         

Got it bros? Good.

BREAK-FREAKIN'-TIME BROS

Here's a rundown on what we've done:
1.) We created a Pax Wicket application
2.) We added Pax Wicket stuff to our Karaf setup
3.) We listened to dubstep and became real men

Aight, let's bounce:

Install the wicket feature by running the command:

features:install wicket
features:install wicket-spring

Now let's go back to our Wicket application


