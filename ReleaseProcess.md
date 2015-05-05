# Overview #

Releasing a new download requires a few steps to be performed. For each release, the changes are listed in the release notes, pydocs are updated to reflect changes in the code, and the library source code, tests, etc. are packaged up in tar.gz files and .zip archives. The archives are then added to the Downloads section of this project. See detailed step by step instructions below.

# Steps #

  1. Create a new bug to track this release.  An example to follow is [here](http://code.google.com/p/gdata-python-client/issues/detail?id=406).
    1. Browse to the [New Issue page](http://code.google.com/p/gdata-python-client/issues/entry).
    1. Select the Release template.
    1. In the summary, enter "Release X.Y.Z", replacing X.Y.Z appropriately.
    1. In the description, enter in the version you're releasing, the freeze date at which you'll cut the release, and the date which you'll do the release and publish the actual release packages.
    1. Make sure that you're marked as the owner, since you're doing the release.
    1. If the release is blocked on any other bugs, make sure they're listed in the Blocked On field.
    1. Submit the issue.
  1. Once the issue is submitted, and you're ready to cut the release, add a comment to the issue giving the exact commit hash at which you're cutting the release.  This ensures people know exactly what is and is not in the release.
  1. Write release notes for the new release in [RELEASE\_NOTES.txt](http://code.google.com/p/gdata-python-client/source/browse/RELEASE_NOTES.txt). `hg log --date "2012-01-11 to 2012-04-20" --style compact -M`
  1. Update the version number in [setup.py](http://code.google.com/p/gdata-python-client/source/browse/setup.py) and in the user agent constant `USER_AGENT` in [src/atom/http\_interface.py](http://code.google.com/p/gdata-python-client/source/browse/src/atom/http_interface.py) and [src/atom/client.py](http://code.google.com/p/gdata-python-client/source/browse/src/atom/client.py).
  1. Regenerate the pydoc documentation pages.
    1. If any new files have been added in this release, then list them in [pydocs/generate\_docs](http://code.google.com/p/gdata-python-client/source/browse/pydocs/generate_docs).
    1. Run the [pydocs/generate\_docs](http://code.google.com/p/gdata-python-client/source/browse/pydocs/generate_docs) script from within the pydocs directory.
  1. Add new packages and files to the indexes so that they will be included in the archives and installed.
    1. ~~Edit the [MANIFEST](http://code.google.com/p/gdata-python-client/source/browse/MANIFEST) file, which lists all of the files which should be included in the .zip and .tar.gz archives. Run the `make-release.sh` script to find missing files.~~ MANIFEST.in does this automatically
    1. Edit the list of packages in [setup.py](http://code.google.com/p/gdata-python-client/source/browse/setup.py). This list determines which packages will be compiled and installed on the user's system.
  1. Run the unit tests, for more details see RunningTests.
  1. Tag the version in mercurial: `$ hg tag 2.0.XX`
  1. Commit everything that's changed in your local copy.  Push your changes.
  1. Generate the download archives.
    1. Run `python setup.py sdist --formats=gztar,zip` which will create `gdata.py-<version.number>.zip` and `.tar.gz` files in the `dist` directory.
    1. Upload the archives to the project's downloads page.
  1. Update the [pypi](http://pypi.python.org/pypi) (Python Package Index) listing for gdata by running `python setup.py register`.
  1. Update the release bug you created at the beginning of this, noting the tag of the commit containing all of the updated release files.
  1. Update the release bug, marking it as fixed.
  1. Send an email to the list announcing the release.