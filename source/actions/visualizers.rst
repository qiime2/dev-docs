Visualizers
===========
A :term:`visualizer` accepts some combination of QIIME 2 ``artifacts`` and parameters as input, and produces exactly one :term:`visualization` as output. Visualizations are visual representations of analytical results (e.g., plots, statistical results, summary tables) and by definition cannot be used as input to other QIIME 2 methods or visualizers. Thus, visualizers can only produce terminal output in a QIIME 2 analysis.

Create a function to register as a Visualizer
---------------------------------------------

Defining a function that can be registered as a :term:`Visualizer` is very similar to defining one that can be registered as a :doc:`Method <methods>` with a few additional requirements.

First, the first parameter to this function must be ``output_dir``. This parameter should be annotated with type ``str``.

Next, at least one ``index.*`` file must be written to ``output_dir`` by the function. This index file will provide the starting point for your users to explore the :term:`Visualization` object that is generated by the ``Visualizer``. Index files with different extensions can be created by the function (e.g., ``index.html``, ``index.tsv``, ``index.png``), but at least one must be created. You can write whatever files you want to ``output_dir``, including tables, graphics, and textual descriptions of the results, but you should expect that your users will want to find those files through your index file(s). If your function does create many different files, an ``index.html`` containing links to those files is likely to be helpful.

Finally, the function cannot return anything, and its return type should be annotated as ``None``.

``q2_diversity.alpha_group_significance`` is an example of a function that can be registered as a ``Visualizer``. In addition to its ``output_dir``, it takes alpha diversity results in a ``pandas.Series`` and sample metadata in a ``qiime2.Metadata`` object and creates several different files (figures and tables) that are linked and/or presented in an ``index.html`` file. The signature of this function is:

.. code-block:: python

   def alpha_group_significance(output_dir: str, alpha_diversity: pd.Series,
                                metadata: qiime2.Metadata) -> None:


Registering a Visualizer
------------------------
Registering ``Visualizers`` is the same as registering :doc:`Methods <methods>`, with two exceptions.

First, you call ``plugin.visualizers.register_function`` to register a ``Visualizer``.

Next, you do not provide ``outputs`` or ``output_descriptions`` when making this call, as ``Visualizers``, by definition, only return a single visualization. Since the visualization output path is a required parameter, you do not include this in an ``outputs`` list (it would be the same for every ``Visualizer`` that was ever registered, so it is added automatically).

Registering ``q2_diversity.alpha_group_significance`` as a ``Visualizer`` looks like the following:

.. code-block:: python

   plugin.visualizers.register_function(
       function=q2_diversity.alpha_group_significance,
       inputs={'alpha_diversity': SampleData[AlphaDiversity]},
       parameters={'metadata': Metadata},
       input_descriptions={
           'alpha_diversity': 'Vector of alpha diversity values by sample.'
       },
       parameter_descriptions={
           'metadata': 'The sample metadata.'
       },
       name='Alpha diversity comparisons',
       description=("Visually and statistically compare groups of alpha diversity"
                    " values."),
       citations=[citations['kruskal1952use']]
   )

See the :doc:`Methods documentation <methods>` for descriptions of the values being provided to `plugin.visualizers.register_function`.
