---
id: 453
title: The Melange Language Workbench
date: 2017-01-19T07:35:56+00:00
author: tdegueul
layout: revision
guid: http://melange.inria.fr/2017/01/17/61-autosave-v1/
permalink: /2017/01/19/61-autosave-v1/
---
<div class="row">
  <div class="col-xs-6">
    <h3 style="text-align: justify;">
      Overview
    </h3>
    
    <p style="text-align: justify;">
      Melange is a language workbench that provides a modular approach for customizing, assembling and integrating multiple domain-specific language (DSL) specifications and implementations. It helps to manage variability within language specifications (syntactic and semantic variation points), and reuse pieces of syntax and semantics from one DSL to the other.
    </p>
    
    <p style="text-align: justify;">
      The language workbench embeds a model-oriented type system that provides model polymorphism and language substitutability, i.e. the possibility to manipulate a model through different interfaces and to define generic transformations that can be invoked on models written using different DSLs. Melange also provides a dedicated meta-language where models are first-class citizens and languages are used to instantiate and manipulate them. By analogy with the class-based, object-oriented paradigm, Melange can be classified as a language-based, model-oriented language.
    </p>
    
    <p style="text-align: justify;">
      Melange is tightly integrated with the Eclipse Modeling Framework ecosystem and relies on the Ecore meta-language for the definition of the abstract syntax of DSLs. Operational semantics is defined using the <a href="http://diverse-project.github.io/k3/">K3 meta-language</a> in the form of aspects that are statically woven on the language&#8217;s syntax. Designers can thus easily design an interpreter for their DSL in a non-intrusive way. Melange is bundled as a set of Eclipse plug-ins. Please refer to the <a href="http://localhost/wordpress/index.php/download/">installation instructions</a> for more information.
    </p>
    
    <h3 style="text-align: justify;">
      Main Features
    </h3>
    
    <ul>
      <li style="text-align: justify;">
        Specification of DSL syntax and operational semantics
      </li>
      <li style="text-align: justify;">
        Full compatibility with other tools of the EMF ecosystem (e.g. <a href="https://www.google.fr/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwjHwOalj43PAhUC0hoKHS7bCAYQFggeMAA&url=https%3A%2F%2Feclipse.org%2FXtext%2F&usg=AFQjCNEdSRoqr6_ZrKYC4mMmAbLyAX9Yzg">Xtext</a> for defining a DSL&#8217;s textual syntax, or <a href="https://www.google.fr/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwjQzYSgj43PAhUQahoKHX9nBBEQFggeMAA&url=https%3A%2F%2Feclipse.org%2Fsirius%2F&usg=AFQjCNEL2NSEskp2-NiQmBm3q4fD6lMUHQ">Sirius</a> for a graphical syntax)
      </li>
      <li style="text-align: justify;">
        Import of existing Ecore metamodels into DSL definitions
      </li>
      <li style="text-align: justify;">
        Composition, extension, restriction, and customization of DSLs
      </li>
      <li style="text-align: justify;">
        Reuse of existing tools and transformations from one DSL to the other
      </li>
      <li style="text-align: justify;">
        Definition of generic tools and services that can be applied to different DSLs
      </li>
      <li style="text-align: justify;">
        Creation and management of language families and variation points
      </li>
    </ul>
  </div>
  
  <div class="col-xs-6">
    <pre><code>package fr.inria.diverse.iot

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
</code></pre>
  </div>
</div>

<!--

<div class="row" style="text-align: center;"><em><strong>Click on the boxes below to get more details.</strong></em></div>



<div class="row">

<div id="col1" class="col-xs-4">

<h3>Make your language executable</h3>


Melange enables the lightweight definition of operational semantics on top of metamodels. Get your interpreter with little effort!

</div>



<div id="col2" class="col-xs-4">

<h3>Modular and Reusable DSL Development</h3>


Assemble, compose, and customize existing DSLs in order to create new ones!

</div>



<div id="col3" class="col-xs-4">

<h3>DSLs Interoperability</h3>


Reuse existing tools and transformations from one DSL to the other thanks to model polymorphism!

</div>


</div>



<div id="description" class="collapse">

<div id="col1-content" style="display: none;">

<div class="row">

<div class="col-xs-6">

<p>In Melange, language definitions consist of syntax and semantics. The syntax of a language is expressed as an Ecore metamodel that defines its concepts, their properties, and the relations between them.<br />
Following the principles of separation of concerns in language development, the operational semantics of a language is expressed separately using another dedicated meta-language: <a href="http://diverse-project.github.io/k3/">K3</a>. Using K3, the semantics of a language consists of a set of <em>aspects</em> that define the computation steps of the semantics along with the runtime data on which they operate.<br />
The definition of a language assembles a syntax with a set of aspects, ensuring that the two are structurally compatible.</p>



<p>To learn more about executable language definition in Melange, please refer to the <a href="http://melange.inria.fr/defining-an-executable-dsl/">Defining an executable DSL</a> tutorial.</p>


</div>



<div class="col-xs-6">

<pre><code>package minifsm

// Assembling a language's syntax with its semantics
language MiniFsm {
    syntax "platform:/resource/MiniFsm/model/MiniFsm.ecore"
    with FSMAspect
    with StateAspect
    with FinalStateAspect
    with TransitionAspect
}
			</code></pre>


</div>


</div>


</div>



<div id="col2-content" style="display: none;">

<div class="row">

<div class="col-xs-6">Proinde concepta rabie saeviore, quam desperatio incendebat et fames, amplificatis viribus ardore incohibili in excidium urbium matris Seleuciae efferebantur, quam comes tuebatur Castricius tresque legiones bellicis sudoribus induratae.</div>



<div class="col-xs-6">

<pre><code>
language IoT {
  syntax "/IoT/model/IoT.ecore"
  slice Idl4Emf on ["OperationDef", "PrimitiveDef"]
  merge Lua renaming { "lua" to "iot2" }
  merge ActivityDiagram renaming { "ad" to "iot2" }
  with OpaqueActionAspect
  with OperationDefAspect
  with ExpressionAspect
  exactType IoT2MT
}
			</code></pre>


</div>


</div>


</div>



<div id="col3-content" style="display: none;">

<div class="row">

<div class="col-xs-6">Ut enim quisque sibi plurimum confidit et ut quisque maxime virtute et sapientia sic munitus est, ut nullo egeat suaque omnia in se ipso posita iudicet, ita in amicitiis expetendis colendisque maxime excellit. Quid enim? Africanus indigens mei? Minime hercule! ac ne ego quidem illius; sed ego admiratione quadam virtutis eius, ille vicissim opinione fortasse non nulla, quam de meis moribus habebat, me dilexit; auxit benevolentiam consuetudo. Sed quamquam utilitates multae et magnae consecutae sunt, non sunt tamen ab earum spe causae diligendi profectae.</div>



<div class="col-xs-6">

<pre><code>
language IoT {
  syntax "/IoT/model/IoT.ecore"
  slice Idl4Emf on ["OperationDef", "PrimitiveDef"]
  merge Lua renaming { "lua" to "iot2" }
  merge ActivityDiagram renaming { "ad" to "iot2" }
  with OpaqueActionAspect
  with OperationDefAspect
  with ExpressionAspect
  exactType IoT2MT
}
			</code></pre>


</div>


</div>

--></div> </div>