Tutorial
========

In this tutorial we will get CGRateS up and running on a Debian 11 (Bullseye) virtual machine with any of the following communication platforms: FreeSWITCH, Asterisk, Kamailio, OpenSIPS (you will have to choose one of these). 
Then you will use a SIP UA of your choice (Zoiper in our case) to 


We will start an instance of CGRateS with the following subsystems enabled:

The setup will consist of setting 6 accounts, as follows:
-1001-prepaid 
-1002-postpaid
-1003-pseudoprepaid 
-1004-rated 
-1006-prepaid 
-1007-rated

.. tabs::

   .. tab:: FreeSWITCH

      Installation here:
      ::

        sudo apt install freeswitch

   .. tab:: Asterisk

      Install asterisk:
      ::

        sudo apt install asterisk

   .. tab:: Kamailio

      Install kama:
      ::

        sudo apt install kamailio

   .. tab:: OpenSIPS

      Installation for this:
      ::

        sudo apt install opensips