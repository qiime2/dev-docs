# Welcome to the QIIME 2 developer docs! 👋

To contribute, follow these steps:

1. Fork this repo.
2. Clone your forked repo onto your computer.
3. Make a new branch:
   `git checkout -b informative-branch-name`
4. Make and preview your edits.
5. Commit and push your edits, and start a pull request.

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

This should download sphinx (plus a few other dependencies). Note that you might want to run this in a qiime2 dev environment, to keep installs separate from your main computer.

To build the documentation and preview your changes, run the following command from the main repo:
```
make dirhtml
```

The output files will show up in the `build` folder. Open these html files to explore the documentation locally.

Once you're satisfied with your changes, commit your changes and push them to your repo, and then start a pull request from your repo's site. Don't commit any of the files in the `build` directory.

# Uploading a new release version to S3 (QIIME 2 Core Development Team)

Start by making a copy of the `build/dirhtml` directory & its contents (while in the `build` folder):
```
cp dirhtml 20XX.REL
```
Move this newly named directory to an easily accessible location on your machine (i.e. Downloads, etc).

Under the caporaso lab grant AWS account:

- Navigate to: **Amazon S3 -> Buckets -> qiime2-dev-docs**
- Select: **Upload -> Add folder**
  - This is where you'll select the `20XX.REL` directory from your machine. You'll then be taken to a pop-up window asking if you're sure you'd like to upload all of the files and folders within this directory, to which you'll select **Upload**.
  - You'll then see a list of all of the files (in their relative locations) in a list. As long as everything looks as expected, select **Upload** at the bottom of this page.
