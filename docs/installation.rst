.. _Redis: https://redis.io/
.. _MySQL: https://dev.mysql.com/
.. _PostgreSQL: https://www.postgresql.org/
.. _MongoDB: https://www.mongodb.com/

.. _installation:

Installation
============

CGRateS can be installed either via packages or through an automated Go source installation. We recommend the latter for advanced users familiar with Go programming, and package installations for those not wanting to engage in the code building process.

Upon the completion of the installation, it is necessary to perform the :ref:`post-install configuration <post_install>` steps to set up CGRateS properly and prepare it to run. After completing the *post-install* process, CGRateS will be configured in **/etc/cgrates/cgrates.json** and enabled in **/etc/default/cgrates**.

Install using Packages
----------------------

The method of installation may vary based on the distribution package:

On Debian or Debian-based Distributions 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can add the CGRateS repository to your system's sources list:

::

   # Install dependencies
   sudo apt-get install wget gnupg -y

   # Download and Move GPG Key to trusted area
   sudo wget https://apt.cgrates.org/apt.cgrates.org.gpg.key -O /etc/apt/trusted.gpg.d/apt.cgrates.org.asc

   # Add the repository to the apt sources list
   echo "deb http://apt.cgrates.org/debian/ v0.10 main" | sudo tee /etc/apt/sources.list.d/cgrates.list

   # Update system repository and install CGRateS
   sudo apt-get update -y
   sudo apt-get install cgrates -y


Or you could manually install the .deb package:

::

   wget http://pkg.cgrates.org/deb/v0.10/cgrates_current_amd64.deb
   sudo apt install ./cgrates_current_amd64.deb

Note: You can find a complete archive of CGRateS packages at http://pkg.cgrates.org/deb/.

On Redhat-based Distributions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Same as before you can add the CGRateS repository to your system's sources list:

::

   sudo dnf install -y dnf-plugins-core
   sudo dnf copr enable cgrates/master
   sudo dnf install -y cgrates

To install an earlier version:

::

   sudo dnf install -y cgrates-<version>.x86_64

Note: The entire archive of CGRateS rpm packages can be found at https://copr.fedorainfracloud.org/coprs/cgrates/v0.10/packages/.

Building from source
--------------------

Prerequisites:

- Git

::

   sudo apt install git

- Go - refer to the official Go installation docs: https://go.dev/doc/install or run the following commands to install the latest Go version at the time of writing this documentation:

::

   sudo apt install wget -y
   sudo rm -rf /usr/local/go
   cd /tmp
   wget https://go.dev/dl/go1.20.5.linux-amd64.tar.gz
   sudo tar -C /usr/local -xzf go1.20.5.linux-amd64.tar.gz
   export PATH=$PATH:/usr/local/go/bin

Configure the project with the following commands:

::

   mkdir -p ~/go/src/github.com/cgrates
   git clone https://github.com/cgrates/cgrates.git ~/go/src/github.com/cgrates/
   cd ~/go/src/github.com/cgrates/cgrates

   # Compile the binaries and move them to $GOPATH/bin
   ./build.sh

   # Create a symbolic link to the data folder
   ln -s ~/go/src/github.com/cgrates/cgrates/data /usr/share/cgrates

   # Make cgr-engine binary available system-wide
   ln -s ~/go/bin/cgr-engine /usr/local/bin/cgr-engine

   # Optional, but might also be useful
   ln -s ~/go/bin/cgr-loader /usr/local/bin/cgr-loader
   ln -s ~/go/bin/cgr-migrator /usr/local/bin/cgr-migrator
   ln -s ~/go/bin/cgr-console /usr/local/bin/cgr-console

Create your own Packages
^^^^^^^^^^^^^^^^^^^^^^^^

After compiling the source code, you can choose to build your own packages.

For Debian-based distos:
~~~~~~~~~~~~~~~~~~~~~~~~

::
   
   # Install dependencies
   sudo apt-get install build-essential fakeroot dh-systemd -y

   cd ~/go/src/github.com/cgrates/cgrates/packages

   # Delete the old ones, if any
   rm -rf ~/go/src/github.com/cgrates/*.deb

   make deb

There might be some console warnings, but they can safely be ignored.

To install it:

::

   cd ~/go/src/github.com/cgrates
   sudo apt install cgrates_*.deb

For Redhat-based distros:
~~~~~~~~~~~~~~~~~~~~~~~~~

::

   sudo apt-get install rpm
   cd ~/go/src/github.com/cgrates/cgrates
   export gitLastCommit=$(git rev-parse HEAD)
   export rpmTag=$(git log -1 --format=%ci | date +%Y%m%d%H%M%S)+$(git rev-parse --short HEAD)
   mkdir -p ~/cgr_build/{BUILD,RPMS,SOURCES,SPECS,SRPMS}
   wget -P ~/cgr_build/SOURCES https://github.com/cgrates/cgrates/archive/$gitLastCommit.tar.gz
   cp ~/go/src/github.com/cgrates/cgrates/packages/redhat_fedora/cgrates.spec ~/cgr_build/SPECS
   cd ~/cgr_build
   rpmbuild -bb --define "_topdir ~/cgr_build" SPECS/cgrates.spec


.. _post_install:

Post-install configuration
--------------------------


Database setup
^^^^^^^^^^^^^^

For its operation CGRateS uses **one or more** database types, depending on its nature, install and configuration being further necessary.

At present we support the following databases:

`Redis`_
  Can be used as :ref:`DataDB`.
  Optimized for real-time information access.
  Once installed there should be no special requirements in terms of setup since no schema is necessary.

`MySQL`_
  Can be used as :ref:`StorDB`.
  Optimized for CDR archiving and offline Tariff Plan versioning.
  Once MySQL is installed, CGRateS database needs to be set-up out of provided scripts. (example for the paths set-up by debian package)

::

   cd /usr/share/cgrates/storage/mysql/
   sudo ./setup_cgr_db.sh root CGRateS.org localhost

`PostgreSQL`_
  Can be used as :ref:`StorDB`.
  Optimized for CDR archiving and offline Tariff Plan versioning.
  Once PostgreSQL is installed, CGRateS database needs to be set-up out of provided scripts (example for the paths set-up by debian package).

::

   cd /usr/share/cgrates/storage/postgres/
   sudo ./setup_cgr_db.sh

`MongoDB`_
  Can be used as :ref:`DataDB` as well as :ref:`StorDB`.
  It is the first database that can be used to store all kinds of data stored from CGRateS from accounts, tariff plans to cdrs and logs.
  Once MongoDB is installed, CGRateS database needs to be set-up out of provided scripts (example for the paths set-up by debian package)

::

   cd /usr/share/cgrates/storage/mongo/
   sudo ./setup_cgr_db.sh


Set versions data
^^^^^^^^^^^^^^^^^

Once database setup is completed, we need to write the versions data. To do this, run migrator tool with the parameters specific to your database. 

Sample usage for MySQL: 
::

   cgr-migrator -stordb_passwd="CGRateS.org" -exec="*set_versions"

