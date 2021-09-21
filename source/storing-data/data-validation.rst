Semantic Validation
===================
.. contents::
   :local:

One of the key features of QIIME 2 is the enforcement of appropriate data storage and utilization, based
on the type of data (also referred to as the semantic type). One of the tools used to implement this is
validation. There are currently two validation methods available for for use within the QIIME 2
Framework: Format Validation and Semantic Validation.

There are two different validation methodologies are implemented to allow the decoupling of semantic types and
formats. This is achieved by running validators only in specific contexts.

Format Validation
-----------------
Why do this????? To enforce a schema on the data.

Where do we define a format?

How is it implemented?

Semantic Validation
-------------------

Why do this????? Semantic Validation is used to validate the *content* of data. The reason that we
enforce content at the semantic level is because it directly informs the usecase of the data and what
actions can be performed on it.

Semantic Types are used by QIIME 2 to restrict the actions that can be performed on a particular data
set. Semantic Validation is a tool that can be used to check that the content of a data file matches the
expected input for an action.

In an attempt to decouple formats and semantic types within the QIIME 2 Ecosystem, validation has been
implemented on semantic types, as well as formats.

Where do we define a semantic type? Can link off to semantic and primitive types here.

How is it implemented?
