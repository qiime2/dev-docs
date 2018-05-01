Anatomy of an Archive
=====================
.. contents::
   :local:

QIIME 2 stores data in a directory structure called an *archive*.

These archives are then zipped to make moving data simple and convenient.

.. figure:: ../img/archive_structure.svg
   :alt: Description of archive structure

The Most Important File
-----------------------

In the root of an :term:`Archive` directory,
there is a file named `metadata.yaml`.
This file describes the :term:`Type`,
the :term:`Format` (if one exists),
and the :term:`Identity` of a peice of data.

An example of this file::

  uuid: 45c12936-4b60-484d-bbe1-98ff96bad145
  type: FeatureTable[Frequency]
  format: BIOMV210DirFmt

Data Goes In /data/
-------------------

The actual *payload* of an archive,
where we put data,
is stored in an aptly named `/data/` directory.

If the archive is a Visualization,
then the *payload* is an interactive visualization
implemented as a small static website.

If the archive is an Artifact,
then the *payload* is determined by the *format*.


Provenance Goes In /provenance/
-------------------------------




Why a ZIP File?
---------------

ZIP files are a ubiquitous and well understood format.
There is a huge variety of software available to read and manipulate ZIP files.

The ZIP format enables random access of files within the archive making it
possible to read data without extracting the entire contents of the ZIP file
(in contrast to a linear archive like TAR).

Maintainer note:
`qiime2.core.archive.archiver:_ZipArchive` is the structure responsible for
managing the contents of a ZIP file (using `zipfile.ZipFile`).

Rules for identifying an archive
--------------------------------

Every QIIME 2 *archive* has the following structure:

A root directory which is named a standard representation of a UUID (version 4),
and a file within that directory named VERSION.

Within VERSION the following text will be present::

  QIIME 2
  archive: <integer version>
  framework: <version string>

Maintainer note:
This file is NOT YAML (and shouldn't be). The goal is to avoid it being caught
up by a future refactor where some other structured file format is used instead
of YAML (we do like YAML however). Additionally, line-endings are currently
unspecified, but in practice will be UNIX-style.

Where `<integer>` is the version that the archive was saved with.
This may be used to identify the *schema* of the archive structure,
allowing software to dispatch appropriate parsing logic.

For example, archive version `0` had no `/provenance/` directory.
This means there is no reason to look for it in the archive.
Admittedly it is just as easy to check if the directory exists,
however this pattern can be used for more complex cases that may arise
in the future.

Maintainer note:
These rules are encoded in `qiime2.core.archive.archiver:_Archive`
