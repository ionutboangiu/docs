.. _Redis: https://redis.io/
.. _MySQL: https://dev.mysql.com/
.. _PostgreSQL: https://www.postgresql.org/
.. _MongoDB: https://www.mongodb.com/

.. _installation:

Installation
============

CGRateS can be installed either via packages or through an automated Go source installation. We recommend the latter for advanced users familiar with Go programming, and package installations for those not wanting to engage in the code building process.

Using Packages
--------------

The method of installation may vary based on the distribution package:

Debian 
^^^^^^

There are two primary ways to install the maintained packages:

Aptitude Repository 
~~~~~~~~~~~~~~~~~~~

You can install CGRateS using the following commands:

::

    # Install dependencies
    sudo apt-get install wget gnupg -y
    # Download GPG Key
    wget https://apt.cgrates.org/apt.cgrates.org.gpg.key -O apt.cgrates.org.gpg.key
    # Verify the key before proceeding
    gpg --verify apt.cgrates.org.gpg.key
    # Move the key to the trusted area
    sudo mv apt.cgrates.org.gpg.key /etc/apt/trusted.gpg.d/apt.cgrates.org.asc
    # Add the repository to the apt sources list
    echo "deb http://apt.cgrates.org/debian/ v0.10 main" | sudo tee /etc/apt/sources.list.d/cgrates.list
    # Update system repository
    sudo apt-get update -y
    # Install CGRateS
    sudo apt-get install cgrates -y

Upon the completion of the installation, it is necessary to perform the :ref:`post-install <post_install>` steps to set up CGRateS properly and prepare it to run. After completing the *post-install* process, CGRateS will be configured in **/etc/cgrates/cgrates.json** and enabled in **/etc/default/cgrates**.

Manual Installation of .deb Package from Archive Server
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To manually install the .deb package, use the following commands:

::

    wget http://pkg.cgrates.org/deb/v0.10/cgrates_current_amd64.deb
    dpkg -i cgrates_current_amd64.deb

Note: You can find a complete archive of CGRateS packages at http://pkg.cgrates.org/deb/.

Redhat/Fedora/CentOS
^^^^^^^^^^^^^^^^^^^^

There are two primary ways of installing the maintained packages:

YUM Repository
~~~~~~~~~~~~~~

To install CGRateS via YUM, execute the following commands:

::

    sudo tee -a /etc/yum.repos.d/cgrates.repo > /dev/null <<EOT
    [cgrates]
    name=CGRateS
    baseurl=http://yum.cgrates.org/yum/v0.10/
    enabled=1
    gpgcheck=1
    gpgkey=https://yum.cgrates.org/yum.cgrates.org.gpg.key
    EOT
    sudo yum update
    sudo yum install cgrates

After the installation, perform the :ref:`post-install <post_install>` section to have CGRateS set up and ready. Once *post-install* steps are completed, CGRateS will be configured in **/etc/cgrates/cgrates.json** and enabled in **/etc/default/cgrates**.

Manual Installation of .rpm Package from Archive Server
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To manually install the .rpm package, use the following command:

::

    sudo rpm -i http://pkg.cgrates.org/rpm/v0.10/cgrates_current.rpm

Note: An entire archive of CGRateS packages can be found at http://pkg.cgrates.org/rpm/.

Using Source
------------

For developing CGRateS and switching between its versions, we utilize the **go mods feature** introduced in Go 1.13.

.. _InstallGO:

Install Go Lang
^^^^^^^^^^^^^^^

Firstly, we need to set up Go Lang on our OS. Feel free to download 
the latest Go binary release from https://golang.org/dl/
In this tutorial, we will install Go 1.17.5

::

   sudo rm -rf /usr/local/go
   cd /tmp
   wget https://go.dev/dl/go1.17.5.linux-amd64.tar.gz
   sudo tar -xvf go1.17.5.linux-amd64.tar.gz -C /usr/local/
   export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin

Build CGRateS from Source
^^^^^^^^^^^^^^^^^^^^^^^^^

Configure the project with the following commands:

::

   go get github.com/cgrates/cgrates
   cd $HOME/go/src/github.com/cgrates/cgrates
   ./build.sh

Create Debian / Ubuntu Packages from Source
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

After compiling the source code, you can create the .deb packages for your Debian-based OS. First, let's install some dependencies: 

::

   sudo apt-get install build-essential fakeroot dh-systemd

We are now ready to create the system package. Before creation, we ensure to delete the old one first.

::

   cd $HOME/go/src/github.com/cgrates/cgrates/packages
   rm -rf $HOME/go/src/github.com/cgrates/*.deb
   make deb

After a while, despite some possible console warnings, your CGRateS package will be ready.

Install Custom Debian / Ubuntu Package
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

   cd $HOME/go/src/github.com/cgrates
   sudo dpkg -i cgrates_*.deb

Generate RPM Packages from Source
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Prerequisites:
 * :ref:`Install Golang <InstallGO>`
 * Git

   ::

    sudo yum install git

 * Build dependencies

   ::

    sudo yum install -y rpm-build
    sudo yum install -y dh-systemd

Building the RPMs:

::

   cd $HOME/go/src/github.com/cgrates/cgrates/packages
   make rpm

This will generate the RPMs in the directory $HOME/go/src/github.com/cgrates/cgrates/packages

