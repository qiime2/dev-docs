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

First, you need to have all the requirements download. From the main repo, run

```
pip install -r requirements.txt
```

This should download sphinx (plus a few other dependencies). Note that you might want to run this in a qiime2 dev environment, to keep installs separate from your main computer.

To build the documentation and preview your changes, run

```
make html
```

from the main repo. The output files will show up in the `build` folder. Open these html files to explore the documentation locally.

Once you're satisfied with your changes, commit your changes and push them to your repo, and then start a pull request from your repo's site. Don't commit any of the files in the `build` directory.
