Archive Versions
================
.. contents::
   :local:

As QIIME 2 has developed, the structure of QIIME 2 :term:`archives<Archive>` has evolved.
This page describes each historical version of the QIIME 2 Archive format,
and may be useful to developers whose code depends on guarantees made by that format
(`source code <https://github.com/qiime2/qiime2/blob/master/qiime2/core/archive/>`_).

For general information about the structure of current QIIME 2 Archives, see :doc:`archive`.
For a detailed description of the part of an Archive which holds Provenance data, see :doc:`provenance`.

Version-agnostic format "guarantees"
------------------------------------

Though there is significant variability in the format of QIIME 2 Archives across versions,
all versions share some common traits.

These shared characteristics, defined in the ``_Archive`` class
in `qiime2/core/archive/archiver.py <https://github.com/qiime2/qiime2/blob/master/qiime2/core/archive/archiver.py>`_,
must be consistent across all formats over time, 
as they allow archive versions to be checked,
and archives with different formats to be dispatched to the appropriate version-specific tools.

All QIIME 2 Archives have:

- a directory named with the Archive UUID, directly under the archive root.
- a file within that directory named VERSION, formatted as shown below

The Archive file system must look like this:

.. figure:: ../img/format_agnostic_archive_structure.svg
   :alt: Box and Arrow diagram of the guaranteed components of an archive, as described above.

The VERSION file must look like this::

    QIIME 2
    archive: <archive version>
    framework: <framework version>

.. note::
   This file is itentionally not YAML/INI/An actual format. This is to
   discourage the situation where the format changes from something like YAML to
   another format and VERSION is updated with it "for consistency".

Version 0
---------

The original QIIME 2 Archive format, there aren't many V0 Archives "in the wild".
v0 Archives were produced only by Alpha Release versions prior to version 2.0.6.

- V0 Archives do not capture provenance.
- 


Version 1
---------

Created in PR #171, Version 1 Archives introduced decentralized provenance tracking
to QIIME 2.

Version 2
---------

This happened after 0

Version 3
---------

This happened after 0

Version 4
---------

This happened after 0

Version 5
---------

This happened after 0