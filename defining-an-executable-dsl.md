---
id: 32
title: Defining an executable DSL
date: 2015-10-09T20:03:49+00:00
author: tdegueul
layout: page
guid: http://localhost/wordpress/?page_id=32
---
**_This tutorial assumes you have installed Melange following the [installation instructions](http://melange.inria.fr/download/)._**

One of the core features of Melange is the support for executable metamodeling. Using Melange, one can define the syntax and semantics of her language (for example, using an interpreter). In this tutorial, we define a simple language for finite-state machine (FSM) modeling. We detail how to create the syntax of our FSM language, its semantics, and how to execute the models conforming to it.

### Abstract syntax

We use a metamodel to describe the abstract syntax of the FSM language. Because Melange relies heavily on the [Eclipse Modeling Framework](https://eclipse.org/modeling/emf/), we use Ecore for writing metamodels. First, let&#8217;s identify the concepts of our FSM language, starting from [Wikipedia&#8217;s definition](https://en.wikipedia.org/wiki/Finite-state_machine):

> A FSM is conceived as an abstract machine that can be in one of a finite number of states. The machine is in only one state at a time; the state it is in at any given time is called the current state. It can change from one state to another when initiated by a triggering event or condition; this is called a transition. A particular FSM is defined by a list of its states, and the triggering condition for each transition.
> 
>![FSM](http://upload.wikimedia.org/wikipedia/commons/d/d8/Automate-impair.png) 

From this definition, we derive the following Ecore metamodel:

<a href="http://melange.inria.fr/wp-content/uploads/2015/10/minifsm-metamodel.png" rel="attachment wp-att-172"><img class="alignnone wp-image-172" src="http://melange.inria.fr/wp-content/uploads/2015/10/minifsm-metamodel-300x178.png" alt="minifsm-metamodel" width="585" height="347" srcset="http://melange.inria.fr/wp-content/uploads/2015/10/minifsm-metamodel-300x178.png 300w, http://melange.inria.fr/wp-content/uploads/2015/10/minifsm-metamodel-768x455.png 768w, http://melange.inria.fr/wp-content/uploads/2015/10/minifsm-metamodel.png 776w" sizes="(max-width: 585px) 100vw, 585px" /></a>

####  Defining the Ecore metamodel {#modeling}

To define this metamodel, we follow the usual procedure of EMF:

  1. `File > New > Project...`
  2. `Eclipse Modeling Framework > Empty EMF Project >` `Next > Name: `“fr.inria.diverse.minifsm” > `Finish`.
  3. In the new project, right-click the ‘model’ folder and go in `New > Others...`  
    Select `Eclipse Modeling Framework > Ecore model`, then `Next`  
    Name it “minifsm.ecore” and click `Finish`
  4. The new Ecore file contains a package with no name.  
    Open the Properties view (Menu `Show View > Other... > General > Properties`)  
    Set its name and nsPrefix to “minifsm”, and its nsURI to “http://minifsm/”
  5. For each element of the FSM language add a new class in the root package: right-click > `New Child > EClass`  
    Properties of the class (such as its name) can be edited using the Properties view.
  6. To add an attribute or reference inside a class, right-click it and select `New Child > EAttribute` or `EReference`

In the end, our new metamodel should look like this:  
<a href="http://melange.inria.fr/wp-content/uploads/2015/10/minifsm.png" rel="attachment wp-att-149"><img class="alignnone size-medium wp-image-149" src="http://melange.inria.fr/wp-content/uploads/2015/10/minifsm-285x300.png" alt="minifsm" width="285" height="300" srcset="http://melange.inria.fr/wp-content/uploads/2015/10/minifsm-285x300.png 285w, http://melange.inria.fr/wp-content/uploads/2015/10/minifsm.png 323w" sizes="(max-width: 285px) 100vw, 285px" /></a>

#### Generating Java code from the metamodel

  1. Right-click the model folder, `New > Other...` and select `Eclipse Modeling Framework > EMF Generator Model`
  2. Name it “minifsm.genmodel” and put it in the model folder
  3. Select the previously created Ecore model
  4. Select its root package
  5. Open minifsm.genmodel, right-click its root and select `Generate Model Code`
  6. The corresponding Java code is generated in the src/ folder

### Defining an interpreter

To define the operational semantics of the FSM language, we  implement an interpreter for it. In this section, we use the [K3 meta-language](http://diverse-project.github.io/k3/) for that purpose.

  1. Open the menu `File > New > Project...` and select `Kermeta 3 > K3 Project`  
    Give it the name “fr.inria.diverse.minifsm.interpreter”
  2. Open the `META-INF/MANIFEST.MF` of the new project and add the project containing the FSM metamodel (fr.inria.diverse.minifsm) to its dependencies
  3. In the src/ folder, change the name of the “sample” package to “minifsm.aspects”
  4. Create a new file “minifsmAspects.xtend” in this package and open it.

The general idea of the K3 meta-language is to allow &#8220;re-opening&#8221; classes of the metamodel to insert new features (references, attributes, methods). Let&#8217;s look at a simple example for the FSM language:

    package minifsm.aspects
    
    import minifsm.Transition
    import fr.inria.diverse.k3.al.annotationprocessor.Aspect
    
    @Aspect(className = Transition)
    class TransitionAspect {
    	def boolean isActivated() {
    		return _self.event === null || _self.fsm.currentEvent == _self.event
    	}
    }

The **@Aspect** annotation takes as argument a **className** which specifies the class of the metamodel to re-open (think about it as the aspect &#8220;pointcut&#8221;). All the features of the class annotated with **@Aspect** are woven on the target class of the metamodel using static introduction: this is the aspect&#8217;s advice. In this example, a new method checking if a given transition is fireable is inserted. The **@Aspect** defines a new implicit variable named __self_ which references the base class (**Transition** in this case).

The attribute ‘currentState’ doesn’t exist in FSM but we can add it by aspect:

    @Aspect(className=FSM)
    class FSMAspect {
        	
    	public var State currentState
    	public var String currentEvent
        	
    	def void execute(EList<String> events){
    		println("Start")
        		
    		val eventIt = events.iterator
    		if(eventIt.hasNext)
    			_self.currentEvent = eventIt.next
    		_self.currentState = _self.initialState
        		
    		while(_self.currentState !== null){
    			_self.currentState.execute
    			if(_self.currentState instanceof FinalState)
    				_self.currentState = null
    			else{
    				val candidate = _self.transitions.findFirst[input === _self.currentState && isActivated]
    				_self.currentState = candidate?.output
    			}
    		}
    	}
    }
    

And of course State need execute():

    @Aspect(className=State)
    class StateAspect {
    	def void execute(){
    		println("Exec "+_self.name)
    	}
    }
    
    @Aspect(className=FinalState)
    class FinalStateAspect extends StateAspect {
    	def void execute(){
    		println("End")
    	}
    }
    

Then you need some imports at the begin of minifsmAspect.xtend to enable the call of aspect’s methods for base classes

    import static extension minifsm.aspects.FSMAspect.*
    import static extension minifsm.aspects.StateAspect.*
    import static extension minifsm.aspects.FinalStateAspect.*
    import static extension minifsm.aspects.TransitionAspect.*
    

For more details, have a look at the [Kermeta 3 documentation](http://diverse-project.github.io/k3/)

## Define a Melange Language

We have a project defining the domain model and another defining its interpreter.  
We will now define a Melange Language to assemble theses two concerns in one entity.

The first step is to create a Melange project:

  1. Open the menu `File > New > Project...` and select `Melange > Melange Project`, then click `Next`.![new project](https://raw.githubusercontent.com/diverse-project/melange/gh-pages/images/newproject-1.png)
  2. Name the project “fr.inria.diverse.melange.fsm” and click `Next`.![name project](https://raw.githubusercontent.com/diverse-project/melange/gh-pages/images/newproject-2.png)
  3. Check `Create a plug-in using one of the templates` and select `Simple Melange project`, then `Next`.![add template](https://raw.githubusercontent.com/diverse-project/melange/gh-pages/images/newproject-3.png)
  4. Set “fr.inria.diverse.melanger” as package, and change language name to MiniFSM. Browse the location to select minifsm.ecore from the “fr.inria.diverse.minifsm” project, then `Finish`.![configure project](https://raw.githubusercontent.com/diverse-project/melange/gh-pages/images/newproject-4.png)
  5. Open the `META-INF/MANIFEST.MF` and in the `dependencies` tab add Plug-ins 
      * fr.inria.diverse.minifsm.interpreter  
        fr.inria.diverse.minifsm should be already there thanks to the wizard.  
        This step is needed to have both metamodel and interpreter projects in the classpath of the Melange project.

You should have these declarations in the .melange file:

<pre><code class="melange">language MiniFSM {
	syntax "platform:/resource/fr.inria.diverse.minifsm/model/minifsm.ecore"
	exactType MiniFSMMT
}
</code></pre>

If you look at the outline at the right side, you can see the language typed by its own ‘exactType’.

Each language is defined by an .ecore file but remember we added behaviors with Aspects. So we have to put them also in the languages declaration.

<pre><code class="melange">language MiniFsm {
	syntax "platform:/resource/fr.inria.diverse.minifsm/model/minifsm.ecore"
	with minifsm.aspects.FinalStateAspect
	with minifsm.aspects.TransitionAspect
	with minifsm.aspects.StateAspect
	with minifsm.aspects.FSMAspect
}
</code></pre>

As you see the keyword **with** refer to the class with the **@Aspect**

Do a right click on the .melange file and select `Melange > Language Runtime`.  
The new created project contains the metamodel and the aspects declared in the .melange file. They are simple copies from the domain model project and the Kermeta project but you can note that the nsURI of the metamodel and packages of the aspects are differents.  
In more complex Languages this content is the result of the differents operators applied (such as merge or slice)

## Using the language runtime

Now we will test if the MiniFSM works fine.  
To do so, we will declare a **transformation** that will load a MiniFSM model an execute it.

<pre><code class="melange">@Main
transformation main() {
	val events = newArrayList('an event')
	
	val model = MiniFSM.load("input/Sample.xmi")
 	val fsm = model.contents.head as minifsm.minifsmmt.FSM

	fsm.init()
	fsm.eval(events)
}
</code></pre>

Here we have:

  * MiniFSM.**load(**<file.xmi>**)**  
    To load a model conform to MiniFSM
  * **@Main**  
    In Melange we can declare several **transformations**. To set one of them the entry point we tag it with **@Main**.
  * model.**contents.head** as minifsm.minifsmmt.FSM  
    We get the first element of the model and cast it to the right type.  
    _minifsm.minifsmmt_ is the package containning all classes of the **exactType** of the MiniFSM language.

To launch the **main()**, look inside the folder `src-gen` (where Melange generates Java files) and inside the package _minifsm_, right click on **Main.java** and select `Run As > Java Application`.