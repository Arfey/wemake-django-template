Testing
=======

We try to keep our quality standards high.
So, we use different tools to make this possible.

We use `mypy <http://mypy-lang.org/>`_ for optional
static typing.
We run tests with `pytest <https://pytest.org/>`_ framework.
We use `pre-commit <https://pre-commit.com/>`_
to run all checks locally before pushing a commit to CI.


pytest
------

``pytest`` is the main tool for test discovery, collection, and execution.
It is configured inside ``setup.cfg`` file.

We use a lot of ``pytest`` plugins that enhance our development experience.
List of these plugins is available inside ``pyproject.toml`` file.

We also kindly ask ``pytest`` to run our linting process via ``pytest-flake8``
plugin. See :ref:`linters` to know what style checks we use.

Plugins
~~~~~~~

We use different ``pytest`` plugins to make our testing process better.
Here's the full list of things we use:

- `pytest-flake8`_ - plugin to run ``flake8`` checks alongside with tests
- `pytest-django`_ - plugin that introduce a lot of ``django`` specific
  helpers, fixtures, and configuration
- `pytest-cov`_ - plugin to measure test coverage
- `pytest-randomly`_ - plugin to execute tests in random order and
  also set predictable random seed, so you can easily debug
  what went wrong for tests that rely on random behavior
- `pytest-deadfixtures`_ - plugin to find unused or duplicate fixtures
- `pytest-timeout`_ - plugin to raise errors for tests
  that take too long to finish, this way you can control test execution speed
- `pytest-testmon`_ - plugin for `Test Driven Development`_ which executes
  tests that are affected by your code changes

.. _pytest-flake8: https://github.com/tholo/pytest-flake8
.. _pytest-django: https://github.com/pytest-dev/pytest-django
.. _pytest-cov: https://github.com/pytest-dev/pytest-cov
.. _pytest-randomly: https://github.com/pytest-dev/pytest-randomly
.. _pytest-deadfixtures: https://github.com/jllorencetti/pytest-deadfixtures
.. _pytest-timeout: https://pypi.org/project/pytest-timeout
.. _pytest-testmon: https://github.com/tarpas/pytest-testmon
.. _`Test Driven Development`: https://en.wikipedia.org/wiki/Test-driven_development

Tweaking tests performance
~~~~~~~~~~~~~~~~~~~~~~~~~~

There are several options you can provide or remove to make your tests faster:

- You can use ``pytest-xdist`` together with
  ``-n auto``  to schedule several numbers of workers,
  sometimes when there are a lot of tests it may increase the testing speed.
  But on a small project with a small amount of test it just
  gives you an overhead, so removing it (together with `--boxed`)
  will boost your testing performance
- If there are a lot of tests with database access
  it may be wise to add
  `--reuse-db option <https://pytest-django.readthedocs.io/en/latest/database.html#example-work-flow-with-reuse-db-and-create-db>`_,
  so ``django`` won't recreate database on each test
- If there are a lot of migrations to perform you may also add
  `--nomigrations option <https://pytest-django.readthedocs.io/en/latest/database.html#nomigrations-disable-django-1-7-migrations>`_,
  so ``django`` won't run all the migrations
  and instead will inspect and create models directly
- Removing ``coverage``. Sometimes that an option.
  When running tests in TDD style why would you need such a feature?
  So, coverage will be calculated when you will ask for it.
  That's a huge speed up
- Removing linters. Sometimes you may want to split linting and testing phases.
  This might be useful when you have a lot of tests, and you want to run
  linters before, so it won't fail your complex testing pyramid with a simple
  whitespace violation


mypy
----

Running ``mypy`` is required before any commit:

.. code:: bash

    mypy server

This will eliminate a lot of possible ``TypeError`` and other issues.
However, this will not make code 100% safe from errors.
So, both the testing and review process are still required.

``mypy`` is configured via ``setup.cfg``.
Read the `docs <https://mypy.readthedocs.io/en/latest/>`_
for more information.


pre-commit
----------

We are using several pre-commit hooks to make sure everything works just fine.
Before you can run hooks, you need to have the pre-commit package installed.
It comes bundled in our project requirements (specified in ``pyproject.toml``).
To setup hooks after installing all the dependencies run:

.. code:: bash

  pre-commit install


You will now see the test results before any commit.
Before each commit the same testing routing as in CI
will be run on your machine.
Because we don't want to waste CI's and people's time
dealing with the fallen builds.
