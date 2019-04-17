---
id: 37
title: Transformation Reuse
date: 2015-10-09T20:04:42+00:00
author: tdegueul
layout: page
guid: http://localhost/wordpress/?page_id=37
---
In this tutorial we will show that Melange allows to execute Transformation on multiple Languages.

## Java

Have a look in the [Family of FSMs](http://melange.inria.fr/family-of-fsms/) example, we have the **prettyPrint()** Transformation in Java that prints the content of a StateMachine.

PrettyPrintFsm.java

    
    package finitestatemachines;
    
    import java.util.ArrayList;
    import java.util.List;
    
    import org.eclipse.emf.common.util.URI;
    import org.eclipse.emf.ecore.resource.Resource;
    import org.eclipse.emf.ecore.resource.ResourceSet;
    import org.eclipse.emf.ecore.resource.impl.ResourceSetImpl;
    
    import family.StandaloneSetup;
    import family.flatfsmmt.fsm.StateMachine;
    
    public class PrettyPrintFsm {
    	public static void prettyPrint(StateMachine fsm) {
    		System.out.println("Printing " + fsm.getName());
    
    		fsm.getStates().forEach(s -> {
    			System.out.println("State " + s.getName());
    
    			s.getIncoming().forEach(t ->
    				System.out.println(
    					"\tIncoming "+ t.getName()
    					+ " from " + t.getSource().getName()));
    			s.getOutgoing().forEach(t ->
    				System.out.println("\tOutgoing " + t.getName()
    				+ " to " + t.getTarget().getName()));
    		});
    
    		System.out.println();
    	}
    
    	public static void main(String[] args) {
    		StandaloneSetup.doSetup();
    		ResourceSet rs = new ResourceSetImpl();
    
    		List<String> models = new ArrayList<>();
    		models.add("melange:/file/input/dummie.xmi?mt=family.FlatFsmMT");
    		models.add("melange:/file/input/timeddummie.xmi?mt=family.FlatFsmMT");
    		models.add("melange:/file/input/compositedummie.xmi?mt=family.FlatFsmMT");
    		models.add("melange:/file/input/timedcompositedummie.xmi?mt=family.FlatFsmMT");
    
    		for (String uri : models) {
    			Resource res = rs.getResource(URI.createURI(uri), true);
    			StateMachine root = (StateMachine) res.getContents().get(0);
    			// Polymorphic invokation of prettyPrint()
    			prettyPrint(root);
    		}
    	}
    }
    

The FSM manipulated by the **prettyPrint()** Transformation is from the ModelType FlatFsmMT

    
    import family.flatfsmmt.fsm.StateMachine;
    

We load 4 models conforms two 4 differents Languages, all subtypes of FlatFsmMT.

    
    models.add("melange:/file/input/dummie.xmi?mt=family.FlatFsmMT");
    models.add("melange:/file/input/timeddummie.xmi?mt=family.FlatFsmMT");
    models.add("melange:/file/input/compositedummie.xmi?mt=family.FlatFsmMT");
    models.add("melange:/file/input/timedcompositedummie.xmi?mt=family.FlatFsmMT");
    

So all theses models are manipulable by the **prettyPrint()** Transformation.

    
    for (String uri : models) {
    	Resource res = rs.getResource(URI.createURI(uri), true);
    	StateMachine root = (StateMachine) res.getContents().get(0);
    	// Polymorphic invokation of prettyPrint()
    	prettyPrint(root);
    }
    

## ATL

The [Family of FSMs](http://melange.inria.fr/family-of-fsms/) example contains also a Transformation in ATL that flatten a composite FSM.

FlattenFsm.atl

    
    -- Directly adapted from http://docatlanmod.emn.fr/Models_2012/ATLstates.atl
    
    -- @path FlatFsm=/fr.inria.diverse.examples.fsm.metamodel/model/fsm.ecore
    -- @path CompositeFsmMT=/FsmFamily/model-gen/CompositeFsmMT.ecore
    module FlattenFsm;
    
    create OUT : FlatFsm from IN : CompositeFsmMT;
    
    rule SM2SM { 
        from sm1 : CompositeFsmMT!StateMachine
        to sm2 : FlatFsm!StateMachine
    }
    
    rule State2State {
        from rs1 : CompositeFsmMT!State (
        	not rs1.oclIsTypeOf(CompositeFsmMT!InitialState) and
    		not rs1.oclIsTypeOf(CompositeFsmMT!CompositeState) and
    		not rs1.oclIsTypeOf(CompositeFsmMT!FinalState)
    	)
        to rs2 : FlatFsm!State (
        	name <- rs1.name,
    		stateMachine <- rs1.stateMachine
    	)
    }
    
    -- Initial states of composite states become regular states in the flattened SM
    rule Initial2State {
        from is1 : CompositeFsmMT!InitialState (
        	not is1.parentState.oclIsUndefined()
    	)
        to is2 : FlatFsm!State (
        	stateMachine <- is1.stateMachine,
    		name <- is1.name
    	)
    }
    
    -- Initial states are still initial states in the flattened SM
    rule Initial2Initial {
        from is1 : CompositeFsmMT!InitialState (
        	is1.parentState.oclIsUndefined()
    	)
        to is2 : FlatFsm!InitialState (
        	stateMachine <- is1.stateMachine,
    		name <- is1.name
    	)
    }
    
    -- Final states of composite states become regular states in the flattened SM
    rule Final2State {
        from fs1 : CompositeFsmMT!FinalState (
        	not fs1.parentState.oclIsUndefined()
    	)
        to fs2 : FlatFsm!State (
        	stateMachine <- fs1.stateMachine, name <- fs1.name
    	)
    }
    
    -- Final states are still final states in the flattened SM
    rule Final2Final {
        from fs1 : CompositeFsmMT!FinalState (
        	fs1.parentState.oclIsUndefined()
    	)
        to fs2 : FlatFsm!FinalState (
        	stateMachine <- fs1.stateMachine, name <- fs1.name
    	)
    }
    
    -- Transitions between two non-composite states are mapped one-to-one
    rule T2TA {
        from t1 : CompositeFsmMT!Transition (
        	not t1.source.oclIsTypeOf(CompositeFsmMT!CompositeState) and
    		not t1.target.oclIsTypeOf(CompositeFsmMT!CompositeState)
    	)
        to t2 : FlatFsm!Transition (
        	name <- t1.name,
    		stateMachine <- t1.stateMachine,
    		source <- t1.source,
    		target <- t1.target
    	)
    }
    
    -- This rule resolves a transition originating from a composite state 
    rule T2TB {
        from t1 : CompositeFsmMT!Transition,
             src : CompositeFsmMT!CompositeState, 
             trg : CompositeFsmMT!State,
             c : CompositeFsmMT!State (
             	t1.source = src and
    			t1.target = trg and
    			c.parentState = src and
    			not trg.oclIsTypeOf(CompositeFsmMT!CompositeState)
    		)
        to t2 : FlatFsm!Transition (
        	name <- t1.name,
    		stateMachine <- t1.stateMachine,
    		source <- c,
    		target <- trg
    	)
    }
    
    -- This rule resolves a transition ending in a composite state 
    rule T2TC {
        from t1 : CompositeFsmMT!Transition,
             src : CompositeFsmMT!State, 
             trg : CompositeFsmMT!CompositeState,
             c : CompositeFsmMT!InitialState (
             	t1.source = src and
    			t1.target = trg and
    			c.parentState = trg and
    			not src.oclIsTypeOf(CompositeFsmMT!CompositeState)
    		)
        to   t2 : FlatFsm!Transition (
        	name <- t1.name,
    		stateMachine <- t1.stateMachine,
    		source <- src,
    		target <- c
    	)
    }
    

You can see that this Transformation manipulates a CompositeFsmMT

    
    -- @path CompositeFsmMT=/FsmFamily/model-gen/CompositeFsmMT.ecore
    

FlattenFsmATL.java contains the code to call the ATL Transformation.

    
    package finitestatemachines;
    
    import java.io.FileInputStream;
    import java.io.FileNotFoundException;
    import java.util.HashMap;
    
    import org.eclipse.core.runtime.NullProgressMonitor;
    import org.eclipse.emf.ecore.EPackage;
    import org.eclipse.m2m.atl.core.ATLCoreException;
    import org.eclipse.m2m.atl.core.IExtractor;
    import org.eclipse.m2m.atl.core.IInjector;
    import org.eclipse.m2m.atl.core.IModel;
    import org.eclipse.m2m.atl.core.IReferenceModel;
    import org.eclipse.m2m.atl.core.ModelFactory;
    import org.eclipse.m2m.atl.core.emf.EMFExtractor;
    import org.eclipse.m2m.atl.core.emf.EMFInjector;
    import org.eclipse.m2m.atl.core.emf.EMFModel;
    import org.eclipse.m2m.atl.core.emf.EMFModelFactory;
    import org.eclipse.m2m.atl.core.emf.EMFReferenceModel;
    import org.eclipse.m2m.atl.core.launch.ILauncher;
    import org.eclipse.m2m.atl.engine.emfvm.launch.EMFVMLauncher;
    
    public class FlattenFsmATL {
    	public static void runATL(String inputURI, String outputURI) {
    		try {
    			ILauncher transformationLauncher = new EMFVMLauncher();
    			ModelFactory modelFactory = new EMFModelFactory();
    			IInjector injector = new EMFInjector();
    			IExtractor extractor = new EMFExtractor();
    
    			IReferenceModel cfsmmt = modelFactory.newReferenceModel();
    			injector.inject(cfsmmt, "http://compositefsmmt/");
    			IReferenceModel ffsm = modelFactory.newReferenceModel();
    			injector.inject(ffsm, "http://fsm/");
    
    			IModel inModel = modelFactory.newModel(cfsmmt);
    			injector.inject(inModel, inputURI);
    
    			IModel outModel = modelFactory.newModel(ffsm);
    			transformationLauncher.initialize(new HashMap<String,Object>());
    			transformationLauncher.addInModel(inModel, "IN", "CompositeFsmMT");
    			transformationLauncher.addOutModel(outModel, "OUT", "FlatFsm");
    			transformationLauncher.launch(ILauncher.RUN_MODE, new NullProgressMonitor(), new HashMap<String,Object>(),
    				new FileInputStream("../FsmFamily/src/finitestatemachines/FlattenFsm.asm"));
    
    			extractor.extract(outModel, "output/" + outputURI);
    
    			EMFModelFactory emfModelFactory = (EMFModelFactory) modelFactory;
    			emfModelFactory.unload((EMFModel) inModel);
    			emfModelFactory.unload((EMFModel) outModel);
    			emfModelFactory.unload((EMFReferenceModel) cfsmmt);
    			emfModelFactory.unload((EMFReferenceModel) ffsm);
    		} catch (ATLCoreException e) {
    			e.printStackTrace();
    		} catch (FileNotFoundException e) {
    			e.printStackTrace();
    		}
    	}
    
    	public static void main(String[] args) {
    		EPackage.Registry.INSTANCE.put(family.compositefsmmt.fsm.FsmPackage.eNS_URI,
    				family.compositefsmmt.fsm.FsmPackage.eINSTANCE);
    		family.StandaloneSetup.doSetup();
    
    		// Polymorphic ATL invocation
    		System.out.println("runATL:\n");
    		runATL("melange:/file/input/compositedummie.xmi?mt=family.CompositeFsmMT",
    				"OutputATLFsm.fsm");
    	}
    }
    

At the end we load a CompositeFsm but we cast it to CompositeFsmMT (its exactType)

    
    runATL("melange:/file/input/compositedummie.xmi?mt=family.CompositeFsmMT",
    				"OutputATLFsm.fsm");
    

## QVTo

Melange supports also QVTo Transformations as you can see in the [SimpleFsmQVTo](https://github.com/diverse-project/melange/tree/master/examples/SimpleFsmQVTo) example.

DummyInvert.qvto

    
    modeltype FsmMT uses 'http://fsmmt/';
    modeltype Fsm   uses 'http://fsm/';
    
    transformation dummyInvert(in inFsm : FsmMT, out outFsm : Fsm);
    
    main() {
    	inFsm.rootObjects()[FsmMT::FSM] -> map mapFSM(); 
    }
    
    mapping FsmMT::FSM::mapFSM() : Fsm::FSM {
    	ownedState   := self.ownedState -> map mapState();
    	initialState := self.finalState -> first().map mapState();
    	finalState   := self.initialState.map mapState();
    }
    
    mapping FsmMT::State::mapState() : Fsm::State {
    	name := "Inverted" + self.name;
    	outgoingTransition := self.incomingTransition -> map mapTransition();
    }
    
    mapping FsmMT::Transition::mapTransition() : Fsm::Transition {
    	input  := "Inverted" + self.input;
    	output := "Inverted" + self.output;
    	target := self.source.map mapState();
    }
    

## Sources

**For Java & ATL**

  * [fr.inria.diverse.examples.fsm.metamodel](https://github.com/diverse-project/melange/tree/master/examples/fr.inria.diverse.examples.fsm.metamodel)
  * [fr.inria.diverse.examples.fsm.metamodel.composite](https://github.com/diverse-project/melange/tree/master/examples/fr.inria.diverse.examples.fsm.metamodel.composite)
  * [fr.inria.diverse.examples.fsm.metamodel.timed](https://github.com/diverse-project/melange/tree/master/examples/fr.inria.diverse.examples.fsm.metamodel.timed)
  * [fr.inria.diverse.examples.fsm.metamodel.timedComposite](https://github.com/diverse-project/melange/tree/master/examples/fr.inria.diverse.examples.fsm.metamodel.timedComposite)
  * [FsmFamily](https://github.com/diverse-project/melange/tree/master/examples/FsmFamily)
  * [FsmFamilySemantics](https://github.com/diverse-project/melange/tree/master/examples/FsmFamilySemantics)

**For QVTo**

  * [SimpleAspectsProject](https://github.com/diverse-project/melange/tree/master/examples/SimpleAspectsProject)
  * [SimpleFsmQVTo](https://github.com/diverse-project/melange/tree/master/examples/SimpleFsmQVTo)