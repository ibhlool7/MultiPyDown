## For remains in mind

### `__main__.py`
- acts as the entry point of our program and takes care of the main flow
- Indeed, when running a package as a script with -m as above, Python executes the contents of the `__main__.py` file


- Notice that `main()` is called on the last line, indeed we need a clock of code like this <br/>
```python
if __name__ == "__main__":
    main() # any function
```
---
###`importlib.resources` 
- is used to import non-code (or resource) files from a package without having to worry about the full file path. It is especially helpful when publishing packages to PyPI where resource files might end up inside binary archives.
---
### `__init__.py`
- represents the root of your package.
- It should usually be kept quite simple, but it’s a good place to put package constants, documentation, and so on.

**NOTE**
- The special variable __version__ is a convention in Python for adding version numbers to your package
``` 
__version__ = "1.0.0"
```
- Variables defined in `__init__.py`become available as variables in the package namespace:
```
>>> import reader
>>> reader.__version__
'1.0.0'
```
---

###  `-m` option 

- The python interpreter program has an -m option that allows you to specify a module name instead of a file name. For instance, if you have a script called hello.py, the following two commands are equivalent:
```
$ python hello.py
Hi there!

$ python -m hello
Hi there!
```
- One advantage of the latter is that it allows you to call modules that are built into Python as well
-Another advantage of using -m is that it works for packages as well as modules.indeed we can call out package as modules and run its main procedural like `django` scripts

**Note**

- how does it work? It looks for a file named `__main__.py`. If such a file exists, it is executed. If `__main__.py` does not exist, then an error message is printed.
- If you are creating a package that is supposed to be executed, you should include a `__main__.py` file. 
---
### Package naming
- we can use different names for your package on PyPI and when importing. However, if you use the same name or very similar names, then it will be easier for your users.
for example we can distribute our package as `MultiPyDown` but use it lib as `pydown`.
---
### `setup.py`
- In order for your package to be uploaded to PyPI, you need to provide some basic information about it. This information is typically provided in the form of a setup.py file
- The setup.py file should be placed in the top folder of your package. A fairly minimal setup.py for reader looks like this:
```python
import pathlib
from setuptools import setup

# The directory containing this file
HERE = pathlib.Path(__file__).parent

# The text of the README file
README = (HERE / DEVELOPER.md).read_text()

# This call to setup() does all the work
setup(
    name="realpython-reader",
    version="1.0.0",
    description="Read the latest Real Python tutorials",
    long_description=README,
    long_description_content_type="text/markdown",
    url="https://github.com/realpython/reader",
    author="Real Python",
    author_email="office@realpython.com",
    license="MIT",
    classifiers=[
        "License :: OSI Approved :: MIT License",
        "Programming Language :: Python :: 3",
        "Programming Language :: Python :: 3.7",
    ],
    packages=["reader"],
    include_package_data=True,
    install_requires=["feedparser", "html2text"],
    entry_points={
        "console_scripts": [
            "realpython=reader.__main__:main",
        ]
    },
)
```
- `install_requires`  is used to list any dependencies your package has to third party libraries. The reader depends on feedparser and html2text, so they should be listed here.
- `entry_points` is used to create scripts that call a function within your package. In our example, we create a new script realpython that calls main() within the reader/__main__.py file.
- `url` In the setup.py example above, url is used to link to the reader GitHub repository.
- `include_package_data` The include_package_data argument controls whether non-code files are copied when your package is installed.

**Note**

- The parameters that are 100% necessary in the call to setup() are the following:
    
    - name: the name of your package as it will appear on PyPI
    - version: the current version of your package
    - packages: the packages and subpackages containing your source code
- You also need to specify any subpackages. In more complicated projects, there might be many packages to list. To simplify this job, setuptools includes find_packages().
---
### Versioning Your Package
 - if you want to update your package on PyPI, you need to increase the version number first. This is a good thing, as it guarantees reproducibility: two systems with the same version of a package should behave the same.
 - use it [semantic Versioning](https://semver.org/lang/fa/)
 - You may need to specify the version in different files inside your project including `__init__.py` and setuptools config.
 

# Publishing package steps
1. install `twine`
    - Uploading Your Package
    - It can also check that your package description will render properly on PyPI using `twine check dist/*`
        - While it won’t catch all problems you might run into, it will for instance let you know if you are using the wrong content type
2. Packages on PyPI are not distributed as plain source code. Instead, they are wrapped into distribution packages.The most common formats for distribution packages are source archives and Python wheels.
    - To create a source archive and a wheel for your package, you can run the following command:
        `$ python setup.py sdist bdist_wheel`
    - This will create two files in a newly created dist directory, a source archive and a wheel:
        ```
        └── dist/
            ├── realpython_reader-1.0.0-py3-none-any.whl
            └── realpython-reader-1.0.0.tar.gz
        ```
    - testing package using `tar tzf realpython-reader-1.0.0.tar.gz`
3. test uploading to TestPyPi using `twine upload --repository-url https://test.pypi.org/legacy/ dist/*`
4. real upload `twine upload dist/*`
5. for update `twine upload --skip-existing dist/*`
**Note**

- On Windows, the source archive will be a .zip file by default. You can choose the format of the source archive with the --format command line option.