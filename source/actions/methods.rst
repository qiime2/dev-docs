Methods
=======
A :term:`method` accepts some combination of QIIME 2 ``artifacts`` and parameters as input, and produces one or more QIIME 2 artifacts as output. These output artifacts could subsequently be used as input to other QIIME 2 `methods` or `visualizers`.

Create a function to register as a Method
-----------------------------------------

A function that can be registered as a ``Method`` will have a Python 3 API, and the inputs and outputs for that function will be annotated with their data types using `mypy`_ syntax. mypy annotation does not impact functionality (though the syntax is new to Python 3), so these can be added to existing functions in your Python 3 software project. An example is ``q2_diversity.beta_phylogenetic``, which takes a ``biom.Table``, an ``skbio.TreeNode`` and a ``str`` as input, and produces an ``skbio.DistanceMatrix`` as output. The signature for this function is:

.. code-block:: python

   def beta_phylogenetic(table: biom.Table, phylogeny: skbio.TreeNode,
                         metric: str)-> skbio.DistanceMatrix:


As far as QIIME is concerned, it doesn’t matter what happens inside this function (as long as it adheres to the contract defined by the signature regarding the input and output types). For example, ``q2_diversity.beta_phylogenetic`` is making some calls to the ``skbio`` and ``biom`` APIs, but it could be doing anything, including making system calls (if your plugin is wrapping a command line application), executing an R library, etc.


Registering a Method
--------------------
Once you have a function that you’d like to register as a ``Method``, and you’ve instantiated your ``Plugin`` object, you are ready to register that function as a ``Method``. This will likely be done in the file where the ``Plugin`` object was instantiated, as it will use that instance (which will be referred to as ``plugin`` in the following examples).

We register a `Method` by calling ``plugin.methods.register_function`` as follows (continuing with the example of ``q2_diversity.beta_phylogenetic``):

.. code-block:: python

   from q2_types import (FeatureTable, Frequency, Phylogeny,
                      Rooted, DistanceMatrix)
   from qiime2.plugin import Str, Choices, Properties, Metadata, Citations

   import q2_diversity
   import q2_diversity._beta as beta


   citations = Citations.load('citations.bib', package='q2_diversity')


   plugin.methods.register_function(
       function=q2_diversity.beta_phylogenetic,
       inputs={'table': FeatureTable[Frequency],
               'phylogeny': Phylogeny[Rooted]},
       parameters={'metric': Str % Choices(beta.phylogenetic_metrics())},
       outputs=[('distance_matrix', DistanceMatrix % Properties('phylogenetic'))],
       input_descriptions={
           'table': ('The feature table containing the samples over which beta '
                     'diversity should be computed.'),
           'phylogeny': ('Phylogenetic tree containing tip identifiers that '
                         'correspond to the feature identifiers in the table. '
                         'This tree can contain tip ids that are not present in '
                         'the table, but all feature ids in the table must be '
                         'present in this tree.')
       },
       parameter_descriptions={
           'metric': 'The beta diversity metric to be computed.'
       },
       output_descriptions={'distance_matrix': 'The resulting distance matrix.'},
       name='Beta diversity (phylogenetic)',
       description=("Computes a user-specified phylogenetic beta diversity metric"
                    " for all pairs of samples in a feature table."),
       citations=[
           citations['lozupone2005unifrac'],
           citations['lozupone2007quantitative']]

   )


The values being provided are:

``function``: The function to be registered as a method.

``inputs``: A dictionary indicating the parameter name and its :term:`semantic type`, for each input ``Artifact``. These semantic types differ from the data types that you provided in your `mypy`_ annotation of the input, as ``semantic types`` describe the data, where the data types indicate the structure of the data. In the example above we’re indicating that the table parameter must be a ``FeatureTable`` of ``Frequency`` (i.e. counts), and that the `phylogeny` parameter must be a ``Phylogeny`` that is ``Rooted``. Notice that the keys in inputs map directly to the parameter names in ``q2_diversity.beta_phylogenetic``.

``parameters``: A dictionary indicating the parameter name and its semantic type, for each input Parameter. These parameters are primitive values (i.e., non-``Artifacts``). In the example above, we’re indicating that the metric should be a string from a specific set (in this case, the set of known phylogenetic beta diversity metrics).

``outputs``: A list of tuples indicating each output name and its semantic type.

``input_descriptions``: A dictionary containing input artifact names and their corresponding descriptions. This information is used by interfaces to instruct users how to use each specific input artifact.

``parameter_descriptions``: A dictionary containing parameter names and their corresponding descriptions. This information is used by interfaces to instruct users how to use each specific input parameter. You should not include any default parameter values in these descriptions, as these will generally be added automatically by an interface.

``output_descriptions``: A dictionary containing output artifact names and their corresponding descriptions. This information is used by interfaces to inform users what each specific output artifact will be.

``name``: A human-readable name for the Method. This may be presented to users in interfaces.

``description``: A human-readable description of the Method. This may be presented to users in interfaces.

``citations``: A list of bibtex-formatted citations. These are provided in a separate ``citations.bib`` file, loaded via the ``Citations`` API, and accessed here by using their bibtex indices as keys.


.. _mypy: http://mypy-lang.org/