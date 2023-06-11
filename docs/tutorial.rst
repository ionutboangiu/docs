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

Configuration:
--------------


Loading **CGRateS** Tariff Plans
--------------------------------

Before proceeding to this step, you should have **CGRateS** installed and
started with custom configuration, depending on the tutorial you have followed.

For our tutorial we load again prepared data out of shared folder, containing
following rules:

- Create the necessary timings (always, asap, peak, offpeak).
- Configure 3 destinations (1002, 1003 and 10 used as catch all rule).
- As rating we configure the following:

 - Rate id: *RT_10CNT* with connect fee of 20cents, 10cents per minute for the first 60s in 60s increments followed by 5cents per minute in 1s increments.
 - Rate id: *RT_20CNT* with connect fee of 40cents, 20cents per minute for the first 60s in 60s increments, followed by 10 cents per minute charged in 1s increments.
 - Rate id: *RT_40CNT* with connect fee of 80cents, 40cents per minute for the first 60s in 60s increments, follwed by 20cents per minute charged in 10s increments.
 - Rate id: *RT_1CNT* having no connect fee and a rate of 1 cent per minute, chargeable in 1 minute increments.
 - Rate id: *RT_1CNT_PER_SEC* having no connect fee and a rate of 1 cent per second, chargeable in 1 second increments.

- Accounting part will have following configured:

  - Create 3 accounts: 1001, 1002, 1003.
  - 1001, 1002 will receive 10units of **\*monetary** balance.


::

 cgr-loader -verbose -path=/usr/share/cgrates/tariffplans/tutorial

To verify that all actions successfully performed, we use following *cgr-console* commands:

- Make sure all our balances were topped-up:

 ::

  cgr-console 'accounts Tenant="cgrates.org" AccountIds=["1001"]'
  cgr-console 'accounts Tenant="cgrates.org" AccountIds=["1002"]'

- Query call costs so we can see our calls will have expected costs (final cost will result as sum of *ConnectFee* and *Cost* fields):

 ::
 
  cgr-console 'cost Category="call" Tenant="cgrates.org" Subject="1001" Destination="1002" AnswerTime="2014-08-04T13:00:00Z" Usage="20s"'
  cgr-console 'cost Category="call" Tenant="cgrates.org" Subject="1001" Destination="1002" AnswerTime="2014-08-04T13:00:00Z" Usage="1m25s"'
  cgr-console 'cost Category="call" Tenant="cgrates.org" Subject="1001" Destination="1003" AnswerTime="2014-08-04T13:00:00Z" Usage="20s"'


Test calls
----------


1001 -> 1002
~~~~~~~~~~~~

Since the user 1001 is marked as *prepaid* inside the telecom switch, calling between 1001 and 1002 should generate pre-auth and prepaid debits which can be checked with *get_account* command integrated within *cgr-console* tool. Charging will be done based on time of day as described in the tariff plan definition above.

*Note*: An important particularity to  note here is the ability of **CGRateS** SessionManager to refund units booked in advance (eg: if debit occurs every 10s and rate increments are set to 1s, the SessionManager will be smart enough to refund pre-booked credits for calls stoped in the middle of debit interval).

Check that 1001 balance is properly deducted, during the call, and moreover considering that general balance has priority over the shared one debits for this call should take place at first out of general balance.

::

 cgr-console 'accounts Tenant="cgrates.org" AccountIds=["1001"]'


1002 -> 1001
~~~~~~~~~~~~

The user 1002 is marked as *postpaid* inside the telecom switch hence his calls will be debited at the end of the call instead of during a call and his balance will be able to go on negative without influencing his new calls (no pre-auth).

To check that we had debits we use again console command, this time not during the call but at the end of it:

::

 cgr-console 'accounts Tenant="cgrates.org" AccountIds=["1002"]'


1001 -> 1003
~~~~~~~~~~~~
The user 1001 call user 1003 and after 12 seconds the call will be disconnected.

CDR Processing Across Different Platforms
-----------------------------------------

  - Generates a CDR event at the end of each call (FreeSWITCH via HTTP Post and Kamailio via evapi)
  - The event is directed towards the port configured inside cgrates.json due to the automatic handler registration built into the SessionS subsystem.
  - The event reaches CGRateS through the SessionS subsystem in close to real-time.
  - Once inside CGRateS, the event is instantly rated and ready for export.


CDR Exporting
-------------

Once the CDRs are mediated, they are available to be exported. One can use available RPC APIs for that or directly call exports from console:

::

 cgr-console 'cdrs_export CdrFormat="csv" ExportPath="/tmp"'


Fraud detection
---------------

Since we have configured some action triggers (more than 20 units of balance topped-up or less than 2 and more than 5 units spent on *FS_USERS* we should be notified over syslog when things like unexpected events happen, e.g.: fraud with more than 20 units topped-up). Most important is the monitor for 100 units topped-up which will also trigger an account disable together with killing it's calls if prepaid debits are used.

To verify this mechanism simply add some random units into one account's balance:

::

 cgr-console 'balance_set Tenant="cgrates.org" Account="1003" Direction="*out" Value=23'
 tail -f /var/log/syslog -n 20

 cgr-console 'balance_set Tenant="cgrates.org" Account="1001" Direction="*out" Value=101'
 tail -f /var/log/syslog -n 20

On the CDRs side we will be able to integrate CdrStats monitors as part of our Fraud Detection system (eg: the increase of average cost for 1001 and 1002 accounts will signal us abnormalities, hence we will be notified via syslog).

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


