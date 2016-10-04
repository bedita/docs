Objects in BEdita
=================

Definition
----------


Brief definition: BEdita ``objects`` (see :term:`object`) are the contents of your application,
the atoms of the information asset you are building.

BEdita ``objects`` also represent your application data model.

Let's try to explain that with some examples: are you building an application to show a museum collection?

Your objects will be artworks, authors and probably historical events, locations and museum rooms, but of course also images and videos.

All of them with some relations between them: an artwork was _createdBy_ an author (or more authors), it could _represent_ an historical event, it was _created_ on a particular place
and be _situated_ in a museum room or _borrowed_ from another museum or gallery.

Are you building a rental car application?

Your objects will probably be cars, car companies, customers, locations of your offices and so on.
Applying some relations you may have a car that has been _produced_ by a car company, _rented_ by a customer from an office.

You can create your custom object types from scratch but you have also a vast collection of core object types
ready to use that may already fit your needs, like:

* contents like documents, events, news
* multimedia items like images, video, audio or collections of those items
* folders that contain other folders or contents to build a hierarchical and browsable structure for your data

Not everything is an ``object`` in BEdita, unlike what happens in Java and other platforms.

An object in BEdita is identified with these capabilities:

* you may extend it adding new properties
* you may create new object types inheriting an existing object type
* you may create semantic relations between objects
* every change on object properties is versioned
* objects share a common id space and have a unique name for every project - url friendly string identifier

Apart from objects we have four basic groups of entities
* entities like tags, categories, permissions or annotations are special entities that we assign to objects: they are not objects itself
* entities like object types, relations, properties are used to design our object model
* other entities like endpoints, configurations, applications, auth providers will be rarely seen directy by API client developers
or backend users: they handle API and project behaviors
* roles are special entities used only to give permissions on objects, endpoints or object types - they are not objects

``Users`` (see :term:`user`) of instead are a special object type: you may add properties and relations to other objects, but you may not extend it with a new type.

It's a powerful yet simple model design that you may use successfully in a wide range of applications.


Usage
------

So, what will you do with BEdita objects?

 * extend core types, adding new properties
 * create new types
 * define relations between object types
 * create actual relations between objects

