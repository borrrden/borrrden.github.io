---
layout: post
title: "Couchbase Lite Corner Tour Part One"
date: 2015-02-19 20:22:02 +0900
comments: true
---

I'd like to start a multi part series based on a trip through the corners of Couchbase Lite.  I'll start off with the simplest thing that you can do in Couchbase Lite, which is creating and editing a `Document` and saving it locally.  A `Document` is nothing more than a collection of keys and values, serializable to JSON. 

`Documents` are stored in a collection called a `Database`.  Couchbase Lite has the ability to manage multiple `Databases` via the `Manager` class.  For convience, the `Manager` provides a static, shared default `Manager` for use throughout an application.  You can access it via `Manager.SharedInstance`.  Once obtained, you can get a `Database` via the `Manager.GetDatabase()` method.  For example, if you want a `Database` named "cb" you can do the following:

    var manager = Manager.SharedInstance;
	var db = manager.GetDatabase("db");
		    
Now that you have a `Database`, I guess you will want to put something into it.  To create a new `Document` in the `Database` you can use the `Database.CreateDocument()` method.  This will return a new `Document` object that you can begin to edit.  For a new `Document` object you can create an initial set of properties using the `Document.PutProperties()` method.  However, be aware that if you call this method on an existing `Document` that has already been saved you will need to take a few extra steps that I will talk about later. 

You may notice that the return type of `PutProperties()` is a class called `SavedRevision`.  This is in order to track the change history of a `Document`.  Whenever you save a document, a new `Revision` is created which is linked to the previous `Revision` in order to create a tree structure tracking all the way back to the first version of the `Document`.

	var doc = db.CreateDocument();
	var props = new Dictionary<string, object> {
		{ "myProperty", "myValue" },
		{ "Jim", "Borden" }
	};
	doc.PutProperties(props);

Keeping that in mind, let's see the process when retrieving and updating an already existing `Document`.  You can retrieve a `Document` out of a `Database` by using the `Database.GetExistingDocument()` method (or, if you want to automatically create it if it doesn't exist:  `Database.GetDocument()`).  This will return the `Document` containing the properties of the newest `Revision` stored in the `Database`.  Let's say you want to change them.  You can do so in one of two ways (technically more but these two are the easiest).

If you just need to change things all at once (fire and forget) then you can make use of the `Document.Update()` method.  It will take care of created a new `UnsavedRevision` for you, which is just a placeholder that is not saved back to the `Database` until its `Save()` method is called. The argument for `Update()` is an `UpdateDelegate` function.  It is a function that takes one argument, an `UnsavedRevision`, and returns a `bool` indicating whether or not this revision should be saved or discarded.  Upon entry, the `UnsavedRevision` will contain identical properties to the current `Revision` of the `Document`.

	doc.Update((UnsavedRevision rev) => 
	{
		//rev.UserProperties will contain the two previous vals
		rev.UserProperties["myProperty"] = "myOtherValue";
		return true; //This will cause the revision to be saved
	});

However, if you need some more complicated logic (say you need to pass the revision to various functions, etc) then you can make use of the `Document.CreateRevision()` method.  This will create a new `UnsavedRevision` with the same characteristics as the one passed to the `Update()` method, but is more easily passed around for editing.  Once the `Save()` method is called, the properties and new `Revision` will be written to the `Database` and a `SavedRevision` will be returned for reference.  

	var newRev = doc.CreateRevision();
	//...
	newRev["myProperty"] = "yetAnotherValue";
	newRev.Save();
																	    
There you have it!  That's all there is to create `Documents` locally in Couchbase Lite.  Next time I'll talk about how to attach arbitrary binary files to a `Document`.
