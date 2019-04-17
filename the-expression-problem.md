---
id: 115
title: The Expression Problem
date: 2015-12-15T18:21:16+00:00
author: tdegueul
layout: page
guid: http://melange.inria.fr/?page_id=115
---
Showing how to solve the EP in Melange. A (possible) solution to the usual example:

    
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