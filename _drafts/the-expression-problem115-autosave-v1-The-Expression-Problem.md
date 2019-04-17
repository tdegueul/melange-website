---
id: 121
title: The Expression Problem
date: 2016-01-06T11:31:05+00:00
author: tdegueul
layout: revision
guid: http://melange.inria.fr/2016/01/06/115-autosave-v1/
permalink: /2016/01/06/115-autosave-v1/
---
Showing how to solve the EP in Melange. A (possible) solution to the usual example:

Langage = structure + comportement donc EP, et on peut le réécrire au niveau lang de la manière suivante:  
MT pas directement relevant, mais permet d&#8217;avoir un treillis pour raisonner/naviguer dans une famille de langages

    
    // Base expression language
    language BaseExpr {
        syntax "BaseExpr.ecore" // abstract Expr from which Lit/Add inherit
        with EvaluateLit // weaving eval() : int on Lit
        with EvaluateAdd // weaving eval() : int on Add
    }
    
    // New variant
    language ExprWithSub inherits BaseExpr {
        syntax "Sub.ecore" // will be merged with BaseExpr.ecore to add Sub
    }
    
    // New operation
    language ExprWithPrettyPrint inherits BaseExpr {
        with PrintLit // weaving print() : String on Lit
        with PrintAdd // weaving print() : String on Add
    }
    
    // All variants, all operations
    language AllExpr inherits ExprWithSub, ExprWithPrettyPrint {
        // Les deux inherits nous donnent Expr/Lit/Add/Sub
        // avec print() et eval() sur Lit et Add, mais rien sur Sub
        // donc:
        with EvalSub
        with PrintSub
    }
    

TODO: See how Melange could also handle the Expression Families Problem.