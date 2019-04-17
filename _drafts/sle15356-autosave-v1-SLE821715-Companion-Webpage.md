---
id: 361
title: 'SLE&#8217;15 Companion Webpage'
date: 2016-10-17T09:35:33+00:00
author: tdegueul
layout: revision
guid: http://melange.inria.fr/2016/10/17/356-autosave-v1/
permalink: /2016/10/17/356-autosave-v1/
---
<pre><code class="melange">@Main
transformation main() {
	val events = newArrayList('an event')
	
	val model = MiniFSM.load("input/Sample.xmi")
 	val fsm = model.contents.head as minifsm.minifsmmt.FSM

	fsm.init()
	fsm.eval(events)
}
</code></pre>

The archive file containing the Eclipse workspace used for the case study can be downloaded here: [SLE15.tar.gz](http://melange-lang.org/resources/SLE15.tar.gz).