# PHP: Setup Development Environment (Holodeck) #

The following steps will illustrate how to setup a dev environment of the new WBE project (Holodeck) for PHP under a Vagrant VM on your local machine. This allows full editing of the web booking engine environment locally.

## Step 1: ##

Download and install the following:

- VirtualBox 4.2.8 from [https://www.virtualbox.org/wiki/Download_Old_Builds_4_2](https://www.virtualbox.org/wiki/Download_Old_Builds_4_2)
- Latest version of Vagrant from [http://downloads.vagrantup.com/](http://downloads.vagrantup.com/)
- Putty as SSH client from [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

**NB:** The version of VirtualBox is quite important as the VM toolset requires the right version of guest addition tools to get networking functional. The version of Putty and Vagrant is not so significant but latest would be the best. Build instructions have been tested with v1.2.3 of Vagrant but any 1.2.x version should be fine.

## Step 2: ##

Clone the PeakWeb Tools from Github: [https://github.com/peak-adventure-travel/tools.git](https://github.com/peak-adventure-travel/tools.git)

If you have already cloned it, make sure you do a pull/fetch to ensure you have latest version.
  
Check it out under folder name 'C:\Development\PeakWeb\tools' or equivalent.

Make sure you have checked out into 'develop' branch as 'master' may not be cutting edge.

## Step 3: ##

Copy the Vagrantfile from 'C:\Development\PeakWeb\tools\vagrant\trunk\Holodeck' (if you fetched git into the above location) and paste it to 'C:\Development\VMs\Holodeck' directory path (or where you wish to run your vagrantfile from, can be anywhere really)

Update the Vagrantfile to ensure that 'puppetModulePath' and 'puppetManifestsPath' point to the right locations to match where you checked out the PeakWeb Tools from Step 2.

## Step 4: ##

Open up command prompt and go to the location specified in Step 3, then fire up vagrant using command 'vagrant up'

This will create a virtual machine with the Holodeck runtime environment. (This will take a while to create VM â€“ also puppet provisioning can take forever for first install so be patient, approx 10-20 mins depending on machine power)

When the above is finished (you'll know everything is provisioned when it returns to command prompt), open putty and log into your new VM with IP 10.0.0.12 â€“ Username: vagrant and Password: vagrant

## Step 5: ##

To setup the codebase (run as user 'vagrant', not root):

	git clone https://username@password:github.com/peak-adventure-travel/holodeck.git /opt/projects/Holodeck/

You can also, if you prefer, use a local computer Github client or Sourcetree and clone the repo into the folder on the Samba share. This is found at \\\10.0.0.12\projects\Holodeck.

Then back in the Putty terminal:

	cd /opt/projects/Holodeck
	composer --dev install
	vendor/bin/regenwsdl --mode=uat
	chmod a+rwx -R app/logs
	chmod a+rwx -R app/cache/dev

Note the composer step may take a while depending on the internet connection.

Then setup your apache conf for the brand you wish to load

	sudo vim /etc/httpd/conf.d/holodeck-local.conf

Uncomment the line for 'setenv' next to the "SYMFONY\_\_BRAND\_\_NAME" you wish to use. You can also skip this part and use one of the brand virtualhosts configured in the apache config file.

Restart apache.

	sudo apachectl restart

## Step 6: ##

When you finish Step 5, update your hosts file in your machine which is located in â€˜C:\Windows\System32\drivers\etcâ€™ in Windows, or /etc/hosts for Mac and add these new entries:

	10.0.0.12 bookings.peakadventuretravel.dev
	10.0.0.12 holodeck.peregrineadventures.dev
	10.0.0.12 holodeck.exodus.dev

Then save the file.

**NB:** You may have to run Notepad as administrator to be sure you have permission to edit the file.

Open your browser and go to one of the below:

	http://bookings.peakadventuretravel.dev/en_AU/booking_engine/addDeparture/1371267
	http://holodeck.exodus.dev/en_AU/booking_engine/addDeparture/1371267
	http://holodeck.peregrineadventures.dev/en_AU/booking_engine/addDeparture/1269320

If it loads you are good to go!

**NB:** If you get a SOAP error, there may be old IP addresses in use under the /etc/hosts file in the VM. Comment out any entry in the last 5 or 6 lines that use any 192.168.* addresses, they are in DNS now. They should be fixed in latest puppet manifest files but this should be the only thing stopping it.

**NB:** If you get a cache or log permissions error, ensure you have set the right permissions on the app/cache and app/log folders so that apache can write to them (Commands in step 5 should resolve this)

## Step 7: ##

Since the files are hosted directly on the VM, you will need to figure out how to edit them via your PHPStorm install on your machine. The files are shared under the VM and the Samba install sets up a correct Netbios name. By going to:

	\\HOLODECK\projects\Holodeck

or if Netbios on your machine for some strange reason is not working:

	\\10.0.0.12\projects\Holodeck

You should be able to see your codebase over the shared folder. You can setup your PHPStorm project folder here and do everything the same as if the files were on your local filesystem. 

## General Notes ##

When you are done working with your VM, use 'vagrant halt' to shut it down. Your changes should persist so if you do 'vagrant up' your filesystem is still present after the reboot.

The only downside is you will NOT be able to view your codebase unless the VM is up, which is not true of the vagrant shared folder methods on your own machine.




