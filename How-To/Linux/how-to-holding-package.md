# Holding packages with dpkg, apt

There are three ways of holding back packages: with dpkg, apt, Synaptic Package Manager.

* **dpkg**

Put a package on hold :

`echo "<package-name> hold" | sudo dpkg --set-selections`

Remove the hold :

`echo "<package-name> install" | sudo dpkg --set-selections`

Display the status of your packages :

`dpkg --get-selections`

Display the status of a single package :

`dpkg --get-selections | grep "<package-name>"`

* **apt**

Hold a package :

`sudo apt-mark hold <package-name>`

Remove the hold :

`sudo apt-mark unhold <package-name>`

* **Synaptic Package Manager**

Go to ***Synaptic Package Manager*** (System > Administration > Synaptic Package Manager).

Click the search button and type the package name.

When you find the package, select it and go to the ***Package*** menu and select ***Lock Version***.

>That package will now not show in the update manager and will not be updated.
