---
id: 215
title: The Melange Resource
date: 2016-08-30T18:40:26+00:00
author: Fabien Coulon
layout: revision
guid: http://melange.inria.fr/2016/08/30/60-autosave-v1/
permalink: /2016/08/30/60-autosave-v1/
---
In this tutorial you will learn how to load a model with Melange in order to manipulate it conforming to a super language.  
You will see also how to perform a downcast.

## Setup

We will work on the workspace built in the [Assembling multiple DSLs](http://melange.inria.fr/assembling-multiple-dsls/) tutorial.

To setup from an empty workspace, import the following projects:  
&#8211; [MiniFSM metamodel](https://ci.inria.fr/k3al/job/melange-master/ws/examples/MiniFSM/languageProjects/fr.inria.diverse.minifsm/*zip*/fr.inria.diverse.minifsm.zip)  
&#8211; [MiniFSM interpreter](https://ci.inria.fr/k3al/job/melange-master/ws/examples/MiniFSM/languageProjects/fr.inria.diverse.minifsm.interpreter/*zip*/fr.inria.diverse.minifsm.interpreter.zip)  
&#8211; [MiniLang metamodel](https://ci.inria.fr/k3al/job/melange-master/ws/examples/MiniFSM/languageProjects/fr.inria.diverse.minilang/*zip*/fr.inria.diverse.minilang.zip)  
&#8211; [MiniLang interpreter](https://ci.inria.fr/k3al/job/melange-master/ws/examples/MiniFSM/languageProjects/fr.inria.diverse.minilang.interpreter/*zip*/fr.inria.diverse.minilang.interpreter.zip)  
&#8211; [Melange project](https://ci.inria.fr/k3al/job/melange-master/ws/examples/MiniFSM/languageProjects/fr.inria.diverse.melange.fsm/*zip*/fr.inria.diverse.melange.fsm.zip)

Open the Main.melange file in fr.inria.diverse.melange.fsm/src/fr/inria/diverse/melanger/

[<img src="http://melange.inria.fr/wp-content/uploads/2015/10/outline-minifsm.png" alt="outline-minifsm" class="aligncenter" />](http://melange.inria.fr/wp-content/uploads/2015/10/outline-minifsm.png)

We will see how to use the Melange Resource to:

  * load a model conform to **MelangedLang** and use it as a **MelangedLangMT** model
  * load a model conform to **MelangedLang** and use it as a **MiniFsmMT** model
  * load a model conform to **MiniFsm** and use it as a **MelangedLangMT** model

We finish the setup by a right click on Main.melange and select `Melange > Generate All`  
This action will generates a project for each Language and create the classes for the adaptation layer between Languages.

Now that Melange Languages are defined, we have to launch a new Eclipse instance to deploy the plugins present in your workspace.  
To do so open the menu `Run` and select`Run As > Eclipse Application`

## Basic loading

You will see in this section how to load a model conform to MelangedLang and execute it.

Once you have an Eclipse with the plugins defining the Languages, open the menu `File > New > Project...` and select `Eclipse Modeling Framework > Empty EMF Project`, then click `Next`  
Name your project and click `Finish`.

Create a new file &#8220;Sample.minifsm&#8221; in the &#8220;model&#8221; folder and fill it with:

    
    <?xml version="1.0" encoding="UTF-8"?>
    <minifsm:FSM xmi:version="2.0" xmlns:xmi="http://www.omg.org/XMI" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:minifsm="http://melangedlang/minifsm/" initialState="//@states.0">
      <states name="Hello"/>
      <states xsi:type="minifsm:FinalState" name="World!"/>
      <transitions input="//@states.0" output="//@states.1" event="an event"/>
    </minifsm:FSM>
    

We now have a project with a model conform to MelangedLang.

Create a new java class &#8220;Main&#8221; with a main method.  
The first thing to do is to call the Melange setup.

    
    	StandaloneSetup.doSetup();
    

It initialise the Melange runtime by registering EPackages of the Languages and the ModelTypes hierachy.

Then we load the resource &#8220;Sample.minifsm&#8221;.

    
    	ResourceSetImpl resourceSet = new ResourceSetImpl(); 
    	MelangeResourceImpl r = (MelangeResourceImpl) resourceSet.getResource(URI.createURI("melange:/file/model/Sample.minifsm?mt=fr.inria.diverse.melanger.MelangedLangMT"),true);
    

  * The URI start with `melange:/` to load the model as a MelangeResource.
  * Next we use the `file` protocol because we are in a standalone mode.
  * The URI end with `mt=fr.inria.diverse.melanger.MelangedLangMT`. This argument tells we want to load the model as a ModelType.  
    This loading also add the implementation of the methods defined in the Aspects.

We can now execute the model by calling the method FSM.execute(). The Melange adaptation layer will call the implementation defined in the FsmAspect.

    
    	FSM root = (FSM) r.getContents().get(0);
    	BasicEList events = new BasicEList();
    	events.add("an event");
    	root.execute(events);
    

You should see this result in the console by executing the main.

    
    Start
    Exec Hello
    End
    

The full code of the Main class:

    
    package example;
    
    import org.eclipse.emf.common.util.BasicEList;
    import org.eclipse.emf.common.util.URI;
    import org.eclipse.emf.ecore.resource.impl.ResourceSetImpl;
    
    import fr.inria.diverse.melange.resource.MelangeResourceImpl;
    import fr.inria.diverse.melanger.StandaloneSetup;
    import fr.inria.diverse.melanger.melangedlangmt.minifsm.FSM;
    
    public class Main {
    	
    	public static void main(String[] args) {
    		
    		StandaloneSetup.doSetup();
    		
    		ResourceSetImpl resourceSet = new ResourceSetImpl(); 
    		MelangeResourceImpl r = (MelangeResourceImpl) resourceSet.getResource(URI.createURI("melange:/file/model/Sample.minifsm?mt=fr.inria.diverse.melanger.MelangedLangMT"),true);
    
    		FSM root = (FSM) r.getContents().get(0);
    		BasicEList<String> events = new BasicEList<String>();
    		events.add("an event");
    		root.execute(events);
    	}
    }
    

## Upcast

We will then load Sample.minifsm and use it as an instance of the Minifsm Language.  
Create a new Java class &#8220;MainUpcast.java&#8221;

The code of the MainUpcast class is quite similar to the Main class:

    
    package example;
    
    import org.eclipse.emf.common.util.BasicEList;
    import org.eclipse.emf.common.util.URI;
    import org.eclipse.emf.ecore.resource.impl.ResourceSetImpl;
    
    import fr.inria.diverse.melange.resource.MelangeResourceImpl;
    import fr.inria.diverse.melanger.StandaloneSetup;
    import fr.inria.diverse.melanger.minifsmmt.minifsm.FSM;
    
    public class MainUpcast {
    	
    	public static void main(String[] args) {
    		
    		StandaloneSetup.doSetup();
    		
    		ResourceSetImpl resourceSet = new ResourceSetImpl(); 
    		MelangeResourceImpl r = (MelangeResourceImpl) resourceSet.getResource(URI.createURI("melange:/file/model/Sample.minifsm?mt=fr.inria.diverse.melanger.MiniFsmMT"),true);
    
    		FSM root = (FSM) r.getContents().get(0);
    		BasicEList<String> events = new BasicEList<String>();
    		events.add("an event");
    		root.execute(events);
    	}
    }
    

We changed the `mt` argument in the URI to see the model as a MiniFsmMT.

    
    	melange:/file/model/Sample.minifsm?mt=fr.inria.diverse.melanger.MiniFsmMT
    

And we changed the `FSM` class to manipulate the one from the super ModelType.

    
    	import fr.inria.diverse.melanger.minifsmmt.minifsm.FSM;
    

## Downcast

Lastly we will load a model conform to MiniFsm and execute it as an instance of MelangedLangMT.

Create a new file &#8220;SampleMiniFsm.xmi&#8221; in the &#8220;model&#8221; folder and fill it with:

    
    <?xml version="1.0" encoding="UTF-8"?>
    <minifsm:FSM xmi:version="2.0" xmlns:xmi="http://www.omg.org/XMI" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:minifsm="http://minifsm/minifsm/" initialState="//@states.0">
      <states name="Hello"/>
      <states xsi:type="minifsm:FinalState" name="World!"/>
      <transitions input="//@states.0" output="//@states.1" event="an event"/>
    </minifsm:FSM>
    /pre>
    
    Create a new Java class "MainDowncast.java"
    
    The code of the MainDowncast class also look like the Main class:
    
    
        
        package example;
        
        import org.eclipse.emf.common.util.BasicEList;
        import org.eclipse.emf.common.util.URI;
        import org.eclipse.emf.ecore.resource.impl.ResourceSetImpl;
        
        import fr.inria.diverse.melange.resource.MelangeResourceImpl;
        import fr.inria.diverse.melanger.StandaloneSetup;
        import fr.inria.diverse.melanger.melangedlangmt.minifsm.FSM;
        
        public class MainDowncast {
        	
        	public static void main(String[] args) {
        		
        		StandaloneSetup.doSetup();
        		
        		ResourceSetImpl resourceSet = new ResourceSetImpl(); 
        		MelangeResourceImpl r = (MelangeResourceImpl) resourceSet.getResource(URI.createURI("melange:/file/model/SampleMiniFsm.xmi?lang=fr.inria.diverse.melanger.MelangedLang&mt=fr.inria.diverse.melanger.MelangedLangMT"),true);
        
        		FSM root = (FSM) r.getContents().get(0);
        		BasicEList<String> events = new BasicEList<String>();
        		events.add("an event");
        		root.execute(events);
        	}
        }
        
    
    
    The URI is what it has changed:
    
    
    
    
    
      * 
        The loaded model is a MiniFsm one's</p>
            /file/model/SampleMiniFsm.xmi
        
    
    
      * 
        MelangedLang is not a super Language of MiniFSM so we can't directly cast the model to MelangedLangMT.  
        But Melange offer the possibility thanks to the argumentlangto create a copy of the original model but as an instance of the specified sub-Language.</p>
            lang=fr.inria.diverse.melanger.MelangedLang
        
    
    
      * 
        Thanks to the </code
        
        <pre><code>mt=fr.inria.diverse.melanger.MelangedLangMT</pre>