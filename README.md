# PharoGEXF

[![Moose 10](https://github.com/badetitou/PharoGEXF/actions/workflows/test.yml/badge.svg)](https://github.com/badetitou/PharoGEXF/actions/workflows/test.yml)
[![Coverage Status](https://coveralls.io/repos/github/badetitou/PharoGEXF/badge.svg?branch=main)](https://coveralls.io/github/badetitou/PharoGEXF?branch=main)

Manipulate [GEXF file format](https://gexf.net) with Pharo.

## Installation 

```st
Metacello new
  githubUser: 'badetitou' project: 'PharoGEXF' commitish: 'main' path: 'src';
  baseline: 'GEXF';
  load
```

## Usage

### Basic commands

1. Create a gexf element
    ```st
    gexf := GEXF new.
    ```
2. Set metadata
    ```st
    gexf metadata creator: 'Gephi.org'.
    gexf metadata description: 'A Web network'.
    ```
 3. Add nodes
    ```st
    graph := gexf graph.
    node1 := graph createNodeWithId: '0'.
	node1 label: 'Hello'.
	node2 := graph createNodeWithId: '2'.
	node2 label: 'World'.
    ```
4. Connect nodes
    ```st
    node1 connectTo: node2.
    ```
5. Export file
    ```st
    String streamContents: [:stream |
	    (GEXFWriter on: stream)
		    prettyPrinting;
	 	    export: gexf ]
    ```

### Full example to show dependencies of classes from Moose

```st
classes := ((model allWithSubTypesOf: FamixJavaType)  select: [ :d | d isStub not and: [ d isAnonymousClass not ]  ]).

gexf := GEXF new.
gexf metadata creator: 'Gephi.org'.
gexf metadata description: 'my project'.

graph := gexf graph.

dicClassNode := classes collect: [ :class | 
	| node |
	node := graph createNode.
	node label: class mooseName.
	class -> node
	 ] as: Dictionary.

dicClassNode keysAndValuesDo: [ :class :node |
	(class query outgoing dependencies targetsAtScope: FamixTType) do: [ :dep |
		dicClassNode at: dep ifPresent: [ :aDepNode | node connectTo: aDepNode ]
		 ] 
	 ].




'd:/seditRHDep.gexf' asFileReference writeStreamDo:  [:stream |
    (GEXFWriter on: stream)
	    prettyPrinting;
 	    export: gexf ]
```
