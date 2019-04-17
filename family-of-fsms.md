---
id: 63
title: Family of FSMs
date: 2015-10-09T20:30:43+00:00
author: tdegueul
layout: page
guid: http://localhost/wordpress/?page_id=63
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
    

ExecuteFsm.java

    
    package finitestatemachines;
    
    import java.util.ArrayList;
    import java.util.List;
    
    import org.eclipse.emf.common.util.URI;
    import org.eclipse.emf.ecore.resource.Resource;
    import org.eclipse.emf.ecore.resource.ResourceSet;
    import org.eclipse.emf.ecore.resource.impl.ResourceSetImpl;
    
    import semantics.common.Context;
    import family.StandaloneSetup;
    import family.flatfsmmt.fsm.StateMachine;
    
    public class ExecuteFsm {
    	public static void execute(StateMachine fsm, String input, String output) {
    		Context c = new Context(input);
    		// Dynamic dispatch on the appropriate eval() implementation
    		fsm.eval(c, output);
    	}
    
    	public static void main(String[] args) {
    		StandaloneSetup.doSetup();
    		ResourceSet rs = new ResourceSetImpl();
    
    		List<String> models = new ArrayList<>();
    		models.add("melange:/file/input/dummie.xmi?mt=family.FlatFsmMT");
    		models.add("melange:/file/input/timeddummie.xmi?mt=family.FlatFsmMT");
    		models.add("melange:/file/input/compositedummie.xmi?mt=family.FlatFsmMT");
    		models.add("melange:/file/input/timedcompositedummie.xmi?mt=family.FlatFsmMT");
    
    		int i = 0;
    		for (String uri : models) {
    			Resource res = rs.getResource(URI.createURI(uri), true);
    			StateMachine root = (StateMachine) res.getContents().get(0);
    			// Polymorphic invokation of execute()
    			execute(root, "{x;y;z,o;p;q}", "output-" + ++i + ".pdf");
    		}
    	}
    }
    

## Sources

  * [fr.inria.diverse.examples.fsm.metamodel](https://ci.inria.fr/k3al/job/melange-master/ws/examples/fr.inria.diverse.examples.fsm.metamodel/*zip*/fr.inria.diverse.examples.fsm.metamodel.zip)
  * [fr.inria.diverse.examples.fsm.metamodel.composite](https://ci.inria.fr/k3al/job/melange-master/ws/examples/fr.inria.diverse.examples.fsm.metamodel.composite/*zip*/fr.inria.diverse.examples.fsm.metamodel.composite.zip)
  * [fr.inria.diverse.examples.fsm.metamodel.timed](https://ci.inria.fr/k3al/job/melange-master/ws/examples/fr.inria.diverse.examples.fsm.metamodel.timed/*zip*/fr.inria.diverse.examples.fsm.metamodel.timed.zip)
  * [fr.inria.diverse.examples.fsm.metamodel.timedComposite](https://ci.inria.fr/k3al/job/melange-master/ws/examples/fr.inria.diverse.examples.fsm.metamodel.timedComposite/*zip*/fr.inria.diverse.examples.fsm.metamodel.timedComposite.zip)
  * [FsmFamily](https://ci.inria.fr/k3al/job/melange-master/ws/examples/FsmFamily/*zip*/FsmFamily.zip)
  * [FsmFamilySemantics](https://ci.inria.fr/k3al/job/melange-master/ws/examples/FsmFamilySemantics/*zip*/FsmFamilySemantics.zip)