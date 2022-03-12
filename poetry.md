# Poetry Package management

The traditional way of setting up a python package isn't too hard with `setup.py`, `twine` and friends but it can be fiddly.

An alternative I've enjoyed far more for smaller new projects like `torchprep` is this

```sh
# Create package directory, tests and README
poetry create new_package

# Setup package name, owner and version
poetry init

# Add dependencies
poetry add requests torch ..

# Build and install a package locally
poetry install

# Upload a package to pypi after supplying credentials
poetry publish
```

## poetry create

It's easy to make all your work a bunch of scripts gated behind `if __name__ == "__main__` but if there was a good reason for you to writee a script in the first place there may be a good reason for someone else to use it so it's best to package up and test your code early which is exactly what `poetry create` does.


## testing
Any major functionality you have should be tested in a file with the name `test_*.py` where each function in that file would have some function with an assert statement like

```python
def test_identity():
    assert 1 == 1 
```

Then running your test suite is as simple as running `pytest .` at the root directory of your project, you can integrate something like this easily within a Github action and make it run on any commit so contributionsn can easily be accepted without breaking any functionality.

## Publishing
Whenever you build a python project you will end up a with a local file in your directory called `your_package.whl` which you can install by running `pip install your_package.whl`, you could theoretically move that `.whl` file on some remote storage like S3 but uploading them to pypi means your users can just run `pip install your_package`.

Generally `setup.py` process is a bit more fiddly since you setup a configuration within a Python object as opposed to a simpler CLI based process.
