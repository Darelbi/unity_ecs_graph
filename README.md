# unity_ecs_graph
Trying to put togheter a storage for ECS, mixing Svelto, Unity3d and Neo4j + Cipher language

Having been one of the first users of the Svelto ECS framework, I started thinking how that relates to game development in general, to scalability, open worlds and serialization. I came to conclusion that the best way to achieve that is using a Graph storage engine, like Neo4j, however this is highly experimental work, thus it may not be suitable for a real game. I have to play with it to see if it has enough power, to support a scalable massive multiplayer and making easier to process on multicore machines gameplay elements by powering abstractions over Svelto's ECS system.


Neo4j is a graph storage engine that store data and connections between data, this allows to decouple the game from hardcoded "references", that may cause problems when trying to synchronize parallel computations. It is overkill delegating so much reposnsibilities to Neo4j, this is a real performance killer. But the idea, is to just ignore the synchronization troubles, to see if actually there are usage patterns that, once emerged, allows to identify where to put hands to allow synchronization.

so basically:

FORGET ABOUT SYNCHRONIZATION PRIMITIVES, MULTI-THREADING. PARALLELITY. => Use a very flexible storage that allows to do everything without warrying about limits, even if doing so force the game to run at 0.00003 FPS, and examine where the pattern is in order to build the game in a PARALLEL-BY-DESIGN. Once that problem is solved (if possible) => We get much much easier multiplayer games with lot of less code dedicated to keep stuff synchronized.

To get you a idea of what Neo4j is capable:


    CREATE 
    (s:Source  {url: "https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3666300/"}),
    (a:Tag {name:"Acupuncture"}),
    (n:Tag {name:"Neuroscience"}),
    (u:Tag {name:"Article"}),
    (l:Lang {language:"EN"}),
    (i:Standard { name: "ISO 639-1 Code"}),
    (s)-[:TAGGED]->(a),
    (s)-[:TAGGED]->(n),
    (s)-[:TAGGED]->(u),
    (a)-[:LOCALIZED]->(l),(n)-[:LOCALIZED]->(l),(u)-[:LOCALIZED]->(l),
    (l)-[:STANDARDIZED]->(i)

I Just created some nodes, a Source link (aka a wikipedia reference), 3 tags, a language and a standard. Then I putted in relation all the nodes, it is clear that

the link is tagged with 3 topics (Acupuncture, Neuroscience, Article), the tags are in English, and the english code follow a particular standard.

Now, forget about blogging, the nodes are game elements, and they are in relation with each other.

The player is a node, the items in inventory another node, each node has a ECS id.

The power of SVELTO, is also actually its limit. You filter components by type, Id, by groups. But you have (relatively high for a game) limit in Ids, Groups number. You can keep collections of Ids and  you have a very high speed. But you are limited when you need to group or filter objects in more complex ways, you can always find a hack to do that, but more often you have to change way of thinking to follow the strict rules of the framework. Actually it pays off, but when you have relationships "modeled by design" (just imagine creating a graph editor using Svelto), things starts to complicate and the task gets daunting.

Also there is a problem, where do you put references of relationships? If A, and B are related => do you store a reference to B in A, a reference to A in B, or both? this problem disappear by using a graph engine.

Then => take svelto. REmove the current limits => some millions Ids, a few dozen groups, hardness to maintain relationships.

See what happens, how easy/hard becomes to code, see if there are patterns that makes life easier, and then come with a more performant solution.

(I know this is all full of typo, it is just a quicklist made for remembering my thoughs after a day passed playing with Neo4j and Unity3d)
