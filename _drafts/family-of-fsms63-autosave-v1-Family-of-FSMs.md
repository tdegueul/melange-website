---
id: 140
title: Family of FSMs
date: 2016-08-31T16:58:50+00:00
author: Fabien Coulon
layout: revision
guid: http://melange.inria.fr/2016/08/25/63-autosave-v1/
permalink: /2016/08/31/63-autosave-v1/
---
The Family of FSMs example is based on a set of close languages defining an FSM.  
We show in this example that Melange are able to deal with the the variations in the abstract syntax and the semantic, such as composite state or simultaneous execution.

FSM.melange

    
    package family
    
    language FlatFsm {
    	syntax "platform:/resource/fr.inria.diverse.examples.fsm.metamodel/model/fsm.ecore"
    	with semantics.flat.StateMachineAspect
    	with semantics.flat.StateAspect
    	exactType FlatFsmMT
    }
    
    language FlatFsmSimultaneous {
    	syntax "platform:/resource/fr.inria.diverse.examples.fsm.metamodel/model/fsm.ecore"
    	with semantics.flat.simultaneous.StateMachineAspect
    	with semantics.flat.simultaneous.StateAspect
    	exactType FlatFsmSimultaneousMT
    }
    
    language TimedFsm {
    	syntax "platform:/resource/fr.inria.diverse.examples.fsm.metamodel.timed/model/fsm.ecore"
    	with semantics.timed.StateMachineAspect
    	with semantics.timed.StateAspect
    	with semantics.timed.TransitionAspect
    	exactType TimedFsmMT
    }
    
    language TimedFsmSimultaneous {
    	syntax "platform:/resource/fr.inria.diverse.examples.fsm.metamodel.timed/model/fsm.ecore"
    	with semantics.timed.simultaneous.StateMachineAspect
    	with semantics.timed.simultaneous.StateAspect
    	with semantics.timed.simultaneous.TransitionAspect
    	exactType TimedFsmSimultaneousMT
    }
    
    language CompositeFsm {
    	syntax "platform:/resource/fr.inria.diverse.examples.fsm.metamodel.composite/model/fsm.ecore"
    	with semantics.composite.StateMachineAspect
    	with semantics.composite.StateAspect
    	exactType CompositeFsmMT
    }
    
    language CompositeFsmSimultaneous {
    	syntax "platform:/resource/fr.inria.diverse.examples.fsm.metamodel.composite/model/fsm.ecore"
    	with semantics.composite.simultaneous.StateMachineAspect
    	with semantics.composite.simultaneous.StateAspect
    	exactType CompositeFsmSimultaneousMT
    }
    
    language TimedCompositeFsm {
    	syntax "platform:/resource/fr.inria.diverse.examples.fsm.metamodel.timedComposite/model/fsm.ecore"
    	with semantics.timedcomposite.StateMachineAspect
    	with semantics.timedcomposite.StateAspect
    	with semantics.timedcomposite.TransitionAspect
    	exactType TimedCompositeFsmMT
    }
    
    language TimedCompositeFsmSimultaneous {
    	syntax "platform:/resource/fr.inria.diverse.examples.fsm.metamodel.timedComposite/model/fsm.ecore"
    	with semantics.timedcomposite.simultaneous.StateMachineAspect
    	with semantics.timedcomposite.simultaneous.StateAspect
    	with semantics.timedcomposite.simultaneous.TransitionAspect
    	exactType TimedCompositeFsmSimultaneousMT
    }
    


`
<pre>
` < 

## Sources

  * [fr.inria.diverse.examples.fsm.metamodel](https://ci.inria.fr/k3al/job/melange-master/ws/examples/fr.inria.diverse.examples.fsm.metamodel/*zip*/fr.inria.diverse.examples.fsm.metamodel.zip)
  * [fr.inria.diverse.examples.fsm.metamodel.composite](https://ci.inria.fr/k3al/job/melange-master/ws/examples/fr.inria.diverse.examples.fsm.metamodel.composite/*zip*/fr.inria.diverse.examples.fsm.metamodel.composite.zip)
  * [fr.inria.diverse.examples.fsm.metamodel.timed](https://ci.inria.fr/k3al/job/melange-master/ws/examples/fr.inria.diverse.examples.fsm.metamodel.timed/*zip*/fr.inria.diverse.examples.fsm.metamodel.timed.zip)
  * [fr.inria.diverse.examples.fsm.metamodel.timedComposite](https://ci.inria.fr/k3al/job/melange-master/ws/examples/fr.inria.diverse.examples.fsm.metamodel.timedComposite/*zip*/fr.inria.diverse.examples.fsm.metamodel.timedComposite.zip)
  * [FsmFamily](https://ci.inria.fr/k3al/job/melange-master/ws/examples/FsmFamily/*zip*/FsmFamily.zip)
  * [FsmFamilySemantics](https://ci.inria.fr/k3al/job/melange-master/ws/examples/FsmFamilySemantics/*zip*/FsmFamilySemantics.zip)