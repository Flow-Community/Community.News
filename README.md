#Readme under construction

#Community.News
Node-based news extension for Neos CMS, serving as an example on what your own implementation could look like.

##1. Introduction
Community.News is a node-based news extension for Neos CMS. While you can (and are of course free to) use this package as-is out of the box, the main intention behind this package is to give interested users a possible headstart on what a news extension could look like and how certain use-cases (like node based news, custom FlowQueries, etc.) can be implemented.

Besides commenting the code at various key-lines, you can find general information about the parts of this package in this Readme.

##2. Installation

###2.1 Installation via Packagist (nice and tidy)
Community.News is listed on Packagist, therefore you only need to include the requirement in the *composer.json* of your Neos installation.
If you want to use the latest stable version (for the current Neos 2.X branch), we recommend the CLI/shell command:
```
composer require community/news:~1.0
```

If you want to test your Installation with the newest developments before they are released and marked as stable (and also can abide encountering a problem or two in the process) you can use:
```
composer require community/news:dev-master
```

After setting the new requirement just run ``` composer update ``` in your shell/cmd as usual.

##2.2 Installation via manual copying (not so nice and tidy)
If you can't use the composer way for whatever reason (rights management on a shared host, etc.) you can download the package and copy it to the */Packages/Application* directory on your server.
Neos will automatically detect the package there and include it by default.

## 3. Configuration

###3.1 Settings.yaml
This file is located in the ```Configuration``` Folder. You do not have to explicitely load this after installing the package, because "Convention over configuration" will take care of that for us. (in other words: "if we put it in the right place with the right name the system will find it automatically")

####3.1.1 The Neos configuration part
The ```typoScript``` configuration entry makes sure, that the TypoScript2 root file - found in the directory *Resources/Private/TypoScript/Root.ts2* - gets included automatically when loading the package.

The ```nodeTypes``` part creates a new NodeType group for the nodes defined in our NodeTypes.yaml (see below) inside the "Add" dialog in the Neos backend.

####3.1.2 The Community configuration part
The ```newsList``` and ```newsEntry``` parts just offer us a possibility to define a couple of constants that we will use in our TypoScript2 files. That way it is possible to easily alter key behaviours (like "the maximum number of items per page") without having to change the TS2 code.

###3.2 NodeTypes.yaml
Inside NodeTypes.yaml we define our own NodeTypes that get used. You most probably will have done this for your own site already, so we will only highlight some entries here.
If you want to know more about general configuration possibilities for NodeTypes, we recommend https://neos.readthedocs.org/en/stable/CreatingASite/NodeTypes/NodeTypeDefinition.html

####3.2.1 Abstract News
```
'Community.News:AbstractNews':
  abstract: TRUE
```
We did introduce AbstractNews to reflect on what we consider to be the essential parts of a news entry. As you can learn from NodeTypes.yaml, the ```'Community.News:News'``` NodeType just inherits from the AbstractNews, but you could encounter a scenario where you want to have different types of news, that still can be fetched together by having the same base type.
(e.g.: You want to differentiate between News and Events, where Events just are a kind of News that also have a set time and place - you would still be able to fetch all News AND Events by querying for the abstract base type)

#### 3.2.2 Mixins
```
'Community.News:Author':
  superTypes:
   'TYPO3.Neos:Document': TRUE
   'TYPO3.Neos.NodeTypes:ImageMixin': TRUE
```
Mixins allow for an easy inclusion of properties that are standardized in Neos (like adding an image to a NodeType).
This has the advantage that you have a central point (the mixin definition) where a change gets reflected in all Nodes that use this mixin.
(You can find the mixins in the Package *TYPO3.Neos.NodeTypes* in the file *Configuration/NodeTypes.Mixins.yaml*

#### 3.2.3 References
```
 author:
    type: reference
    ui:
      label: 'Author'
      inspector:
        group: 'related'
        editorOptions:
          nodeTypes: ['Community.News:Author']
```
This part of the AbstractNews NodeType defines that each news entry has a related author.
Mind the word "reference" which states that there is only one vs the entry "references" in the categories property of this NodeType, which states that there can be multiples assigned.

## 4. TypoScript
All files mentioned can be found in the folder *Resources/Private/TypoScript/* or according subfolders.
We use TypoScript2 to get the news nodes (like news, categories, authors, etc.) that have been created by editors/admins in the backend and pass them to the Fluid templates (see below) for rendering. You can find details about TypoScript2 at: https://neos.readthedocs.org/en/1.2/IntegratorGuide/InsideTypoScript.html

As good practice we did create two subfolders - putting all TypoScript files that help us rendering NodeTypes in to a *NodeTypes* folder, while objects that have no NodeType representation and which get rendered "on the fly" get put in a *TypoScriptObjects* directory.
In addition we name the TypoScript (ts2) files after the NodeType name where applicable. ('Community.News:Author' becomes Author.ts2 - the full path would be: Community.News/Resources/Private/TypoScript/NodeTypes/Author.ts2)

###4.1 Root.ts2
As mentioned above - this file is auto-loaded due to our Settings.yaml and includes all needed TypoScript files in turn.

###4.2 Author.ts2, Category.ts2

to be continued ... ;)






