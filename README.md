## Question about Importing Modules under Pytest

I'm converting some tests written earlier for Unittest and am having trouble with import statements when running them with Pytest.

As an example, in the older code the module `create.py` in directory `lib` might be imported as

```python
import lib.create
```

but this syntax usually raises errors when I run the code with Pytest.

**Solution found**: see [below](#solution).

---

I'd appreciate any advice. I've placed a minimal example in the files in this repository. After cloning, enter the set up under Python 2.7 with:

```bash
cd pytest_import_question
virtualenv v_env27
. v_env27/bin/activate
pip install -Ur requirements.txt
py.test tests
```

Running Pytest from within `pytest_import_question` typically generates an `ImportError`:

```bash
$ py.test tests
============================= test session starts ==============================
platform darwin -- Python 2.7.6 -- py-1.4.26 -- pytest-2.7.0
rootdir: .../pytest_import_question/tests, inifile:
collected 0 items / 1 errors

==================================== ERRORS ====================================
_______________________ ERROR collecting test_caller.py ________________________
tests/test_caller.py:3: in <module>
    import lib.create
E   ImportError: No module named lib.create
=========================== 1 error in 0.01 seconds ============================
```

Other comments:

 1. Directory structure is as follows:

    ```bash
    pytest_import_question/
        lib/
            __init__.py
            create.py
        tests/
            test_caller.py
    ```

 1. File `__init__.py` is empty. The other two files are a test file that calls `lib/create.py`:

    ```python
    # test_caller.py

    import lib.create

    def test_show():
        assert True
    ```

    and `lib/create.py` itself, which contains a minimal function:

    ```python
    # create.py

    def create():
        pass
    ```

 1. I have tried updating the `sys.path` list in `test_caller.py`:

    ```python
    import sys
    sys.path.append('lib')
    ```

    or

    ```python
    import sys
    sys.path.extend(['..', 'lib'])
    ```

    but the result is the same.

 1. The example works with `nose`, however:

    ```bash
    $ nosetests tests/
    .
    ----------------------------------------------------------------------
    Ran 1 test in 0.003s
    ```
    
    Why not with Pytest? Is there a workaround?

---

Thanks for any suggestions as to how to deal with this.

### Solution

The solution is include a `setup.py` file at the top-level directory of the project, and then to use `pip` to install it in "editable mode"

```bash
pip install -e .
```

That will generate a directory whose name ends in `.egg-info` and which contains a number of configuration files. After that, running with Pytest takes place without difficulties.

[end]