Tutorial
========

In this tutorial, on a Debian 11 (Bullseye) virtual machine, we will do the following together:
1. Set up a communication platform of your choice. We support the following options:
   -  FreeSWITCH
   -  Asterisk
   -  Kamailio
   -  OpenSIPS
2. Start a CGRateS instance with the corresponding agent configured. What we call agents are basically components within CGRateS that manage the communication between CGRateS and the communication platforms.
3. Configure user accounts as follows:
   -1001 -  prepaid 
   -1002 -  postpaid
   -1003 -  pseudoprepaid 
   -1004 -  rated 
   -1006 -  prepaid 
   -1007 -  rated
4. Add balance to the accounts that we will use.
5. Use Zoiper to make calls between the accounts we configured then check the updated balances. Feel free to use any other SIP UA.
6. Set up fraud detection.


Software Installation:
----------------------

*CGRateS* already has a section within this documentation regarding installation. It can be found :ref:`here<installation>`.

Regarding the communication platforms, click on the tab corresponding to the choice you made and follow the steps in order to set up:

.. tabs::

   .. group-tab:: FreeSWITCH

      For detailed information on installing FreeSWITCH on Debian, please refer to its official `installation wiki <https://developer.signalwire.com/freeswitch/FreeSWITCH-Explained/Installation/Linux/Debian_67240088/>`_.

      Before installing FreeSWITCH, you need to authenticate by creating a SignalWire Personal Access Token. To generate your personal token, follow the instructions in the `SignalWire official wiki on creating a personal token <https://developer.signalwire.com/freeswitch/freeswitch-explained/installation/howto-create-a-signalwire-personal-access-token_67240087/>`_.

      To install FreeSWITCH and configure it, we have chosen the simplest method using *vanilla* packages.

      .. code-block:: bash

         TOKEN=YOURSIGNALWIRETOKEN # Insert your SignalWire Personal Access Token here
         apt-get update && apt-get install -y gnupg2 wget lsb-release
         wget --http-user=signalwire --http-password=$TOKEN -O /usr/share/keyrings/signalwire-freeswitch-repo.gpg https://freeswitch.signalwire.com/repo/deb/debian-release/signalwire-freeswitch-repo.gpg
         echo "machine freeswitch.signalwire.com login signalwire password $TOKEN" > /etc/apt/auth.conf
         chmod 600 /etc/apt/auth.conf
         echo "deb [signed-by=/usr/share/keyrings/signalwire-freeswitch-repo.gpg] https://freeswitch.signalwire.com/repo/deb/debian-release/ `lsb_release -sc` main" > /etc/apt/sources.list.d/freeswitch.list
         echo "deb-src [signed-by=/usr/share/keyrings/signalwire-freeswitch-repo.gpg] https://freeswitch.signalwire.com/repo/deb/debian-release/ `lsb_release -sc` main" >> /etc/apt/sources.list.d/freeswitch.list

         # If /etc/freeswitch does not exist, the standard vanilla configuration is deployed
         apt-get update && apt-get install -y freeswitch-meta-all

   .. group-tab:: Asterisk

      To install Asterisk, follow these steps:

      .. code-block:: bash

         # Install the necessary dependencies
         sudo apt-get install -y build-essential libasound2-dev autoconf \
                              openssl libssl-dev libxml2-dev \
                              libncurses5-dev uuid-dev sqlite3 \
                              libsqlite3-dev pkg-config libedit-dev \
                              libjansson-dev

         # Download Asterisk
         wget https://downloads.asterisk.org/pub/telephony/asterisk/asterisk-20-current.tar.gz -P /tmp

         # Extract the downloaded archive
         sudo tar -xzvf /tmp/asterisk-20-current.tar.gz -C /usr/src

         # Change the working directory to the extracted Asterisk source
         cd /usr/src/asterisk-20*/

         # Compile and install Asterisk
         sudo ./configure --with-jansson-bundled
         sudo make menuselect.makeopts
         sudo make
         sudo make install
         sudo make samples
         sudo make config
         sudo ldconfig

         # Create the Asterisk system user
         sudo adduser --quiet --system --group --disabled-password --shell /bin/false --gecos "Asterisk" asterisk

   .. group-tab:: Kamailio

      Kamailio can be installed using the commands below, as documented in the `Kamailio Debian Installation Guide <https://kamailio.org/docs/tutorials/devel/kamailio-install-guide-deb/>`_.

      .. code-block:: bash

         wget -O- http://deb.kamailio.org/kamailiodebkey.gpg | sudo apt-key add -
         echo "deb http://deb.kamailio.org/kamailio56 bullseye main" > /etc/apt/sources.list.d/kamailio.list
         apt-get update
         apt-get install kamailio kamailio-extra-modules kamailio-json-modules 

   .. group-tab:: OpenSIPS

      We got OpenSIPS_ installed via following commands:
      
      .. code-block:: bash

         apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 049AD65B
         echo "deb https://apt.opensips.org buster 3.3-releases" >/etc/apt/sources.list.d/opensips.list
         echo "deb https://apt.opensips.org buster cli-nightly" >/etc/apt/sources.list.d/opensips-cli.list
         apt-get update
         sudo apt-get install opensips opensips-mysql-module opensips-cgrates-module opensips-cli


.. tabs::

   .. group-tab:: FreeSWITCH

      Installation here:
      ::

        sudo apt install freeswitch

   .. group-tab:: Asterisk

      Install asterisk:
      ::

        sudo apt install asterisk

   .. group-tab:: Kamailio

      Install kama:
      ::

        sudo apt install kamailio

   .. group-tab:: OpenSIPS

      Installation for this:
      ::

        sudo apt install opensips


