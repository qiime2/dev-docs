Transformers
============
``Transformers`` are functions for converting :term:`Semantic Type` objects into other formats that can be consumed by python functions. These transformers are typically defined along with the ``Semantic Types`` for which they are designed, and ``q2-types`` provides a number of common ``Types`` and their transformers. Plugins often define new semantic types and/or transformers that are not covered by these core ``Types``.

How are transformers used by a plugin?
--------------------------------------
``Transformers`` are not called directly at any time within a plugin. This process is handled by the QIIME 2 framework, as long as the appropriate ``transformers`` are registered in that plugin. The framework interprets the input :term:`Artifact` source format and destination format from a ``plugin``'s registration and functional annotation, respectively. Upon output from a :term:`Method`, the framework interprets the source format (function output) and the ``Semantic Type`` of the desination ``Artifact`` from the ``plugin`` functional annotation and registration, respectively.

For example, we can see how functional annotations define input and output formats in ``q2_diversity.beta_phylogenetic``:

.. code-block:: python

   def beta_phylogenetic(table: biom.Table, phylogeny: skbio.TreeNode,
                         metric: str)-> skbio.DistanceMatrix:

This function requires ``biom.Table`` and ``skbio.TreeNode`` objects as input, and produces an ``skbio.DistanceMatrix`` object as output.

We can examine the first few lines of the ``Method`` registration for this function to determine the ``Semantic Types`` of these input and output objects:

.. code-block:: python

   plugin.methods.register_function(
       function=q2_diversity.beta_phylogenetic,
       inputs={'table': FeatureTable[Frequency],
               'phylogeny': Phylogeny[Rooted]},
       parameters={'metric': Str % Choices(beta.phylogenetic_metrics())},
       outputs=[('distance_matrix', DistanceMatrix % Properties('phylogenetic'))],


So we see that the ``biom.Table`` object begins its life as a ``FeatureTable[Frequency]`` artifact, the ``skbio.TreeNode`` comes from a ``Phylogeny[Rooted]`` artifact, and the output ``skbio.DistanceMatrix`` must somehow be coerced to become a ``DistanceMatrix[phylogenetic]`` artifact. How do we possibly handle this? Before you hyperventilate, remember that the QIIME 2 framework does all conversion for you, `provided the appropriate ``transformers`` have been registered`.

.. _registering-a-transformer:

Registering a Transformer
-------------------------
To give you an idea how this works, let's take a look at how an example ``transformer`` is registered in ``q2-types``:

.. code-block:: python

   import skbio
   
   from ..plugin_setup import plugin
   from . import LSMatFormat
   
   
   @plugin.register_transformer
   def _1(data: skbio.DistanceMatrix) -> LSMatFormat:
       ff = LSMatFormat()
       with ff.open() as fh:
           data.write(fh, format='lsmat')
       return ff
   
   
   @plugin.register_transformer
   def _2(ff: LSMatFormat) -> skbio.DistanceMatrix:
       return skbio.DistanceMatrix.read(str(ff), format='lsmat', verify=False)


These transformers define how an ``skbio.DistanceMatrix`` object is transformed into an ``LSMatFormat`` object (the underlying format of the data in a ``DistanceMatrix[phylogenetic]`` artifact, as defined in q2-types).

So QIIME 2 recognizes (in the function annotation) that it has an incoming ``skbio.DistanceMatrix``, which it transforms (via the registered ``Transformer``) to ``LSMatFormat`` and packages into a ``DistanceMatrix[phylogenetic]`` artifact (as defined in the ``Method`` registration). Easy as 🎂.

