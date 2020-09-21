Decentralized Provenance Tracking
=================================
.. contents::
   :local:

What Provenance Data is Captured?
---------------------------------
- `action.yaml` - a yaml description of the action
- Action-relevant metadata
- `/artifacts/` - UUID-labeled subdirectories, containing the above documents for every Artifact involved in the analysis to this point. NOTE: `/data/` is not captured here; Artifacts would rapidly grow to unusable size if it were.
Every Artifact's root directory contains a subdirectory named `/provenance/`,
containing the following:


Why Provenance Data is Captured?
---------------------------------

Some details
````````````