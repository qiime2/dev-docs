Welcome to the QIIME 2 developer docs!

To contribute, follow these steps:

1. Fork this repo
2. Clone your forked repo onto your computer
3. Make a new branch
   `git checkout -b informative-branch-name`
4. Make and preview your edits
5. Commit and push your edits, and start a pull request

# Previewing edits

This documentation is built by [sphinx](http://www.sphinx-doc.org/en/master/), which you can also use to make a local copy of the docs to preview on your computer.

First, you need to have all the required packages installed. Start by going to https://packages.qiime2.org/qiime2/<20XX.Y>/amplicon/released (where <20XX.Y> is the current release epoch).
Check on the latest versions for qiime2 and q2cli (these are the two QIIME 2 packages you'll need in your conda environment).
Create a new conda environment with the following packages/channels:

```
conda create -n dev-docs -c https://packages.qiime2.org/qiime2/<20XX.Y>/amplicon/released -c conda-forge -c bioconda -c defaults qiime2=<20XX.Y.ver> q2cli=<20XX.Y.ver> sphinx
```
Where 20XX.Y is the current release epoch, and 20XX.Y.ver is the version found on packages.qiime2.org for qiime2 and q2cli, respectively.

Once the environment has been created, you'll activate it with:
```
conda activate dev-docs
```

From the main dev-docs repo, run:
```
pip install -r requirements.txt
```

This will install any separate dependencies required outside of sphinx.

To build the documentation and preview your changes, run the following command from the main repo:
```
make dirhtml
```

The output files will show up in the `build` folder. Open these html files to explore the documentation locally.

Once you're satisfied with your changes, commit your changes and push them to your repo, and then start a pull request from your repo's site. Don't commit any of the files in the `build` directory.
