Testing Plugins
===============

.. contents::
   :local:

.. toctree::
   :maxdepth: 2

   busywork

Testing a QIIME 2 :term:`plugin <Plugin>` is a snap with :class:`TestPluginBase
<qiime2.plugin.testing.TestPluginBase>`!

This document will not attempt to detail best-practices with respect to testing
strategies - ask ``n`` developers how to test code and you will get ``n+1``
answers back.  QIIME 2 does however provide a convenience test harness to
simplify a few of the more repetitive parts of testing. Rather than
copy-and-paste code blocks here, links to the real code are presented below.

Formats
-------

https://github.com/qiime2/q2-types/blob/master/q2_types/feature_data/tests/test_format.py

Types
-----

https://github.com/qiime2/q2-types/blob/master/q2_types/feature_data/tests/test_type.py

Transformers
------------

https://github.com/qiime2/q2-types/blob/master/q2_types/feature_data/tests/test_transformer.py

Plugin Registration
-------------------

.. note:: testing plugin registration does not require the use of the QIIME 2 test harness.

https://github.com/qiime2/q2-vsearch/blob/master/q2_vsearch/tests/test_plugin_setup.py
