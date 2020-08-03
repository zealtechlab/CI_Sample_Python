# CI_Sample_Python

Sample Python web app for the CI demonstration

This Feature includes is a Python Flask app with sqlite DB embedded.

## Launch app in vscode

--> Select Run Menu --> Add Configuration
a new folder named .vscode with a launch json file will be created.

select Flask app launch configuraiton to include the flask run option

This will include the basic run.

now use run either from left nav menu or from top menu.

Similariy configure tests in vscode.
First select the Test icon on left nav and click Run all tests, this may fail.
Regardless of the failure, select the test status from the status bar, to configure tests.
Select the project folder and click configure tests and select pytest (use existing config file) and the tests will reload.

Now run the tests again and check the results.

## Below instructions for pure command line hackers or the raw/terminal way of dealing flask and pytest

### Install

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

#### Create a virtualenv and activate it

```bash
#!/bin/bash
$ python3 -m venv venv
$ . venv/bin/activate
```

#### Venv on Windows cmd

```bash
#!/bin/bash
$ py -3 -m venv venv
$ venv\Scripts\activate.bat
```

### Install Flaskr

```bash
#!/bin/bash
$ pip install -e .
```

### Run

```bash
#!/bin/bash
$ export FLASK_APP=flaskr
$ export FLASK_ENV=development
$ flask init-db
$ flask run
```

#### Run on Windows cmd

```bash
#!/bin/bash
> set FLASK_APP=flaskr
> set FLASK_ENV=development
> flask init-db
> flask run
```

Open <http://127.0.0.1:5000> in a browser.

### Test

```bash
#!/bin/bash
$ pip install '.[test]'
$ pytest
```

#### Run with coverage report

install coverage

```bash
#!/bin/bash
$ conda install -c anaconda coverage
```

```bash
#!/bin/bash
$ coverage run -m pytest
$ coverage report
$ coverage html  # open htmlcov/index.html in a browser
```
