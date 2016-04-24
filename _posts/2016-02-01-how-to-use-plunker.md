---
layout:     post
title:      How to use plunker
date:       2016-02-01
summary:    How to use plunker as a tool to quickly try out ideas.
categories: tech devtool
tags:      plunker
---
Plunker has been a great tool for developers to try out new ideas effectively, share code among communities.
Compared to other popular tool such as JSFiddle and Codepen, I personally find Plunker excel in the following areas:

* Best support for `angularjs`. In fact Plunker itself is written in `angularjs`.
* Ability to create unlimited number of files. This makes trying out larger size of demos possible.
* `SSO` through Github. 
* Save templates & create new plunks from templates.
* Better editor, full page preview, built-in code prettifier and linter etc.

We are going to talk about a few commonly used features offered by Plunker.

### One time use

It is the most basic usage of Plunker. Simply go to [Plunker](http://plnkr.co/edit/?p=catalogue) and start editing the html and javascript.
You can  'Live Preview' what you are doing by clicking the preview icon on the right toolbar in a Plunker page.

![plunk template list](/images/posts/plunker-preview.png)

### Save for further use and share

This requires you to sign in with your Github account. After doing so, you will be able to save your plunks.

### Save a plunk as a template

If we find ourselves writing boilerplate code again and again, we can save ourselves time by creating a template
so that all the plunks have a good starting point. To do so, we need to:

* Create a new plunk and put in the template code.
* Save it as a normal plunk, so you get more options on the top toolbar, one of which is 'Save as template':

![plunk template list](/images/posts/plunker-template-unsave.png)

* Click 'Save as template' icon to save the plunk as a template.

### Start a new plunk from a saved template

It is one of my favorite feature of Plunker, because we don't have to start from scratch in Plunker again and again.
To start editing from a template, first make sure we have saved templates to edit from:

* Click the template icon on the toolbar on the right side of the Plunker page as shown below:

![plunk template](/images/posts/plunker-template-icon.png)

* A list of saved templates will be shown as below:

![plunk template list](/images/posts/plunker-template-list.png)

* Click Edit button to start editing from a template selected.

**Note**: this is not [editing directly **on** the template](#edit-a-template). The change won't be made to
the template, but to a new plunk that is created from the template. 

You may wonder how to tell if the plunk you are editing is actually a template or not. As far as I know,
there is no good way to tell the difference other than the fact that saved template has a yellow icon on the top toolbar:

![plunk template list](/images/posts/plunker-template-save.png)

whereas normal plunk has a black icon as the 'save as template' button:

![plunk template list](/images/posts/plunker-template-unsave.png)

### Edit a template

We can also make changes to saved templates. We first need to find the template we want to update in 'My plunks' page, then click 'Edit this Plunk' button as shown below.

![plunk template list](/images/posts/plunker-edit.png)
