---
layout: wiki
title: Projection
---
## Projection

## Projector
![](http://www.websequencediagrams.com/cgi-bin/cdraw?lz=dGl0bGUgQ3JlYXRlIHNuaXBwZXQgYW5kIHNhdmUgaXQKClByb2plY3Rvci0-K1MAGwZSZW5kZXJlckNvbGxlY3Rpb246IHIADwUoACcHaW9uU291cmNlRGF0YSwgQ29udGV4dAANBikKbG9vcAoAMxkAVhIAMC4AgRYPLT4tAIEcGwCBSwdMaXN0AHYcACAIAIFmFG1lcmdlKAA-CykKZW5kAIFEHC0AgksJAHQNKCkAgmEMRGF0YVBvb2xXcml0ZXI6IHdyaXRlACIMAGUN&s=default)

## SnippetRenderer

![](http://www.websequencediagrams.com/cgi-bin/cdraw?lz=U25pcHBldFJlbmRlcmVyLT4rQ29udGV4dFNvdXJjZTogZ2V0QWxsQXZhaWxhYmxlABcHcwoAGg0tPi0AOg86IABBBwoKbG9vcCB0aHJvdWdoIGMAOAgKAGgSQmxvY2sANwpyAIEPBShQcm9qZWN0aW9uAIENBkRhdGEsAFYIKQoAKA0AdBRzdHJpbmcgLy8gAIFlBwCBFAZudABvFACCCAdLZXlHZW5lcmF0b3IAgX4FS2V5Rm9yAIIUBygAghwHLCBtaXhlZFtdKQCBRQgALAwAZiZLZXkAbRs6Y3JlYXRlKGtleSwAgjUGbnQAZAkAgmEUAINIBwCCSxMAg2IHTGlzdDphZGQoAINyBykKZW5k&s=default)

## BlockRenderer

![](http://www.websequencediagrams.com/cgi-bin/cdraw?lz=QmxvY2tSZW5kZXJlci0-K1RoZW1lTG9jYXRvcjogZ2V0TGF5b3V0Rm9ySGFuZGxlKHN0cmluZykKAB0MLT4tADsNOiAAMAYKAE4QAEcGAFMFTm9kZUNoaWxkcmVuKCkKAGEGADcSb3V0ZXJNb3N0AIEkBQCBBwZzOgCBDwZbXQBQEQCBSgU6IDw8Y3JlYXRlPj4AfQYAgQ8SAIFzBQCBEBAAgggFU3RydWN0dXJlOiBhZGQAghsFKACCIQUpAIEMQ3JvbwCBRAYAgXMIAIFBEWxvb3AgdGhyb3VnaCBjAIIaByBibG9jayBsAIF5BgCBIhUAgnIKAIF1BgA2BgAzBnMgcmVjdXJzaXZlbHkAgVUgc2V0UGFyZW4AgmwGKG5hbWU6AINvBiwAgQoGAIJeBgCCDAdlbmQK&s=default)

-----

## Diagram Source Data

```
title Create snippet and save it

Projector->+SnippetRendererCollection: render(ProjectionSourceData, ContextSource)
loop
SnippetRendererCollection->+SnippetRenderer: render(ProjectionSourceData, ContextSource)
SnippetRenderer->-SnippetRendererCollection: SnippetList
SnippetRendererCollection-> SnippetRendererCollection: merge(SnippetList)
end
SnippetRendererCollection->-Projector: SnippetList()
Projector->DataPoolWriter: writeSnippetList(SnippetList)
```

```
SnippetRenderer->+ContextSource: getAllAvailableContexts
ContextSource->-SnippetRenderer: Context
loop through contexts
SnippetRenderer->+BlockRenderer: render(ProjectionSourceData, Context)
BlockRenderer->-SnippetRenderer: string // Snippet Content
SnippetRenderer->+SnippetKeyGenerator: getKeyForContext(Context, mixed[])
SnippetKeyGenerator->-SnippetRenderer: string // Snippet Key
SnippetRenderer->+Snippet:create(key, content)
Snippet->-SnippetRenderer: Snippet
SnippetRenderer->SnippetList:add(Snippet)
end
```

```
BlockRenderer->+ThemeLocator: getLayoutForHandle(string)
ThemeLocator->-BlockRenderer: Layout
BlockRenderer->+Layout: getNodeChildren()
Layout->-BlockRenderer: outerMostBlockLayouts:Layout[]
BlockRenderer->+Block: <<create>>
Block->-BlockRenderer: Block
BlockRenderer->BlockStructure: addBlock(Block)
BlockRenderer->+Layout: getNodeChildren()
Layout->-BlockRenderer: rootBlockChildrenLayouts:Layout[]
loop through children block layouts
BlockRenderer->BlockRenderer: create child blocks recursively
BlockRenderer->BlockStructure: setParentBlock(name:string, childBlock:Block)
end
```
