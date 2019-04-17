---
id: 364
title: 'SLE&#8217;15 Companion Webpage'
date: 2016-10-17T10:17:39+00:00
author: tdegueul
layout: revision
guid: http://melange.inria.fr/2016/10/17/356-revision-v1/
permalink: /2016/10/17/356-revision-v1/
---
### A language for Internet of Things systems modeling in Melange

[<img class="aligncenter wp-image-358" src="http://melange.inria.fr/wp-content/uploads/2016/10/iot2.png" alt="iot2" width="600" height="379" srcset="http://melange.inria.fr/wp-content/uploads/2016/10/iot2.png 800w, http://melange.inria.fr/wp-content/uploads/2016/10/iot2-300x189.png 300w, http://melange.inria.fr/wp-content/uploads/2016/10/iot2-768x485.png 768w" sizes="(max-width: 600px) 100vw, 600px" />](http://melange.inria.fr/wp-content/uploads/2016/10/iot2.png)

    package fr.inria.diverse.iot
    
    language Idl4Emf {
      syntax "/IDL/model/IDL.ecore"
      exactType Idl4EmfMT
    }
    
    language ActivityDiagram {
      syntax "/AD/model/ActivityDiagram.ecore"
      with org.xtext.activitydiagram.semantics.*
      exactType ActivityDiagramMT
    }
    
    language Lua {
      syntax "/Lua/model/Lua.ecore"
      with org.xtext.lua.semantics.*
      exactType LuaMT
    }
    
    // Assembling a new modeling language for internet
    // of things systems modeling from an interface
    // description language, an activity diagram, and 
    // the Lua programming language
    language IoT {
      syntax "/IoT/model/IoT.ecore"
      slice Idl4Emf on ["OperationDef", "PrimitiveDef"]
      merge Lua renaming { "lua" to "iot2" }
      merge ActivityDiagram renaming { "ad" to "iot2" }
      with fr.inria.diverse.iot2.OpaqueActionAspect
      with fr.inria.diverse.iot2.OperationDefAspect
      with fr.inria.diverse.iot2.ExpressionAspect
      exactType IoT2MT
    }
    

The archive file containing the Eclipse workspace used for the case study can be downloaded here: [SLE15.tar.gz](http://melange-lang.org/resources/SLE15.tar.gz).