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

.. literalinclude:: ../data/conf/cgrates/cgrates.json
   :language: javascript
   :linenos:


Data Converters Configuration Tutorial
--------------------------------------

Data converters play a crucial role in manipulating and formatting data within the :ref:`cgr-engine`. This tutorial will guide you through the configuration of various data converters.

*strip Data Converter
~~~~~~~~~~~~~~~~~~~~~~

The `*strip` data converter is designed to strip a prefix, suffix, or both from a string. It is highly configurable, allowing you to specify which part of the string to strip, identify the substring to remove, and determine the number of characters to eliminate.


Usage
^^^^^

To use the `*strip` data converter, you need to initialize it with specific parameters in the input string. The input string should follow one of these formats:

- ``*strip:<side>:<amount>``
- ``*strip:<side>:<substring>[:<amount>]``
- ``*strip:<side>:*char:<substring>[:<amount>]``

Where:

- ``<side>``: Specifies which part of the string to strip. It must be one of ``*prefix``, ``*suffix``, or ``*both``.
- ``<substring>``: Identifies the substring to remove. It can be a specific string, ``*nil`` for null characters, ``*space`` for spaces, or any other character.
- ``<amount>`` (optional): Determines the number of characters to remove. If omitted, all instances of ``<substring>`` are removed.

Examples
^^^^^^^^

- ``*strip:*prefix:5``: Removes the first 5 characters from the string's prefix.
- ``*strip:*suffix:*nil``: Eliminates all trailing null characters in the string.
- ``*strip:*both:*space:2``: Clears 2 spaces from both the prefix and suffix of the string.
- ``*strip:*suffix:*char:abc``: Removes the substring "abc" from the suffix of the string.
- ``*strip:*prefix:*char:abc:2``: Strips the substring "abc" from the prefix of the string, repeated 2 times.

Conclusion
----------

Understanding and effectively using the configuration file is essential for optimizing the performance and behavior of the :ref:`cgr-engine`. For additional assistance or information, refer to the official documentation and support channels.