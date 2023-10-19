************
Contributing
************

You want to contribute? Start by checking out the repository. Details are on the `download <https://lsc-project.org/download.html>`__ page.

Feel free to send
=================

* patches (against master or recent branch)
* documentation (documentation is written in `Restructured Text <https://www.sphinx-doc.org/en/master/usage/restructuredtext/index.html>`__, fill free to contribute to the `git documentation repository <https://github.com/lsc-project/documentation>`__
* feedback

The preferred language is English, but please send it anyway!

Source code
===========

Please adhere to :doc:`our defined code style <codestyle>`.

Please check your code against:

* Checkstyle + Sun checks for code style
* Cobertura for test coverage (try to reach 30% for new features)

If you add new features, please contribute also the technical specification and test cases (JUnit + test plan).

Process
=======

If you want to propose a fix or a new feature, please send a pull request on our github repository. Someone of the LSC core team should answer you within 3 days.

Even core team members need to contribute trough pull request in order to get feedback before applying their changes. If a core team member does not get any feedback within 3 days, he may apply himself his pull request.

Contribution strategy
=====================

There are multiple branches in the repository:

* ``main``: current development branch
* ``v2.X``: contains all the fixes for the current 2.X stable version

If you want to propose a pull request, you must determine if your change:

* is introducing a new feature or a fix that breaks the current behaviour of LSC
* is a minor change, or is fixing a bug

In the first case you should propose a merge into ``main``, in the 2nd case in ``v2.X``.

Development documentation
=========================

A collection of :ref:`documentation for developers <index-development>` is available:

Forge
=====

You can find LSC on GitHub: `https://github.com/lsc-project <https://github.com/lsc-project>`__. There you can fill bugs, see the history of commits,...

License and contributions
=========================

LSC is under BSD license. To contribute new features or documentation, you need to send your code with a precise notice about the license under which you want to contribute. If this is not the original project license, your contribution will be integrated in a "third parties contribution" directory (non existent at this time).


