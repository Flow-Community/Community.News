#Readme under construction

#Community.News
Node-based news extension for Neos CMS, serving as an example on what your own implementation could look like.
Dmitri Pisarev (@dimaip), Florian Weiss (@WeissheitenWien), Community Contributors (thank you!)

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

### 4.1 TypoScript object autogeneration

Neos does generate a default TypoScript object for each defined NodeType automatically. The objects created this way are named like the NodeType. (E.g: The NodeType "Community.News:Author" becomes the TypoScript object "Community.News:Author")
For each property you defined in NodeTypes.yaml, you'll also find an automatically generated TypoScript property. (e.g.: You don't have to specify ```name = ${q(node).property('name')}``` yourself, because Neos will do that in the background and the Fluid variable ```{name}``` is therefore available by default. (see .\Resources\Private\Templates\NodeTypes\Author.html)

### 4.2 Prototyping

When checking out the code you'll often encounter the line ```prototype(something)```. You might know that from JavaScript and similar to the use there prototyping allows us to *define properties for all instances of a certain TypoScript object type*
(e.g.: prototype(My.Package:SomeNode){ this = 'that' } would result in all TypoScript objects of type "My.Package:Somenode" having a property "this" with value "that" (no matter if they were created before or after this line is included)
You can find more information about prototyping at: http://neos.readthedocs.org/en/stable/CreatingASite/TypoScript/InsideTypoScript.html

### 4.3 Using Flowpack.Listable:List for listing functionality

You'll find, that the ```Community.News:AbstractList``` TypoScript object inherits from ```Flowpack.Listable:List```, which provides a general reusable and reliable way to display lists of arbitrary nodes (like news, articles, etc.).
As good practice the concern of listing items is separated from the concern of rendering items and therefore relying on this package provides a solid foundation for listing, while allowing us to take care of rendering the desired content.
The package itself and according details can be found at: https://github.com/Flowpack/Flowpack.Listable - the use cases of implementing functionality are discussed below at the specific points of interest.

### 4.4 Using "PrimaryContent" to render nodes automatically 

When browsing the TS2 files, you will encounter a certain code part multiple times with slight variations (in this case for the author):  
```
# Attach to `PrimaryContent` to render nodes of this type automatically
prototype(TYPO3.Neos:PrimaryContent) {
    author{
        @position = 'before default'
        condition = ${q(node).is('[instanceof Community.News:Author]')}
        type = 'Community.News:Author'
    }
}
```
This will result in Nodes of the specific type being rendered automatically. (generally speaking: "Your rendered node will replace the main content automatically"). A very handy approach for nodes that are listed in the tree, but should be able to render "on their own".
Let's look at *.\Packages\Sites\TYPO3.NeosDemoTypo3Org\Resources\Private\TypoScript\Root.ts2* of the Neos introduction package. Chances are great that you render your main content the same way as this demo package does, that is via:
```
content {
		// Default content section
		main = PrimaryContent {
		    nodePath = 'main'
		}
}
```
You can find the default specification of ```PrimaryContent``` in *.\Packages\Application\TYPO3.Neos\Resources\Private\TypoScript\Prototypes\PrimaryContent.ts2* and easily see that it inherits from ```TYPO3.TypoScript:Case```
By default there is only one option (which states that the developer has to define a node path which points to a ContentCollection from where the nodes are fetched.
We just extend this ```TYPO3.TypoScript:Case``` with a new option, which is chosen on the condition, that the current node is an instance of a specific NodeType (in this case ```Community.News:Author```).
If this condition is matched, the object becomes the type specified and is rendered accordingly.

### 4.4 TypoScript files overview

In the following we highlight details about specific TS2 file codelines that aren't covered by the general information above.

### 4.4.1 Root.ts2
As mentioned before - this file is auto-loaded due to our Settings.yaml and includes all needed TypoScript files in turn.

### 4.4.2 Lists.ts2
A prototype for an abstract news list is generated, which will later be used to implement specific lists we need for displaying purposes.
Apart from inheriting the ```prototype(Flowpack.Listable:List)``` we define, that each ```Flowpack.Listable:Listable``` node that gets rendered by an abstract news list should be ordered by the "created" property (which is available by default from Neos 2.0 and up).
Also the list can be filtered by a tag if it is passed as get parameter (see the according FlowQuery). You might wonder where the "filter" operation comes from and how it is possible to sort by date in Neos. For these operations custom FlowQueries were created by the authors of ```Flowpack.Listable``` - you can find them at: https://github.com/Flowpack/Flowpack.Listable/tree/master/Classes/Flowpack/Listable/TypoScript/Eel/FlowQueryOperations
If you are interested on how to create your own custom FlowQuery operations, you can find information about that at http://neos.readthedocs.org/en/stable/ExtendingNeos/CustomFlowQueryOperations.html

### 4.4.3 Author.ts2, Category.ts2
Those files are rather similar, therefore we cover them together.
The section name allows us to render a specific part of the Fluid template, which might seem unnecessary in this case, as there is only one section available in the according file. This allows us to use fully qualified XML namespaces however, which often comes in handy when you want to use auto-completion in your IDE of choice. (you can check out https://buzz.typo3.org/people/florian-weiss/article/something-neos-vii-riders-on-the-phpstorm/ for a tutorial)
Our "main" object just fetches the ContentCollection that most probably will contain information about the author added by editors (e.g.: short summary, image, etc.), newsByAuthor uses the ```filterByReference``` FlowQuery of ```Flowpack.Listable``` (see above) to get all news that were created by this author. (rember: the NodeType definition of ```Community.News:AbstractNews``` contains a ```type: reference``` for the ```author``` property)

### 4.4.4 NewsShort.ts2
As there is no NodeType in the NodeType definition, Neos will not automatically create a TS2 object and we have to fetch the properties via the according TypoScript lines.

### 5 Starting your own News package and examples
As stated in the foreword this extension is not intended to be used out of the box, but should rather serve as an example on how you can create your own news plugin.
However we invite you to also use ```Flowpack.Listable``` as a base for your own work (just require it via composer so you are sure to be able to get new updates easily), as chances are great that you'll need some sort of listings yourself.

If you want to see some real world examples for Neos news implementations check out:
http://www.billardcafe.at (News rendered as tiles with flavor images in between, also introducing events)
(dmitri insert your sites here please :))

Got your own example and want to have it included here? Got new ideas or improvements for our source? Get in touch with us directly via Github or on Twitter!
We are looking forward to hearing from you.








