# How to become a contributor and submit patches #

## Contributor License Agreements ##

We'd love to accept your code patches! However, before we can take them, we have to jump a couple of legal hurdles.

Please fill out either the individual or corporate Contributor License Agreement.

  * If you are an individual writing original source code and you're sure you own the intellectual property, then you'll need to sign an [individual CLA](http://code.google.com/legal/individual-cla-v1.0.html).
  * If you work for a company that wants to allow you to contribute your work to Google Data Python Client Library, then you'll need to sign a [corporate CLA](http://code.google.com/legal/corporate-cla-v1.0.html).

Follow either of the two links above to access the appropriate CLA and instructions for how to sign and return it. Once we receive it, we'll add you to the official list of contributors and be able to accept your patches.

## Submitting Patches ##

  1. Join Google Data Python Client Library Contributors [discussion group](http://groups.google.com/group/gdata-python-client-library-contributors).
  1. Decide which code you want to submit. A submission should be a set of changes that addresses one issue in the Google Data Python Client Library [issue tracker](http://code.google.com/p/gdata-python-client/issues/list). Please don't mix more than one logical change per submittal, because it makes the history hard to follow. If you want to make a change that doesn't have a corresponding issue in the issue tracker, please create one.
  1. Also, coordinate with team members that are listed on the issue in question. This ensures that work isn't being duplicated and communicating your plan early also generally leads to better patches.
  1. Ensure that your code adheres to the [Google Data Python source code style](http://code.google.com/p/gdata-python-client/wiki/StyleGuide).
  1. Ensure that there are unit tests for your code. For templates to help you get started with new unit tests, see the CodeTemplates page.
  1. Sign a Contributor License Agreement.
  1. Run the `upload-diffs.py` script provided in the project source code to send your diffs to http://codereview.appspot.com where it will be reviewed before being submitted. The `upload-diffs.py` script is just a modified version of `upload.py` from the [Rietveld](http://code.google.com/p/rietveld/) project with some defaults changed to make it easier to use on this project. See http://code.google.com/p/rietveld/wiki/CodeReviewHelp for more details on how `upload-diff.py` works.