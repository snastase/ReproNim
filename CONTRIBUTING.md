Contributing to ReproNim
========================

[gh-repronim]: http://github.com/ReproNim/ReproNim

Files organization
------------------

- `repronim/` is the main Python module where major development is happening,
  with major submodules being:
    - `cmdline/` - helpers for accessing `interface/` functionality from
     command line
    - `interface/` - high level interface functions which get exposed via
      command line (`cmdline/`) or Python (`repronim.api`).
    - `tests/` - some unit- and regression- tests (more could be found under
      `tests/` of corresponding submodules)
        - `utils.py` provides convenience helpers used by unit-tests such as
          `@with_tree`, `@serve_path_via_http` and other decorators
    - `ui/` - user-level interactions, such as messages about errors, warnings,
      progress reports, AND when supported by available frontend --
      interactive dialogs
    - `support/` - various support modules, e.g. for git/git-annex interfaces,
      constraints for the `interface/`, etc
- `docs/` - yet to be heavily populated documentation
    - `bash-completions` - bash and zsh completion setup for repronim (just
      `source` it)
- `tools/` contains helper utilities used during development, testing, and
  benchmarking of ReproNim.  Implemented in any most appropriate language
  (Python, bash, etc.)

How to contribute
-----------------

The preferred way to contribute to the ReproNim code base is
to fork the [main repository][gh-repronim] on GitHub.  Here
we outline the workflow used by the developers:


0. Have a clone of our main [project repository][gh-repronim] as `origin`
   remote in your git:

          git clone git://github.com/repronim/repronim

1. Fork the [project repository][gh-repronim]: click on the 'Fork'
   button near the top of the page.  This creates a copy of the code
   base under your account on the GitHub server.

2. Add your forked clone as a remote to the local clone you already have on your
   local disk:

          git remote add gh-YourLogin git@github.com:YourLogin/repronim.git
          git fetch gh-YourLogin

    To ease addition of other github repositories as remotes, here is
    a little bash function/script to add to your `~/.bashrc`:

        ghremote () {
                url="$1"
                proj=${url##*/}
                url_=${url%/*}
                login=${url_##*/}
                git remote add gh-$login $url
                git fetch gh-$login
        }

    thus you could simply run:

         ghremote git@github.com:YourLogin/repronim.git

    to add the above `gh-YourLogin` remote.  Additional handy aliases
    such as `ghpr` (to fetch existing pr from someone's remote) and 
    `ghsendpr` could be found at [yarikoptic's bash config file](http://git.onerussian.com/?p=etc/bash.git;a=blob;f=.bash/bashrc/30_aliases_sh;hb=HEAD#l865)

3. Create a branch (generally off the `origin/master`) to hold your changes:

          git checkout -b nf-my-feature

    and start making changes. Ideally, use a prefix signaling the purpose of the
    branch
    - `nf-` for new features
    - `bf-` for bug fixes
    - `rf-` for refactoring
    - `doc-` for documentation contributions (including in the code docstrings).
    We recommend to not work in the ``master`` branch!

4. Work on this copy on your computer using Git to do the version control. When
   you're done editing, do:

          git add modified_files
          git commit

   to record your changes in Git.  Ideally, prefix your commit messages with the
   `NF`, `BF`, `RF`, `DOC` similar to the branch name prefixes, but you could
   also use `TST` for commits concerned solely with tests, and `BK` to signal
   that the commit causes a breakage (e.g. of tests) at that point.  Multiple
   entries could be listed joined with a `+` (e.g. `rf+doc-`).  See `git log` for
   examples.  If a commit closes an existing ReproNim issue, then add to the end
   of the message `(Closes #ISSUE_NUMER)`

5. Push to GitHub with:

          git push -u gh-YourLogin nf-my-feature

   Finally, go to the web page of your fork of the ReproNim repo, and click
   'Pull request' (PR) to send your changes to the maintainers for review. This
   will send an email to the committers.  You can commit new changes to this branch
   and keep pushing to your remote -- github automagically adds them to your
   previously opened PR.

(If any of the above seems like magic to you, then look up the
[Git documentation](http://git-scm.com/documentation) on the web.)

Development environment
-----------------------

Although we now support Python 3 (>= 3.3), primarily we still use Python 2.7
and thus instructions below are for python 2.7 deployments.  Replace `python-{`
with `python{,3}-{` to also install dependencies for Python 3 (e.g., if you would
like to develop and test through tox).

See [README.md:Dependencies](README.md#Dependencies) for basic information
about installation of repronim itself.
On Debian-based systems we recommend to enable [NeuroDebian](http://neuro.debian.net)
since we use it to provide backports of recent fixed external modules we depend upon.

```sh
apt-get install -y -q python-{appdirs,argcomplete,humanize,mock,setuptools,six,yaml}
```

and additionally, for development we suggest to use tox and new
versions of dependencies from pypy:

```sh
apt-get install -y -q python-{nose,pip,vcr,virtualenv} python-tox
```

some of which you could also install from PyPi using pip  
(prior installation of those libraries listed above might be necessary)

```sh
pip install -r requirements.txt
```

and you will need to install recent git-annex using appropriate for your
OS means (for Debian/Ubuntu, once again, just use NeuroDebian).


Documentation
-------------

### Docstrings

We use [NumPy standard] for the description of parameters docstrings.  If you are using
PyCharm, set your project settings (`Tools` -> `Python integrated tools` -> `Docstring format`).

[NumPy standard]: https://github.com/numpy/numpy/blob/master/doc/HOWTO_DOCUMENT.rst.txt#docstring-standard

In addition, we follow the guidelines of [Restructured Text] with the additional features and treatments
provided by [Sphinx].

[Restructured Text]: http://docutils.sourceforge.net/docs/user/rst/quickstart.html
[Sphinx]: http://www.sphinx-doc.org/en/stable/

### HOWTO build documentation

```sh
PYTHONPATH=$PWD make -C docs html
```

in the top directory, and then built documentation should become available
under `docs/build/html/` directory.

Additional Hints
----------------

1. For merge commits to have more informative description, add to your
   `.git/config` or `~/.gitconfig` following section:
   
        [merge]
        summary = true
        log = true
   
   and if conflicts occur, provide short summary on how they were resolved
   in "Conflicts" listing within the merge commit
   (see [example](https://github.com/repronim/repronim/commit/eb062a8009d160ae51929998771964738636dcc2)).


Quality Assurance
-----------------

It is recommended to check that your contribution complies with the following
rules before submitting a pull request:

- All public methods should have informative docstrings with sample usage
  presented as doctests when appropriate.

- All other tests pass when everything is rebuilt from scratch.

- New code should be accompanied by tests.


### Tests

`repronim/tests` contains tests for the core portion of the project, and
more tests are provided under corresponding submodules in `tests/`
subdirectories to simplify re-running the tests concerning that portion
of the codebase.  To execute many tests, the codebase first needs to be
"installed" in order to generate scripts for the entry points.  For
that, the recommended course of action is to use `virtualenv`, e.g.

```sh
virtualenv --system-site-packages venv-tests
source venv-tests/bin/activate
pip install -r requirements.txt
python setup.py develop
```

and then use that virtual environment to run the tests, via

```sh
python -m nose -s -v repronim
```

or similarly,

```sh
nosetests -s -v repronim
```

then to later deactivate the virtualenv just simply enter

```sh
deactivate
```

Alternatively, or complimentary to that, you can use `tox` -- there is a `tox.ini`
file which sets up a few virtual environments for testing locally, which you can
later reuse like any other regular virtualenv for troubleshooting.
Additionally, [tools/testing/test_README_in_docker](tools/testing/test_README_in_docker) script can
be used to establish a clean docker environment (based on any NeuroDebian-supported
release of Debian or Ubuntu) with all dependencies listed in README.md pre-installed.


### Coverage

You can also check for common programming errors with the following tools:

- Code with good unittest coverage (at least 80%), check with:

          pip install nose coverage
          nosetests --with-coverage path/to/tests_for_package

- We rely on https://codecov.io to provide convenient view of code coverage.
  Installation of the codecov extension for Firefox/Iceweasel or Chromium
  is strongly advised, since it provides coverage annotation of pull
  requests.

### Linting

We are not (yet) fully PEP8 compliant, so please use these tools as
guidelines for your contributions, but not to PEP8 entire code
base.

[beyond-pep8]: https://www.youtube.com/watch?v=wf-BqAjZb8M

*Sidenote*: watch [Raymond Hettinger - Beyond PEP 8][beyond-pep8]

- No pyflakes warnings, check with:

           pip install pyflakes
           pyflakes path/to/module.py

- No PEP8 warnings, check with:

           pip install pep8
           pep8 path/to/module.py

- AutoPEP8 can help you fix some of the easy redundant errors:

           pip install autopep8
           autopep8 path/to/pep8.py

Also, some team developers use
[PyCharm community edition](https://www.jetbrains.com/pycharm) which
provides built-in PEP8 checker and handy tools such as smart
splits/joins making it easier to maintain code following the PEP8
recommendations.  NeuroDebian provides `pycharm-community-sloppy`
package to ease pycharm installation even further.


Easy Issues
-----------

A great way to start contributing to ReproNim is to pick an item from the list of
[Easy issues](https://github.com/repronim/repronim/labels/easy) in the issue
tracker.  Resolving these issues allows you to start contributing to the project
without much prior knowledge.  Your assistance in this area will be greatly
appreciated by the more experienced developers as it helps free up their time to
concentrate on other issues.

Various hints for developers
----------------------------

### Useful tools

- While performing IO/net heavy operations use [dstat](http://dag.wieers.com/home-made/dstat)
  for quick logging of various health stats in a separate terminal window:
  
        dstat -c --top-cpu -d --top-bio --top-latency --net

- To monitor speed of any data pipelining [pv](http://www.ivarch.com/programs/pv.shtml) is really handy,
  just plug it in the middle of your pipe.

- For remote debugging epdb could be used (avail in pip) by using
  `import epdb; epdb.serve()` in Python code and then connecting to it with
  `python -c "import epdb; epdb.connect()".`

- We are using codecov which has extensions for the popular browsers
  (Firefox, Chrome) which annotates pull requests on github regarding changed coverage.

### Useful Environment Variables
Refer repronim/config.py for information on how to add these environment variables to the config file and their naming convention

- *REPRONIM_LOGLEVEL*: 
  Used for control the verbosity of logs printed to stdout while running repronim commands/debugging
- *REPRONIM_TESTS_KEEPTEMP*: 
  Function rmtemp will not remove temporary file/directory created for testing if this flag is set
- *REPRONIM_EXC_STR_TBLIMIT*: 
  This flag is used by the repronim extract_tb function which extracts and formats stack-traces.
  It caps the number of lines to REPRONIM_EXC_STR_TBLIMIT of pre-processed entries from traceback.
- *REPRONIM_TESTS_TEMPDIR*: 
  Create a temporary directory at location specified by this flag.
  It is used by tests to create a temporary git directory while testing git annex archives etc
- *REPRONIM_TESTS_NONETWORK*: 
  Skips network tests completely if this flag is set
  Examples include test for s3, git_repositories, openfmri etc
- *REPRONIM_TESTS_SSH*: 
  Skips SSH tests if this flag is **not** set
- *REPRONIM_LOGTRACEBACK*: 
  Runs TraceBack function with collide set to True, if this flag is set to 'collide'.
  This replaces any common prefix between current traceback log and previous invocation with "..."
- *REPRONIM_TESTS_NOTEARDOWN*: 
  Does not execute teardown_package which cleans up temp files and directories created by tests if this flag is set
- *REPRONIM_USECASSETTE*:
  Specifies the location of the file to record network transactions by the VCR module.
  Currently used by when testing custom special remotes
- *REPRONIM_CMD_PROTOCOL*: 
  Specifies the protocol number used by the Runner to note shell command or python function call times and allows for dry runs. 
  'externals-time' for ExecutionTimeExternalsProtocol, 'time' for ExecutionTimeProtocol and 'null' for NullProtocol.
  Any new REPRONIM_CMD_PROTOCOL has to implement repronim.support.protocol.ProtocolInterface
- *REPRONIM_CMD_PROTOCOL_PREFIX*: 
  Sets a prefix to add before the command call times are noted by REPRONIM_CMD_PROTOCOL.
- *REPRONIM_PROTOCOL_REMOTE*:
  Binary flag to specify whether to test protocol interactions of custom remote with annex
- *REPRONIM_LOG_TIMESTAMP*:
  Used to add timestamp to repronim logs
- *REPRONIM_RUN_CMDLINE_TESTS*:
  Binary flag to specify if shell testing using shunit2 to be carried out
- *REPRONIM_TEMP_FS*:
  Specify the temporary file system to use as loop device for testing REPRONIM_TESTS_TEMPDIR creation
- *REPRONIM_TEMP_FS_SIZE*:
  Specify the size of temporary file system to use as loop device for testing REPRONIM_TESTS_TEMPDIR creation
- *REPRONIM_NONLO*:
  Specifies network interfaces to bring down/up for testing. Currently used by travis.
