Glossary
========
This page serves as a reference for other documents, we don't recommend reading
it without the accompanying context provided by the rest of this documentation.

.. glossary::

   Archive
     The directory structure of a QIIME 2 :term:`Result`.
     Contains *at least* a root directory (named by UUID) and a `VERSION` file
     within that directory.

   Format
     A string that represents a particular file format.

   Directory Format
     A string that represents a particular layout of files and or directories
     as well as how their contents will be structured.

   Identity
     Distinguishes a piece of data. QIIME 2 does not consider a `mv` operation
     to change identity, however re-running a command, would change identity.

   View
     A particular representation of data. This includes on-disk formats and 
     in-memory data structures (objects).

   Type
     A name that is used to classify how a piece of data may be used.
     Most commonly used to refer to :term:`Semantic Type`, but can also refer
     to :term:`Primitive Type` and :term:`Visualization (Type)`. 

   Semantic Type
     A :term:`Type` that is used to classify :term:`Artifact` and how they
     can be used. These types may be extended by :term:`Plugin`.

   Primitive Type
     A :term:`Type` that is used to communicate parameters to an :term:`Interface`.
     These are predefined by the framework and cannot be extended.

   Visualization (Type)
     The :term:`Type` of a :term:`Visualization`. There is no subtyping
     relations between this type and any other (it is a singleton) and cannot
     be extended (because it is a singleton).

   Payload
     Data that is meant for primary consumption or interpretation (in contrast
     to *metadata* which may be useful retrospectively, but is not primarily
     useful).

   Result
     A generic term for either a Visualization or an Artifact.

   Visualization
     A QIIME 2 :term:`Result` that contains an interactive visualization.

   Artifact
     A QIIME 2 :term:`Result` that countains data to operate on.

   Plugin
     TODO

   Interface
     TODO

   Framework
     TODO

   Action
     TODO

   Method
     TODO
 
   Visualizer
     TODO
