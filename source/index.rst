.. jasmin-doc documentation master file, created by
   sphinx-quickstart on Mon Feb  3 16:25:51 2025.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to the jasmin documentation!
======================================

.. include:: jasmin.wiki/About.md
   :parser: myst_parser.sphinx_

.. toctree::
   :maxdepth: 2
   :caption: Jasmin-Lang:
   :glob:

   Jasmin-language-guide.md
   jasmin.wiki/Arrays.md
   jasmin.wiki/System-calls.md

.. toctree::
   :maxdepth: 2
   :caption: Jasmin Compiler:
   :glob:

   jasmin.wiki/Reference-interpreter.md
   jasmin.wiki/Howto-Add-Instructions.md
   jasmin.wiki/Compilation-to-assembly.md
   jasmin.wiki/Command-Line-Interface.md
   jasmin.wiki/Installation-instructions.md
   jasmin.wiki/Compilation-passes.md
   jasmin.wiki/Memory-Layout.md
   jasmin.wiki/Pretty-printing.md

.. toctree::
   :maxdepth: 2
   :caption: Tooling:
   :glob:

   jasmin.wiki/Extraction-to-EasyCrypt.md
   jasmin.wiki/Safety-checker.md
   jasmin.wiki/Selective-Speculative-Load-Hardening.md
   jasmin.wiki/Constant-time-verification.md


.. toctree::
   :maxdepth: 2
   :caption: Misc:
   :glob:

   jasmin.wiki/FAQ.md
   jasmin.wiki/Get-support.md
   jasmin.wiki/Wish-List.md
   jasmin.wiki/dev/Branches.md
   jasmin.wiki/dev/CI-on-gitlab.md
   jasmin.wiki/dev/Making-a-release.md
   jasmin.wiki/Emacs-mode.md


.. include:: jasmin.wiki/Home.md
   :parser: myst_parser.sphinx_

Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
