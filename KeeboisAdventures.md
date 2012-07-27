# Getting Ready for OSGi #

## Setting up Eclipse ## 
### Download Eclipse (Indigo 3.7) ###

http://www.eclipse.org/indigo/

*Note: First; DO NOT USE THE SOFTWARE CENTER TO INSTALL ECLIPSE.* Eclipse's update mechanism conflicts with Ubuntu's. 
Any update done via Eclipse's Equinox p2 repos will castrate the living hell out of your Eclipse setup. YOU HAVE BEEN 
WARNED. Second; if you wish to use EIK (Eclipse Integration for Karaf) use Indigo (3.7), otherwise, feel free to 
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

*DO NOT OMIT THE MOZZILA_FIVE_HOME LINE*, it's important.

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

[Download Karaf](http://karaf.apache.org/index/community/download.html)
<br/>
[Karaf installation](http://karaf.apache.org/manual/latest-2.2.x/users-guide/installation.html)
<br/>

## Setting Up EIK (Eclipse Integration for Karaf) ##

Real men use IDEs, and that's why I use Eclipse (sorry NetBeans)

Download EIK here:

[EIK @ Googlecode](http://code.google.com/a/eclipselabs.org/p/eik/downloads/list)

*Note: Make sure you are using the right version of Eclipse.* Juno (4.2) is not yet supported. After downloading
the jar file, install it via Help > Install New Software > Add New Archive. (If you're using Indigo 3.7, download
the latest SNAPSHOT build, I've confirmed that the EIK 0.5.3 version doesn't work with Eclipse 3.7)

*Note: Again, you have to configure your Eclipse if you are behind a proxy.* I'll add a short tutorial next revision.

> "That's when I told her I love you girl but I'm not the answer to the questions that you still have."
> **Savior, Rise Against**

Open Preferences and type in "OSGi Frameworks", check Apache Karaf on Equinox. Once done, open the Karaf Perspective, 
select Create New > Apache Karaf Installation...  you know what, just follow the tutorial:

[Setting up EIK](http://code.google.com/a/eclipselabs.org/p/eik/wiki/UsingEIK)

And that's it; now you can run Karaf inside Eclipse. (It's not in any way better than running Karaf via the terminal, but hey, it's free
and open source.)

# Creating a Pax Wicket (Blueprint) application #
## Generating a Blueprint archetype ##

Okay, since there's no direct way of generating a Pax Wicket (Blueprint) archetype, let's do it how real men should do it.
First, generate a Blueprint skeleton (archetype) via Maven:

[List of Archetypes for real men](http://karaf.apache.org/manual/2.2.8/developers-guide/archetypes.html)

		mvn archetype:generate \
		    -DarchetypeGroupId=org.apache.karaf.archetypes \
		    -DarchetypeArtifactId=karaf-blueprint-archetype \
		    -DarchetypeVersion=2.2.8 \
		    -DgroupId=com.mycompany \
		    -DartifactId=com.mycompany.blueprint \
		    -Dversion=1.0-SNAPSHOT \
		    -Dpackage=com.mycompany.blueprint

Once done, edit the pom.xml to add the following dependencies:

		<!-- OSGi Core -->
		<dependency>
			<groupId>org.osgi</groupId>
			<artifactId>org.osgi.core</artifactId>
			<version>4.2.0</version>
		</dependency>
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

You also need to import the following packages via the bundle plugin (inside the pom):

		<Import-Package>
			org.apache.wicket,
			org.apache.wicket.util,
			org.apache.wicket.event,
			org.apache.wicket.page,
			org.apache.wicket.request.resource,
			org.apache.wicket.request.mapper,
			org.apache.wicket.request.cycle,
			org.apache.wicket.markup.html,
			org.apache.wicket.markup.html.basic,
			org.apache.wicket.protocol.http,
			org.apache.wicket.request.http,
			org.ops4j.pax.wicket.api,
			org.ops4j.pax.wicket.util,
			org.osgi.framework,
		        org.ops4j.pax.wicket.util.proxy,
			net.sf.cglib.proxy;version="[2,3)",
			net.sf.cglib.core;version="[2,3)",
			net.sf.cglib.reflect;version="[2,3)",
			javax.servlet,
			org.apache.wicket.settings,
			org.apache.wicket.session,
			org.apache.wicket.util.file,
			org.apache.wicket.request,
			javax.servlet.http,
			org.apache.wicket.ajax,
			org.osgi.service.blueprint
		</Import-Package>

I'm kind (and ridiculously handsome) enough to provide you a sample pom.xml, here ya go:

		<?xml version="1.0" encoding="UTF-8"?>
		<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

		    <modelVersion>4.0.0</modelVersion>

		    <groupId>com.kevin.realman</groupId>
		    <artifactId>com.kevin.realman.sample</artifactId>
		    <version>1.0-SNAPSHOT</version>
		    <packaging>bundle</packaging>

		    <name>A Manly Application</name>
		    <description>For real men who like to code</description>
		    
		    <properties>
			    <mvn.build.java.version>1.7</mvn.build.java.version> 
		    </properties>
			
		    <dependencies>
			    <!-- OSGi Core -->
			    <dependency>
				<groupId>org.osgi</groupId>
				<artifactId>org.osgi.core</artifactId>
				<version>4.2.0</version>
			    </dependency>
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
		    </dependencies>

		    <build>
			<plugins>
			    <plugin>
				<groupId>org.apache.felix</groupId>
				<artifactId>maven-bundle-plugin</artifactId>
				<version>2.3.7</version>
				<extensions>true</extensions>
				<configuration>
				    <instructions>
					<Bundle-SymbolicName>${project.artifactId}</Bundle-SymbolicName>
					<Bundle-Version>${project.version}</Bundle-Version>
					<Export-Package>
					    com.kevin.realman.sample*;version=${project.version}
					</Export-Package>
					<Import-Package>
					    org.apache.wicket,
							    org.apache.wicket.util,
							    org.apache.wicket.event,
							    org.apache.wicket.page,
							    org.apache.wicket.request.resource,
							    org.apache.wicket.request.mapper,
							    org.apache.wicket.request.cycle,
							    org.apache.wicket.markup.html,
							    org.apache.wicket.markup.html.basic,
							    org.apache.wicket.protocol.http,
							    org.apache.wicket.request.http,
							    org.ops4j.pax.wicket.api,
							    org.ops4j.pax.wicket.util,
							    org.osgi.framework,
						            org.ops4j.pax.wicket.util.proxy,
							    net.sf.cglib.proxy;version="[2,3)",
							    net.sf.cglib.core;version="[2,3)",
							    net.sf.cglib.reflect;version="[2,3)",
							    javax.servlet,
							    org.apache.wicket.settings,
							    org.apache.wicket.session,
							    org.apache.wicket.util.file,
							    org.apache.wicket.request,
							    javax.servlet.http,
							    org.apache.wicket.ajax,
							    org.osgi.service.blueprint
					</Import-Package>
				    </instructions>
				</configuration>
			    </plugin>
			    <plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>2.5.1</version>
				<configuration>
				    <source>${mvn.build.java.version}</source>
				    <target>${mvn.build.java.version}</target>
				    <showDeprecation>true</showDeprecation>
				    <showWarnings>true</showWarnings>
				</configuration>
			    </plugin>
			</plugins>
		    </build>
		</project>

Execute:

		mvn clean install

It'll create an archetype (duh), don't judge me. But it ain't a Wicket app, so let's Wicketify it!

Assuming nothing goes wrong, we can now proceed to coding. 


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

Note: I'm using Pax Wicket 1.0.1 (which uses Wicket 1.5.7) check [the download site](http://team.ops4j.org/wiki/display/paxwicket/Download)
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

Here's a rundown on what we've done

	1.   We created a Pax Wicket application
	2.   We added Pax Wicket stuff to our Karaf setup
	3.   We listened to dubstep and became real men

Aight, let's bounce:

Install the wicket feature by running the command:

		features:install wicket
		features:install wicket-spring

Now let's go back to our Wicket application

## TO BE CONTINUED ##
