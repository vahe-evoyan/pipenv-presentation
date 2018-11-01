name: inverse
layout: true
class: center, middle, inverse

---

# pipenv

### Goodbye virtualenv... and Welcome Pipenv

.footnote[[@evoyan](https://twitter.com/evoyan)]

---
layout: false

.left-column[
# Intro

## Classic Way
]

.right-column[

How did we manage package management until today.

- Some where installing packages globally via `easy_install`, `pip` or even via OS package manager, such as `apt-get`.
- To know which packages should be installed we used `pip` along with it's magical `requirements.txt` in the projects.
  * **Pros:** easy to generate via `pip freeze`
  * **Cons:** difficult to maintain, easy to break
  ### Problems
    - There is a big chance to break the dependencies, when changing some version in the `requirements.txt` manually (assuming it was generated via `pip freeze`).
    - It is not clear which dependencies are for the project, which are the sub-dependencies and which are used only for development.
    - It may broke on different installations when it's not managed via `pip feeze`, i.e. there are no fixed versions of dependencies and sub-dependencies.
]

---
layout: false

.left-column[
# Intro

## Classic Way
## Improved Way
]

.right-column[

With all these problems with `pip` and `virtualenv` here's probably the best way you can organize you package versions.

- Keep a directory of requirement files for different environments.
```
    /myproject
        |_ requirements
            |_ base.txt
            |_ production.txt
            |_ development.txt
```
- Include the files from one another.
```
-r base.txt
requests[security]
```
- Have two versions of the files for work and for deployment.
```
    |_ requirements
        |_ production.txt
        |_ production-to-freeze.txt
        ...
```

]

---
layout: false

.left-column[
# Intro

## Classic Way
## Improved Way
## A Better Pip Workflow™
]

.right-column[

Keeping two versions of files solves the deployment issues. See Kenneth Reitz' post about [A Better Pip Workflow™](https://www.kennethreitz.org/essays/a-better-pip-workflow).

- `production-to-freeze.txt` is used to specify your top-level dependencies, and any explicit versions you need to specify.
```
-r base.txt
requests[security]
flask
```
- `production.txt` contains the output of `pip freeze` command and has exact versions of all dependencies and sub-dependencies.
```
cffi==1.5.2
cryptography==1.2.2
enum34==1.1.2
Flask==0.10.1
gunicorn==19.4.5
idna==2.0
ipaddress==1.0.16
...
```

]

---
layout: false

.left-column[
# Intro

## Classic Way
## Improved Way
## A Better Pip Workflow™
## virtualenv
]

.right-column[

Virtualenv has one basic command :-)

```
$ virtualenv ENV
```

Then you activate/deactivate it by running the corresponding scripts.

```
$ source /path/to/ENV/bin/activate
```
then
```
(ENV) $ deactivate
```

Or, you simply use `virtualenvwrapper` or `vf` (for `fish`).

```
$ workon ENV
(ENV) $ python --version
(ENV) $ deactivate
```

]

---
name: inverse
layout: true
class: center, middle, inverse
---

# pipenv

## What is it and why should I be using it?

---
layout: false

.left-column[
# pipenv

## Workflow
]

.right-column[

Install from Pipfile, if there is one, or, add a package to your new project:

```
$ pipenv install [<package>]
```

This will create a Pipfile if one doesn’t exist and create a `virtualenv` in a public location.

If one does exist, it will automatically be edited with the new package you provided.

Next, activate the Pipenv shell:

```
$ pipenv shell
```

This will spawn a new shell subprocess, which can be deactivated by using `exit`.

Upgrade is easy too :-)

```
$ pipenv update [<package>]
```

]

---
layout: false

.left-column[
# pipenv

## Workflow
## Pipfile[s]
]

.right-column[

Pipfile contains all your abstract dependency declarations, package index configurations and python versions.

* **Hint!** You can specify versions of a package using the Semantic Versioning scheme.

```
$ pipenv install "records>0.5.0"
```

```
[[source]]
url = "https://pypi.python.org/simple"
verify_ssl = true
name = "pypi"

[packages]
requests = "*"
records = '>0.5.0'
django = { git = 'https://github.com/django/django.git', ref = '1.11.4', editable = true }
"e682b37" = {file = "https://github.com/divio/django-cms/archive/release/3.4.x.zip"}
"e1839a8" = {path = ".", editable = true}
pywinusb = { version = "*", os_name = "=='nt'", index="pypi"}


[dev-packages]
pytest = "*"
```

]

---
layout: false

.left-column[
# pipenv

## Workflow
## Pipfile[s]
]

.right-column[

Pipfile.lock is generated when you run `pipenv install` or `pipenv lock`.

- It is a JSON file with all **exact versions** of the dependencies and sub-dependencies. This file is used to install the packages for deployments.
- It contains the **sha256 hashes** of each downloaded package, so the following installations will guarantee the package integrity in compromised networks or untrusted PyPI endpoints.

You can see a lot of similarity with `npm`, `yarn` and other modern package mangers for different languages/platforms. Pipenv includes all the best features of these.

]


---
name: inverse
layout: true
class: center, middle, inverse
---

# Migrate now! It's easy.

```
pipenv install -r path/to/requirements.txt
```

---
layout: false

.left-column[
# pipenv

## Workflow
## Pipfile[s]
## Useful Commands
]

.right-column[

- Check for outdated dependencies

```
$ pipenv update --outdated
```

- Specify the version of Python

```
$ pipenv --python 3.6
```

- Handling editable dependencies

```
$ pipenv install --dev -e .

$ cat Pipfile
...
[dev-packages]
"e1839a8" = {path = ".", editable = true}
...
```

- Uninstall the package

```
$ pipenv uninstall
```

]

---
layout: false

.left-column[
# pipenv

## Workflow
## Pipfile[s]
## Useful Commands
]

.right-column[

- Enforce that your `Pipfile.lock` is up to date

```
$ pipenv install --deploy
```

or, install directly from `Pipenv.lock`

```
$ pipenv sync
```

- Generate a `requirements.txt`

```
$ pipenv lock -r
chardet==3.0.4
requests==2.18.4
certifi==2017.7.27.1
idna==2.6
urllib3==1.22
```

- Check for security vulnerabilities

```
$ pipenv check
```
]

---
layout: false

.left-column[
# pipenv

## Workflow
## Pipfile[s]
## Useful Commands
## Tips
]

.right-column[

There is a lot more about pipenv on the official [Read The Docs](https://pipenv.readthedocs.io) page. But let's concentrate on practical side of it.

- Keep both `Pipfile` and `Pipfile.lock` in version control (except when multiple versions of Python are being targeted).
- Always specify your target Python version in your Pipfile’s `[requires]` section.
- `WORKON_HOME` environment variable is honored by Pipenv as well.
- In case you're moving your project directory frequently, create the `virtualenv` inside the project by setting the `PIPENV_VENV_IN_PROJECT`. The global virtualenv name is generated from the hash of the path to your project.
- Use custom scripts!
```
$ pipenv run hello
```
Add the commands into Pipfile
```
[scripts]
hello = "python -c \"print('Hello World!')\""
```
]

---
layout: false

.left-column[
# pipenv

## Workflow
## Pipfile[s]
## Useful Commands
## Tips
]

.right-column[

- Use environment variables in your Pipfile
```
[[source]]
url = "https://${PYPI_USERNAME}:${PYPI_PASSWORD}@my_private_repo.example.com/simple"
verify_ssl = true
name = "pypi"
```
- If you are a Fish user, add the auto-completion.
```
eval (pipenv --completion)
```
]

---

name: last-page
template: inverse

## That's all folks!

Heavily based on official Pipenv [Read The Docs](https://pipenv.readthedocs.io/en/latest/).
