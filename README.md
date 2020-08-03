# CI_Sample_Python
Sample Python web app for the CI demonstration

This Feature includes is a Python Flask app with sqlite DB embedded.

## Install

Be sure to use the same version of the code as the version of the docs you're reading. You probably want the latest tagged version, but the default Git version is the master branch.

```bash
#!/bin/bash
# clone the repository
$ git clone https://github.com/pallets/flask
$ cd flask
# checkout the correct version
$ git tag  # shows the tagged versions
$ git checkout latest-tag-found-above
$ cd examples/tutorial
```

### Create a virtualenv and activate it:

```bash
#!/bin/bash
$ python3 -m venv venv
$ . venv/bin/activate
```

Or on Windows cmd:

```bash
#!/bin/bash
$ py -3 -m venv venv
$ venv\Scripts\activate.bat
```

Install Flaskr:

```bash
#!/bin/bash
$ pip install -e .
```

## Run

```bash
#!/bin/bash
$ export FLASK_APP=flaskr
$ export FLASK_ENV=development
$ flask init-db
$ flask run
```

Or on Windows cmd:

```bash
#!/bin/bash
> set FLASK_APP=flaskr
> set FLASK_ENV=development
> flask init-db
> flask run
```

Open <http://127.0.0.1:5000> in a browser.

## Test

```bash
#!/bin/bash
$ pip install '.[test]'
$ pytest
```

Run with coverage report:

```bash
#!/bin/bash
$ coverage run -m pytest
$ coverage report
$ coverage html  # open htmlcov/index.html in a browser
```