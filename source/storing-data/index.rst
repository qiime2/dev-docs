How Data is Stored
==================
.. contents::
   :local:

In any software project, data needs to be stored (or persisted).
The way that this is accomplished can impact every facet of a software's design.
In order to better demonstrate *why* certain aspects of QIIME 2 exist as they are
we will highlight what goals or constraints QIIME 2 has, and then demonstrate how
QIIME 2 achieves each goal.

If you are diving straight into the details, the following pages document the
specifics:

- :doc:`archive`
- :doc:`types`
- :doc:`formats`
- :doc:`provenance`

.. this isn't a toctree because it would be repeated twice in the global index

Otherwise, continue reading for a high-level summary of the topic.

Goals
-----
Data should be stored in a way that:

- is accessible long-term (e.g. 20 years)
- makes the transfer of data between users convenient
- makes it possible to determine when data is valid input
- eases interoperability between tools
- can be extended by plugins
- allows it to track the full history (provenance) of execution

These goals directly impact many of the design decisions made in QIIME 2.
The solution described in this section seeks to address each of these goals.
There are many other ways to solve these problems when one or more of these
constraints are lifted, however QIIME 2 chooses these constraints because we
believe they are *useful* to the scientist and will allow *composable* software
to be developed and reused to advance the state of the art.

Accessibility and Transferability
---------------------------------
QIIME 2 stores all data as a directory structure inside of a ZIP file.
There is a :term:`payload` directory named ``/data/`` where data is stored in
a common format. This permits additional *metadata* to be stored alongside the
data (in non-``/data/`` directories or files).

Why a Directory-based Archive is Used
`````````````````````````````````````
To store data in a way that is accessible,
it should be a common format
when such a format exists
(for example a FASTA file or a newick file).
When such a format does not exist,
it should be a plain-text structure that is self-descriptive.
Such that, a person in 20 years might be able to glance at it,
and roughly understand what the purpose of a given document is
(assuming file-systems and text-based encodings still make sense in the future).

Because some common formats are paired with others, or reused multiple times to
represent multiple entities (e.g. multiple samples),
the data that a tool needs is sometimes a *directory* of formats.
Alternatively a new format could be invented with new rules
(though this would make interoperability difficult).

For these reasons, QIIME 2 stores data as a directory structure.
In particular data such as FASTA or newick will be considered the :term:`Payload <payload>` which is to be delivered to a tool.

There is a flaw to using directory structures as a way of storing data.
Moving directory structures is inconvenient as they do not exist as a single file.
A common way to fix this is to zip a file and extract it at the destination.
This is exactly what QIIME 2 does.
ZIP files additionally have the advantage of being incredibly well supported
by a *wide* array of software.
Some software manipulates ZIP files directly
(often built into an operating system's graphical interface)
and others use ZIP files as a backing structure
(such as ``.docx`` and ``.epub``).
Because it is so widely used, maintaining the long-term accessibility of data is much more likely.

Learn More About the Archive Structure
``````````````````````````````````````
The following sections detail more specific information about the directory
structure and ZIP file:

.. toctree::
   :maxdepth: 2

   archive


Input Validation (Type Checking)
--------------------------------
QIIME 2 stores a file named ``metadata.yaml`` alongside the ``/data/``
directory. This file contains the :term:`type` of the data, which QIIME 2 can
use to validate that a given ZIP file is valid input for a given :term:`action`.

Why Types and Other Metadata are Stored
```````````````````````````````````````
Now that there is a way to store a :term:`payload`
and a way to move it around,
there needs to be a way to *describe* it
so that the computer can determine if a given payload is valid.
This prevents user errors and allows :term:`interfaces<Interface>` to provide a
more complete and rich user interface.

To accomplish this, we need data about the data, or *metadata*
(in the general sense, this should not be confused with QIIME 2's sample/feature metadata).
If the :term:`payload` is placed in a *subdirectory*
then we can store additional files which can contain this *metadata*
without needed to worry about filename conflicts with the payload itself.
Now QIIME 2 is able to record a type and anything else that may enable the
computer (or user) to make a more informed decision about the use of a given piece of data.

Learn More About Type Checking
``````````````````````````````
The following sections provide more information for how types are used and
defined in QIIME 2:

.. toctree::
   :maxdepth: 2

   types


Interoperability and Extension
------------------------------
QIIME 2 stores a string called a :term:`Directory Format` in ``metadata.yaml``
which instructs the computer what the specific layout of ``/data/`` is.
Once this is known, it is possible to convert that data into other formats.
:term:`plugins<Plugin>` can define new formats and request data in specific :term:`views<View>`.

Why Directory Formats are Needed
````````````````````````````````
Different tools expect different file formats or in-memory data structures.
Many of these are *semantically* substitutable, in other words, they can carry
the same information (in different ways). Another way to state this is
that these different formats and data-structures each represent a different
:term:`view` of the same data.

If we combine this idea with a :term:`semantic type` we are able to use the
abstraction of the type to ignore the view when reasoning about composition of
:term:`actions<Action>`. While the :term:`semantic type` may be
adequate for describing what data is used for, it does not provide a means to
structure it (on-disk or in-memory). For this we use a :term:`view`.
In particular, when storing data for later use (or sharing) it is necessary to
save it to disk in some way (in particular, we need to store it in ``/data/``).
We use a :term:`directory format` to accomplish this purpose. It has a name
which is recorded in ``metadata.yaml`` and defines how ``/data/`` is to be
structured.

Learn More About (Directory) Formats
````````````````````````````````````
The following sections provide additional information about formats and
directory formats in QIIME 2:

.. toctree::
   :maxdepth: 2

   formats


Provenance Metadata
-------------------

Inside of each Artifact, QIIME 2 stores metadata about how that artifact was 
generated. We call this "provenance". Notably, each Artifact contains
provenance information about *every prior QIIME 2 :term:`actions<Action>`* involved
in its creation, from `import` to the most recent step in the analysis.

This provenance information includes type and format information, system and
environment details, the Actions performed and all parameters passed to them,
and all registered citations.

Learn More About Provenance
```````````````````````````
.. toctree::
   :maxdepth: 2

   provenance

For a detailed discussion of the file structure which holds provenance metadata,
see :ref:`provenance-structure`.