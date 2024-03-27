GenMogrification:
=================
GenMogrification is the transmogrification of blocks, entities and items as they are generated in the world.
The main goal of this mod is to provide mod pack authors with a tool for preventing select things from being naturally
generated.

An alternate use of this mod would be to simply alter world gen for added variety.
If someone wants one out of three skeletons to spawn with a sword instead of a bow,
or they want zombies to wear complete sets of various types of armor, it can be done.

## When does GenMogrification occur:

* NEW CHUNK LOADING: When a chunk is loaded for the first time, all blocks in the chunk are subject to genmogrification.
* MOB SPAWNING: Each time a mob spawns, the entity and its equipment are subject to genmogrification.
* ENTITY DEATH: Each time a dying entity drops items upon death, those items are subject to genmogrification.
* LOOT GENERATION: When loot is generated (e.g. by opening a previously unopened chest), the loot is subject to
  genmogrification.  
  Please note that this is done using a loot modifier.  
  It is possible for other loot modifiers to alter the loot after this mod has made its changes.  
  ___If restricted items appear in loot, the responsible loot modifier would need to be altered.___

## Configuration:

GenMogrification configuration data is stored as a Json file.
An example file is provided to help understand the file syntax.
The syntax of that data is defined here as follows.

Elements of the data are named in all-caps.
These names are for documentation purposes and are not used in the config file.
Each element will be described, followed by a data diagram, if required, which illustrates how it is
used.

Pipes (`|`) are used to note an OR relationship.
When pipes are used within an array (`[....]`), each entry can be any of the allowed options.
Boolean values will default to false if omitted.

#### GEN_MOG_DATA:

This is root of the configuration data.
It can be an array, object or comment-formatted string.
It should typically be an array of objects.

`[GEN_MOG_COMMENT | GEN_MOG_OBJECT]`

#### GEN_MOG_COMMENT:

Comments are text strings prefixes with a hash (`#`), double-slash (`//`), or slash-asterisk (`/*`). They can be
included in the GEN_MOG_DATA if desired.

`"# comment"` | `"// comment"` | `"/* comment */"`

#### GEN_MOG_OBJECT:

A json object with key/value pairs defining the details of a transmogrification.
It will be processed as a block, entity or item transmogrification
based on the presence of the associated keys ("block", "blocks", "entity", "entities", "item", "items")
or based on the value of a "type" key that has one of those associated keys for its value.
The "type" key is supported for use with a "tag" or "tags" key.
The singular and plural mogrification types can be used interchangeably.

`BLOCK_GEN_MOG` | `ENTITY_GEN_MOG` | `ITEM_GEN_MOG`

Links:  
[BLOCK_GEN_MOG](#block_gen_mog)  
[ENTITY_GEN_MOG](#entity_gen_mog)  
[ITEM_GEN_MOG](#item_gen_mog)

### RESOURCE_LOCATIONS:

A resource location contains two parts: a namespace and a path.
The namespace defaults to minecraft if omitted.

#### TARGET_RESOURCE_LOCATION:

The target is the block/entity/item to be transmogrified during generation.
This can either be a single text string or an array.
All targets in an array of targets will be processed for genmogrification.

`"RESOURCE_LOCATION"` | `["RESOURCE_LOCATION"]`

#### RESULT_RESOURCE_LOCATION:

This can either be a single text string or an array.
If an array is provided, one will be select at random for each transmogrification.
For items, "EMPTY" can be used in place of an item resource location.

`"RESOURCE_LOCATION"` | `["RESOURCE_LOCATION"]`

### BLOCKS

#### BLOCK_GEN_MOG:

To be a block mogrification, the object must have "block", "blocks", or "type" as a key.

_All key/value pairs are optional._
```
{
  "type"                  : "block" | "blocks",
  "block" | "blocks"      : TARGET_RESOURCE_LOCATION,
  "tag" | "tags"          : TARGET_RESOURCE_LOCATION,
  "properties"            : BLOCK_PROPERTIES,
  "result"                : RESULT_BLOCK
}
```

#### BLOCK_PROPERTIES:

Properties are objects with key/value pairs that match their block state.
Values need to be text strings.
For example, a boolean property needs to be either `"true"` or `"false"`.
A block might have properties like "facing" or "powered".

_Example:_

```
{
  "facing"  : "north",
  "powered" : "true"
}  
```

#### RESULT_BLOCK:

This is the result of the transmogrification.
The result can be a resource location, an object, or an array.
If an array of results is given, one will be selected at random each transmogrification.

`"RESOURCE_LOCATION"` | `RESULT_BLOCK_OBJECT` | `[RESULT_BLOCK]`

#### RESULT_BLOCK_OBJECT:

In the absence of a "block"|"blocks" key, the result block will be the same as the target block.
When `"copyProperties"` is `"true"`, the result block will have the same properties as the target block,
as long as the target block supports the property and the property isn't specified in a "properties" key.
The "properties" key is used to set block state properties of the target block.

_All key/value pairs are optional._

```
{
  "block" | "blocks"  : RESULT_RESOURCE_LOCATION,
  "copyProperties"    : true | false,
  "properties"        : BLOCK_PROPERTIES
}  
```

Link:  
[Back to GEN_MOG_OBJECT](#gen_mog_object)

### ENTITIES

#### ENTITY_GEN_MOG:

To be an entity mogrification, the object must have "entity", "entities", or "type" as a key.
The starting inventory of the resulting entity can be specified using the "inventory" key.

_All key/value pairs are optional._
```
{
  "type"                  : "entity" | "entities",
  "entity" | "entities"   : TARGET_RESOURCE_LOCATION,
  "tag" | "tags"          : TARGET_RESOURCE_LOCATION,
  "inventory"             : STARTING_EQUIPMENT,
  "result"                : RESULT_ENTITY
}
```

#### STARTING_EQUIPMENT:

Listed slots will have their starting inventory overwritten.

```
{
  "head"      : RESULT_RESOURCE_LOCATION,
  "chest"     : RESULT_RESOURCE_LOCATION,
  "legs"      : RESULT_RESOURCE_LOCATION,
  "feet"      : RESULT_RESOURCE_LOCATION,
  "mainhand"  : RESULT_RESOURCE_LOCATION,
  "offhand"   : RESULT_RESOURCE_LOCATION
}
```

#### RESULT_ENTITY:

This is the result of the transmogrification.
The result can be a resource location, an object, or an array.
If an array of results is given, one will be selected at random each transmogrification.

`"RESOURCE_LOCATION"` | `RESULT_ENTITY_OBJECT` | `[RESULT_ENTITY]`

#### RESULT_ENTITY_OBJECT:

In the absence of an "entity"|"entities" key, the result entity will be the same as the target entity.
The starting inventory of the resulting entity can be specified using the "inventory" key.
Inventory defined here will be merged with any defined in the ENTITY_GEN_MOG.

_All key/value pairs are optional._

```
{
  "entity" | "entities"  : RESULT_RESOURCE_LOCATION,
  "inventory"            : STARTING_EQUIPMENT
}  
```

Links:  
[Back to GEN_MOG_OBJECT](#gen_mog_object)

### ITEMS

#### ITEM_GEN_MOG:

To be an item mogrification, the object must have "item", "items", or "type" as a key.
exactMatchProperties of true means that not only must the item have the given properties,
but it also must not have any additional properties.

_All key/value pairs are optional._
```
{
  "type"                 : "item" | "items",
  "item" | "items"       : TARGET_RESOURCE_LOCATION,
  "tag" | "tags"         : TARGET_RESOURCE_LOCATION,
  "exactMatchProperties" : true | false,
  "properties"           : ITEM_PROPERTIES
  "result"               : RESULT_ITEM
}
```

#### ITEM_PROPERTIES:

Properties are objects with key/value pairs.

_Example:_

```
{
  "Enchantments": [
    {
      "id": "minecraft:unbreaking",
      "lvl": "3s"
    },
    {
      "id": "minecraft:mending",
      "lvl": "1s"
    }
  ]
}

```

#### RESULT_ITEM:

This is the result of the transmogrification.
The result can be a resource location, an object, or an array.
If an array of results is given, one will be selected at random each transmogrification.

`"RESOURCE_LOCATION"` | `RESULT_ITEM_OBJECT` | `[RESULT_ITEM]`

#### RESULT_ITEM_OBJECT:

In the absence of an "item"|"items" key, the result item will be the same as the target item.
The "properties" key is used to set the nbt tag of the resulting item.
When `"copyProperties"` is `"true"`, the resulting item will have the same properties as the target item,
although if `"properties"` is also defined here, those properties will be added as well.

_All key/value pairs are optional._

```
{
  "entity" | "entities" : RESULT_RESOURCE_LOCATION,
  "properties"          : STARTING_EQUIPMENT,
  "copyProperties"      : true | false
}  
```

Links:  
[Back to GEN_MOG_OBJECT](#gen_mog_object)

-------------------------------

## Miscellaneous Notes:

#### ARRAYS:
Any part of the config that accepts an array `[....]` will also accept sub-arrays within the array.
Not specifically useful, just technically true.
Each entry in an array and within sub arrays will be converted into a flat list.
How sub-arrays are processed is subject to change, so avoid using them.

#### ITEM PROPERTIES:
No effort is made by this mod to verify any nbt tag data provided.
Keep this in mind when troubleshooting any item-related issues.

#### ERROR LOGGING:
While some errors in the json data will generate error messages, not all will.
If some aspect of a genmogrification is being ignored,
check the log _and_ verify the json data for possible issues.
If this mod sees significant use, I might make the error reporting more thorough.

#### CHAINING:
The results of genmogrification may themselves be subject to genmogrification.
If you turn all zombies into skeletons and all skeletons into wither skeletons,
a zombie spawner is probably going to generate wither skeletons.
If you also decide to turn all wither skeletons into zombies,
the server will probably get stuck in an endless loop of canceling and summoning.

#### SPECIAL HANDLING:
There are some aspects of generation that are not compatible with arbitrary changes.
A few of them have been accounted for.
I'm certain there are plenty more that have yet to reveal themselves.
If you encounter an issue with a specific genmogrification, maybe reconsider the changes.

Two specific issues that were discovered and handled are end portal blocks and skeleton bows.

Generating an end portal won't happen automatically if the eyes are filled in through genmogrification.
Without using the config option to automatically generate all end portals, 
portals completed that way would be impossible to activate.
Either use the config option, or don't fill in the eyes on the portals.

As far as the skeletons, they get their bow after their spawn finalization event.
To allow the bows to be genmogrified, their weapon genmogrification is handled separately.
Practically, they do not require any special handling by the user of this mod.
I only mention it as an example of how something else might present a challenge.

One last example....
adding a weapon or armor to an entity doesn't mean that they will be able to render them.
If you give equipment to a skeleton or zombie, they will render it without issue.
If you give them to an enderman or villager, don't expect to be able to see it.
At this point, I can't even say for certain if they would have any effect,
although I suspect the armor would still be functional.

-------------------------------


<center> *** Thank you and enjoy the mod. *** </center>

