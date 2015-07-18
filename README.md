# Community.News
Node-based news extension for Neos CMS.
Work in progress. Join if you have ideas what News extension for Neos should look like!

# Installation Instructions
By popular request we added a short introduction on how to include Community.News in your installation for testing purposes

The composer way
-----------------
As there is no final release version of Community.News yet, we did not register the package on packagist.
Therefore you have to include the repository in your composer.json file (the one in the Neos root)

"repositories": [
  {
    "type": "vcs",
    "url":  "https://github.com/Flow-Community/Community.News.git"
  }
],

Depending on the Version of Neos you are running (1.2.X) or a Dev-Version or Beta of 2.0, you'd choose your branch:

Neos 1.2.X

"require": {
   ...
   "community/news": "0.1-alpha1@dev"
},

Neos 2.0

"require-dev": {
    ...
    "community/news": "dev-master"
},

After adding the according lines to your composer.json file, run "composer update".


Quick & Dirty
--------------
Download the version needed for your installation and copy the package into /Packages/Application
