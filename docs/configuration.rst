.. _configuration:

Configuration
=============

Introduction
------------

The configuration file allows users to customize and control the behavior of the :ref:`cgr-engine`. This section provides detailed information on how to structure and use the configuration.

Format & Structure
------------------

Configuration files are organized into sections, making them easily splitable for better management and readability. Even though the format of the files is JSON, it also allows commented lines starting with \\\\.

.. note::
   Configuration can be split into any number of *.json* files or directories. The :ref:`cgr-engine` loads these files recursively and in alphabetical order from the configuration folder.

Loading & Reloading
-------------------

Configuration files can be loaded from local folders or remotely via HTTP. They are loaded at startup and can be reloaded at runtime using designated APIs. Users can either reload the configuration (from the same or from a different path) or overwrite it (by sending a JSON BLOB via API to the engine).

.. note::
   Remote reloading from an HTTP server is also supported.

Default Configuration
----------------------

Below is the hardcoded default configuration file for the :ref:`cgr-engine`. 

.. container:: toggle

   .. literalinclude:: ../data/conf/cgrates/cgrates.json
      :language: javascript
      :linenos:


Conclusion
----------

Understanding and effectively using the configuration file is essential for optimizing the performance and behavior of the :ref:`cgr-engine`. For additional assistance or information, refer to the official documentation and support channels.