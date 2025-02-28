.. image:: https://github.com/kynan/nbstripout/actions/workflows/tests.yml/badge.svg
    :target: https://github.com/kynan/nbstripout/actions/workflows/tests.yml
.. image:: https://img.shields.io/pypi/dm/nbstripout
    :target: https://pypi.org/project/nbstripout
.. image:: https://img.shields.io/pypi/v/nbstripout
    :target: https://pypi.org/project/nbstripout
.. image:: https://img.shields.io/conda/vn/conda-forge/nbstripout
    :target: https://anaconda.org/conda-forge/nbstripout
.. image:: https://img.shields.io/pypi/pyversions/nbstripout
    :target: https://pypi.org/project/nbstripout
.. image:: https://img.shields.io/pypi/format/nbstripout
    :target: https://pypi.org/project/nbstripout
.. image:: https://img.shields.io/pypi/l/nbstripout
    :target: https://raw.githubusercontent.com/kynan/nbstripout/master/LICENSE.txt
.. image:: https://img.shields.io/github/stars/kynan/nbstripout?style=social
    :target: https://github.com/kynan/nbstripout/stargazers
.. image:: https://img.shields.io/github/forks/kynan/nbstripout?style=social
    :target: https://github.com/kynan/nbstripout/network/members

nbstripout: strip output from Jupyter and IPython notebooks
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Opens a notebook, strips its output, and writes the outputless version to the
original file.

Useful mainly as a git filter or pre-commit hook for users who don't want to
track output in VCS.

This does mostly the same thing as the `Clear All Output` command in the
notebook UI.

Based on https://gist.github.com/minrk/6176788.

Python 3 only
=============

As of version 0.5.0, nbstripout supports Python 3 *only*. If you need to use
Python 2.7, install nbstripout 0.3.10 ::

    pip install nbstripout==0.3.10

Screencast
==========

This screencast demonstrates the use and working principles behind the
nbstripout utility and how to use it as a Git filter:

.. image:: https://i.imgur.com/7oQHuJ5.png
    :target: https://www.youtube.com/watch?v=BEMP4xacrVc

Installation
============

You can download and install the latest version of ``nbstripout`` from PyPI_,
the Python package index, as follows: ::

    pip install --upgrade nbstripout

When using the Anaconda_ Python distribution, install ``nbstripout`` via the
conda_ package manager from conda-forge_: ::

    conda install -c conda-forge nbstripout

Usage
=====

Strip output from IPython / Jupyter / Zeppelin notebook (modifies the file in-place): ::

    nbstripout FILE.ipynb [FILE2.ipynb ...]
    nbstripout FILE.zpln

Force processing of non ``.ipynb`` files: ::

    nbstripout -f FILE.ipynb.bak

For using Zeppelin mode while processing files with other extensions use: ::

    nbstripout -m zeppelin -f <file.ext>

Write to stdout e.g. to use as part of a shell pipeline: ::

    cat FILE.ipynb | nbstripout > OUT.ipynb
    cat FILE.zpln | nbstripout -m zeppelin > OUT.zpln

or ::

    nbstripout -t FILE.ipynb | other-command

Set up the git filter and attributes as described in the manual installation
instructions below: ::

    nbstripout --install

Set up the git filter using ``.gitattributes`` ::

    nbstripout --install --attributes .gitattributes

Set up the git filter in your global ``~/.gitconfig`` ::

    nbstripout --install --global

Set up the git filter in your system-wide ``$(prefix)/etc/gitconfig`` (most installations will require you to ``sudo``) ::

    [sudo] nbstripout --install --system

Remove the git filter and attributes: ::

    nbstripout --uninstall

Remove the git filter from your global ``~/.gitconfig`` and attributes ::

    nbstripout --uninstall --global

Remove the git filter from your system-wide ``$(prefix)/etc/gitconfig`` and attributes ::

    [sudo] nbstripout --uninstall --system

Remove the git filter and attributes from ``.gitattributes``: ::

    nbstripout --uninstall --attributes .gitattributes

Check if ``nbstripout`` is installed in the current repository
(exits with code 0 if installed, 1 otherwise): ::

    nbstripout --is-installed

Print status of ``nbstripout`` installation in the current repository and
configuration summary of filter and attributes if installed
(exits with code 0 if installed, 1 otherwise): ::

    nbstripout --status

Do a dry run and only list which files would have been stripped: ::

    nbstripout --dry-run FILE.ipynb [FILE2.ipynb ...]

Print the version: ::

    nbstripout --version

Show this help page: ::

    nbstripout --help

Configuration files
+++++++++++++++++++

The following table shows in which files the ``nbstripout`` filter and
attribute configuration is written to for given extra flags to ``--install``
and ``--uninstall``:

======================================== =========================== ===============================
flags                                    filters                     attributes
======================================== =========================== ===============================
none                                     ``.git/config``             ``.git/info/attributes``
``--global``                             ``~/.gitconfig``            ``~/.config/git/attributes``
``--system``                             ``$(prefix)/etc/gitconfig`` ``$(prefix)/etc/gitattributes``
``--attributes=.gitattributes``          ``.git/config``             ``.gitattributes``
``--global --attributes=.gitattributes`` ``~/.gitconfig``            ``.gitattributes``
======================================== =========================== ===============================

Install globally
++++++++++++++++

Usually, ``nbstripout`` is installed per repository so you can choose where to
use it or not. You can choose to set the attributes in ``.gitattributes`` and
commit this file to your repository, however there is no way to have git set up
the filters automatically when someone clones a repository. This is by design,
to prevent you from executing arbitrary and potentially malicious code when
cloning a repository.

To install ``nbstripout`` for all your repositories such that you no longer
need to run the installation once per repository, install as follows: ::

    mkdir -p ~/.config/git  # This folder may not exist
    nbstripout --install --global --attributes=~/.config/git/attributes

This will set up the filters and diff driver in your ``~/.gitconfig`` and
instruct git to apply them to any ``.ipynb`` file in any repository.

Note that you need to uninstall with the same flags: ::

    nbstripout --uninstall --global --attributes=~/.config/git/attributes

Install system-wide
+++++++++++++++++++

To install ``nbstripout`` system-wide so that it applies to all repositories
for all users, install as follows (most installations will require you to ``sudo``): ::

    [sudo] nbstripout --install --system

This will set up the filters and diff driver in ``$(prefix)/etc/gitconfig`` and
instruct git to apply them to any ``.ipynb`` file in any repository for any user.

Note that you need to uninstall with the same flags: ::

    [sudo] nbstripout --uninstall --system

Apply retroactively
+++++++++++++++++++

``nbstripout`` can be used to rewrite an existing Git repository using
``git filter-branch`` to strip output from existing notebooks. This invocation
uses ``--index-filter`` and operates on all ipynb-files in the repo: ::

    git filter-branch -f --index-filter '
        git checkout -- :*.ipynb
        find . -name "*.ipynb" -exec nbstripout "{}" +
        git add . --ignore-removal
    '

If the repository is large and the notebooks are in a subdirectory it will run
faster with ``git checkout -- :<subdir>/*.ipynb``. You will get a warning for
commits that do not contain any notebooks, which can be suppressed by piping
stderr to ``/dev/null``.

This is a potentially slower but simpler invocation using ``--tree-filter``: ::

    git filter-branch -f --tree-filter 'find . -name "*.ipynb" -exec nbstripout "{}" +'

Removing empty cells
++++++++++++++++++++

Strip empty cells i.e. cells where ``source`` is either empty or only contains
whitespace ::

    nbstripout --strip-empty-cells

Removing `init` cells
++++++++++++++++++++

By default ``nbstripout`` will keep cells with `init_cell: true` metadata. To disable
this behavior use ::

    nbstripout --strip-init-cells

Removing entire cells
++++++++++++++++++++

In certain conditions it might be handy to remove not only the output, but the entire cell, e.g. when developing exercises.

To drop all cells tagged with "solution" run:

    nbstripout --drop-tagged-cells="solution"

The option accepts a list of tags separated by whitespace.

Keeping some output
+++++++++++++++++++

Do not strip the execution count/prompt number ::

    nbstripout --keep-count

Do not strip outputs that are smaller that a given max size (useful for removing large outputs like images) ::

    nbstripout --max-size 1k

Do not strip the output ::

    nbstripout --keep-output

To mark special cells so that the output is not stripped, you can either:

1.  Set the ``keep_output`` tag on the cell. To do this, enable the tags
    toolbar (View > Cell Toolbar > Tags) and then add the ``keep_output`` tag
    for each cell you would like to keep the output for.

2.  Set the ``"keep_output": true`` metadata on the cell.  To do this, select
    the "Edit Metadata" Cell Toolbar, and then use the "Edit Metadata" button
    on the desired cell to enter something like::

        {
          "keep_output": true,
        }

You can also keep output for an entire notebook. This is useful if you want to
strip output by default in an automated environment (e.g. CI pipeline), but want
to be able to keep outputs for some notebooks. To do so, add the option above to
the *notebook* metadata instead. (You can also explicitly remove outputs from
a particular cell in these notebooks by adding a cell-level metadata entry.)

Another use-case is to preserve initialization cells that might load
customized CSS etc. critical for the display of the notebook.  To
support this, we also keep output for cells with::

    {
      "init_cell": true,
    }

This is the same metadata used by the `init_cell nbextension`__.

__ https://github.com/ipython-contrib/jupyter_contrib_nbextensions/tree/master/src/jupyter_contrib_nbextensions/nbextensions/init_cell

Stripping metadata
++++++++++++++++++

The following metadata is stripped by default:

* Notebook metadata: ``signature``, ``widgets``
* Cell metadata: ``ExecuteTime``, ``collapsed``, ``execution``, ``scrolled``

Additional metadata to be stripped can be configured via either

*   ``git config (--global/--system) filter.nbstripout.extrakeys``, e.g. ::

        git config --global filter.nbstripout.extrakeys '
          metadata.celltoolbar
          metadata.kernelspec
          metadata.language_info.codemirror_mode.version
          metadata.language_info.pygments_lexer
          metadata.language_info.version
          metadata.toc
          metadata.notify_time
          metadata.varInspector
          cell.metadata.heading_collapsed
          cell.metadata.hidden
          cell.metadata.code_folding
          cell.metadata.tags
          cell.metadata.init_cell'

*   the ``--extra-keys`` flag, which takes a string as an argument, e.g. ::

        --extra-keys "metadata.celltoolbar cell.metadata.heading_collapsed"

Note: Previous versions of Jupyter used ``metadata.kernel_spec`` for kernel
metadata. Prefer stripping ``kernelspec`` entirely: only stripping some
attributes inside ``kernelspec`` may lead to errors  when opening the notebook
in Jupyter (see `#141 <https://github.com/kynan/nbstripout/issues/141>`_).

Excluding files and folders
+++++++++++++++++++++++++++

To exclude specific files or folders from being processed by the ``nbstripout``
filters, add the path and exception to your filter specifications
defined in ``.git/info/attributes`` or ``.gitattributes``: ::

    docs/** filter= diff=

This will disable ``nbstripout`` for any file in the ``docs`` directory.: ::

    notebooks/Analysis.ipynb filter= diff=

This will disable ``nbstripout`` for the file ``Analysis.ipynb`` located in
the ``notebooks`` directory.

To check which attributes a given file has with the current config, run ::

    git check-attr -a -- path/to/file

For a file to which the filter applies you will see the following: ::

    $ git check-attr -a -- foo.ipynb
    foo.ipynb: diff: ipynb
    foo.ipynb: filter: nbstripout

For a file in your excluded folder you will see the following: ::

    $ git check-attr -a -- docs/foo.ipynb
    foo.ipynb: diff:
    foo.ipynb: filter:

Manual filter installation
==========================

Set up a git filter and diff driver using nbstripout as follows: ::

    git config filter.nbstripout.clean '/path/to/nbstripout'
    git config filter.nbstripout.smudge cat
    git config filter.nbstripout.required true
    git config diff.ipynb.textconv '/path/to/nbstripout -t'

This will add a section to the ``.git/config`` file of the current repository.

If you want the filter to be installed globally for your user, add the
``--global`` flag to the ``git config`` invocations above to have the
configuration written to your ``~/.gitconfig`` and apply to all repositories.

If you want the filter to be installed system-wide, add the ``--system`` flag
to the ``git config`` invocations above to have the configuration written to
``$(prefix)/etc/gitconfig`` and apply to all repositories for all users.

Create a file ``.gitattributes`` (if you want it versioned with the repository)
or ``.git/info/attributes`` (to apply it only to the current repository) with
the following content: ::

    *.ipynb filter=nbstripout
    *.ipynb diff=ipynb

This instructs git to use the filter named _nbstripout_ and the diff driver
named _ipynb_ set up in the git config above for every ``.ipynb`` file in the
repository.

If you want the attributes be set for ``.ipynb`` files in any of your git
repositories, add those two lines to ``~/.config/git/attributes``. Note that
this file and the ``~/.config/git`` directory may not exist.

If you want the attributes be set for ``.ipynb`` files in any git
repository on your system, add those two lines to ``$(prefix)/etc/gitattributes``.
Note that this file may not exist.

Using ``nbstripout`` as a pre-commit hook
=========================================

`pre-commit`_ is a framework for managing git `pre-commit hooks`_.

Once you have `pre-commit`_ installed, add the follwong to the
``.pre-commit-config.yaml`` in your repository: ::

    repos:
    - repo: https://github.com/kynan/nbstripout
      rev: 0.5.0
      hooks:
        - id: nbstripout

Then run ``pre-commit install`` to activate the hook.

.. warning::
  In this mode, ``nbstripout`` is used as a git hook to strip any ``.ipynb``
  files before committing. This also modifies your working copy!

  In its regular mode, ``nbstripout`` acts as a filter and only modifies what
  git gets to see for committing or diffing. The working copy stays intact.

.. _pre-commit: https://pre-commit.com
.. _pre-commit hooks: https://git-scm.com/docs/githooks

Troubleshooting
===============

Known issues
++++++++++++

Certain Git workflows are not well supported by `nbstripout`:

* Local changes to notebook files that are made invisible to Git due to the
  `nbstripout` filter do still cause conflicts when attempting to sync upstream
  changes (`git pull`, `git merge` etc.). This is because Git has no way of
  resolving a conflict caused by a non-stripped local file being merged with a
  stripped upstream file. Adressing this issue is out of scope for `nbstripout`.
  Read more and find workarounds in `#108`_.

.. _#108: https://github.com/kynan/nbstripout/issues/108

Show files processed by nbstripout filter
+++++++++++++++++++++++++++++++++++++++++

Git has `no builtin support <https://stackoverflow.com/a/52065333/396967>`_
for listing files a clean or smudge filter operates on. As a workaround,
change the setup of your filter in ``.git/config``, ``~/.gitconfig`` or
``$(prefix)/etc/gitconfig`` as follows to see the filenames either filter operates on: ::

    [filter "nbstripout"]
        clean  = "f() { echo >&2 \"clean: nbstripout $1\"; nbstripout; }; f %f"
        smudge = "f() { echo >&2 \"smudge: cat $1\"; cat; }; f %f"
        required = true

Mercurial usage
===============

Mercurial does not have the equivalent of smudge filters.  One can use
an encode/decode hook but this has some issues.  An alternative
solution is to provide a set of commands that first run ``nbstripout``,
then perform these operations. This is the approach of the `mmf-setup`_
package.

.. _mmf-setup: http://bitbucket.org/mforbes/mmf_setup
.. _Anaconda: https://www.continuum.io/anaconda-overview
.. _conda: http://conda.pydata.org
.. _conda-forge: http://conda-forge.github.io
.. _PyPI: https://pypi.io
