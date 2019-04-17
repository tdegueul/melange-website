---
id: 102
title: Melange
date: 2015-10-12T11:55:26+00:00
author: tdegueul
layout: revision
guid: http://melange.inria.fr/2015/10/12/61-revision-v1/
permalink: /2015/10/12/61-revision-v1/
---
<div style="width: 50%; float: left;">
  <p style="text-align: left;">
    Melange is a language workbench which help language designers to mashup their various language concerns as language design choices, to manage their variability, and support their reuse. It provides a modular and reusable approach for customizing, assembling and integrating multiple DSLs specifications and implementations.
  </p>
  
  <p style="text-align: left;">
    The language workbench embeds a model-oriented type system that provides model polymorphism and language substitutability, i.e. the possibility to manipulate a model through different interfaces and to define generic transformations that can be invoked on models written using different DSLs. Melange also provides a dedicated meta-language where models are first-class citizens and languages are used to instantiate and manipulate them. By analogy with the class-based, object-oriented paradigm, Melange can be classified as a language-based, model-oriented language.
  </p>
  
  <p style="text-align: left;">
    Melange is tightly integrated with the Eclipse Modeling Framework ecosystem and relies on the meta-language Ecore for the definition of the abstract syntax of DSLs. Executable meta-modeling is supported by weaving operational semantics defined with Xtend. Designers can thus easily design an interpreter for their DSL in a non-intrusive way. Melange is bundled as a set of Eclipse plug-ins. Please refer to the <a href="http://localhost/wordpress/index.php/download/">installation instructions</a> for more information.
  </p>
</div>

<div style="float: right;">
  <pre><code>package fr.inria.diverse.iot2

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
</code></pre>
</div>