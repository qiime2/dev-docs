Decentralized Provenance Tracking
=================================
.. contents::
   :local:

QIIME 2 provides automatic, integrated, and decentralized tracking of analysis
metadata, including information about the host system, the computing
environment, Actions performed, parameters passed, primary sources cited, and
more. We describe all of this information about "how an analysis was
performed" as "provenance".

The notion of a QIIME 2 :term:`Result` is central here. Whenever an
:term:`Action` is performed on some data with QIIME 2, the framework
captures relevant metadata about the action and environment and stores it in
the Action's Result. When/if that Result is saved as an :term:`Archive`, the
captured provenance data is stored within the Archive as well. (This happens
automatically with `q2cli`, and manually with the Artifact API.) For
reference, :ref:`provenance-structure` contains a detailed discussion of the
file structure which holds provenance metadata.

Why Capture Provenance Data?
----------------------------

Provenance gives QIIME 2 users, paper reviewers, and developers valuable
tools for zero-cost documentation, study validation and reproduction,
workflow pipelining, and software maintenance and repair.

Among the benefits of this model are:

- Analyses are *fully* reproducible.
- Analyses self-document, reducing the need for investigator notetaking. For example, q2view produces directed provenance graphs. QIIME 2 Artifacts bring their citations with them. Methods-section text could theoretically be generated from a collection of QIIME 2 Artifacts.
- Analyses are replay-able. The QIIME 2 team is developing functionality to generate executable scripts from prior results, simplifying the repetition of analyses.
- In the unlikely event of a data integrity bug, problematic combinations of hardware, environment, Action, and parameters can be investigated effectively. Impacted results can be programatically identified, and could be programatically correctable in some cases.

By capturing provenance metadata at the level of Actions/Results, QIIME 2
provenance is both host- and interface-agnostic. In other words, a QIIME 2
analysis can be performed across various host systems, using whatever interfaces
the user prefers, without compromising the validity of the analysis or the
provenance. The result of every step in the analysis contains its own
unique history.

What Provenance Data is Captured?
---------------------------------

.. figure:: ../img/prov_unit_whole.svg
   :alt: A file tree diagram of all provenance files associated with one Action
.. figure:: ../img/prov_unit.svg
   :alt: Simplified representation of the provenance files associated with one Action
.. figure:: ../img/prov_whole_archive.svg
   :alt: Simplified representation of all files within one Archive, emphasizing how an Archive holds provenance for an arbitrary number of Actions

TODO: indicate that prov_unit = prov_unit_whole
TODO: Use boxes to focus reader on the fact that `provenance/ holds exactly the same stuff as each UUID dir, plus `artifacts/`
TODO: Screencap or mockup of `action.yaml`, possibly from q2view or just a code block in the RST, with high-level information on what it contains...
... and information on how to find examples in the wild. 

- `action.yaml` - a yaml description of the action
- Action-relevant metadata
- `/artifacts/` - UUID-labeled subdirectories, containing the above documents for every Artifact involved in the analysis to this point. NOTE: `/data/` is not captured here; Artifacts would rapidly grow to unusable size if it were.

Every Artifact's root directory contains a subdirectory named `/provenance/`,
containing the following:


Some details
````````````