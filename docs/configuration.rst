.. _configuration:

Configuration
=============

Introduction
------------

The configuration file, in *JSON* format, is a crucial component that allows users to customize and control the behavior of the :ref:`cgr-engine`. This document provides detailed information on the configuration's structure, usage, and best practices.

Format & Structure
------------------

Configuration files are organized into sections, making them easily splitable for better management and readability. Each section starts with a commented line (//*), providing a brief description or instruction.

.. hint::
   Configuration can be split into any number of *.json* files or directories. The :ref:`cgr-engine` loads these files recursively and in alphabetical order from the configuration folder.

Loading & Reloading
-------------------

Configuration files can be loaded from local folders or remotely via HTTP. They are loaded at startup and can be reloaded at runtime using designated APIs. Users can either *pull* the configuration (reload from path) or *push* it (send JSON BLOB via API to the engine).

.. hint::
   Remote reloading from an HTTP server is also supported.

Default Configuration
----------------------

Below is the hardcoded default configuration file for the :ref:`cgr-engine`. Click the button to expand and view the content.

.. container:: json-spoiler

   .. literalinclude:: ../data/conf/cgrates/cgrates.json
      :language: javascript
      :linenos:

.. note::
   You might need to add some custom CSS and JavaScript to handle the spoiler functionality. Refer to the Sphinx documentation for guidance on adding static files.

Conclusion
----------

Understanding and effectively using the configuration file is essential for optimizing the performance and behavior of the :ref:`cgr-engine`. For additional assistance or information, refer to the official documentation and support channels.