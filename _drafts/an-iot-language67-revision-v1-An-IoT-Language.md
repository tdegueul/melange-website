---
id: 293
title: An IoT Language
date: 2016-09-01T10:05:11+00:00
author: Fabien Coulon
layout: revision
guid: http://melange.inria.fr/2016/09/01/67-revision-v1/
permalink: /2016/09/01/67-revision-v1/
---
The Internet Of Things example illustrate the composition of Languages.  
Based on a Language able to define a System with boards and services, we merge another Language to describe interfaces for theses services, the Lua Language to define the implementation the services and an Activity Language to define the behavior of the System

<pre><code lang="melange">package fr.inria.diverse.iot2

// This is a simple comment
language Idl4Emf {
  syntax "/org.csu.idl.idlmm/model/IDLMM.ecore"
  exactType Idl4EmfMT
}

language ActivityDiagram {
  syntax "/org.xtext.activitydiagram.model/model/activitydiagram.ecore"
  with org.xtext.activitydiagram.semantics.*
  exactType ActivityDiagramMT
}

/**
 * This is another one
 */
language Lua {
  syntax "/org.xtext.lua/model/generated/Lua.ecore"
  with org.xtext.lua.semantics.*
  exactType LuaMT
}

language IoT2 {
  syntax "/fr.inria.diverse.iot2.model/model/IoT2.ecore"
  slice Idl4Emf
    on ["OperationDef", "PrimitiveDef"]
    renaming { "idlmm" to "iot2" }
  merge Lua renaming { "lua" to "iot2" }
  merge ActivityDiagram renaming { "activitydiagram" to "iot2" }
  with fr.inria.diverse.iot2.OpaqueActionAspect
  with fr.inria.diverse.iot2.OperationDefAspect
  with fr.inria.diverse.iot2.ExpressionAspect
  exactType IoT2MT
}
</code></pre>

## Sources

Language workbench

  * [org.csu.idl.idlmm](https://ci.inria.fr/k3al/job/melange-master/ws/examples/IoT2/org.csu.idl.idlmm/*zip*/org.csu.idl.idlmm.zip)
  * [org.xtext.activitydiagram.model](https://ci.inria.fr/k3al/job/melange-master/ws/examples/IoT/org.xtext.activitydiagram.model/*zip*/org.xtext.activitydiagram.model.zip)
  * [org.xtext.activitydiagram.semantics](https://ci.inria.fr/k3al/job/melange-master/ws/examples/IoT/org.xtext.activitydiagram.semantics/*zip*/org.xtext.activitydiagram.semantics.zip)
  * [org.xtext.lua](https://ci.inria.fr/k3al/job/melange-master/ws/examples/IoT/org.xtext.lua/*zip*/org.xtext.lua.zip)
  * [org.xtext.lua.semantics](https://ci.inria.fr/k3al/job/melange-master/ws/examples/IoT/org.xtext.lua.semantics/*zip*/org.xtext.lua.semantics.zip)
  * [fr.inria.diverse.iot2.model](https://ci.inria.fr/k3al/job/melange-master/ws/examples/IoT2/fr.inria.diverse.iot2.model/*zip*/fr.inria.diverse.iot2.model.zip)
  * [fr.inria.diverse.iot2](https://ci.inria.fr/k3al/job/melange-master/ws/examples/IoT2/fr.inria.diverse.iot2/*zip*/fr.inria.diverse.iot2.zip)