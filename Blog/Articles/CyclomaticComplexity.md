---
title: Cyclomatic Complexity
author: Peter Gregg
description: Cyclomatic Complexity
image: https://dummyimage.com/800x600/000/fff&text=placeholder
thumbnail: https://dummyimage.com/200x200/000/fff&text=placeholder
type: article
status: draft
published: 2021/07/10 16:00:00
categories: 
  - Cyclomatic Complexity
---

# Cyclomatic Complexity

## What is it?
Thomas McCabe, Sr., who developed CC in 1976 defines it as measuring "the amount of decision logic in a source code function" NIST235. In simple terms, complex functions contain many decisions that have to be made.

The formula for calculating the CC of a single function or method is CC = E - N + 2.
- N - nodes (lines of code)
- E - edges (possible decisions)

The CC for the below code is 3 (=5 - 4 + 2) 

```c#
public void decision(int c1, int c2) { 
    if (c1 < 100) 
        return 0; 
    else if (c1 + C2 > 500)         
        return 1; 
    else 
        return -1; 
}
```

## Why should it be Analyzes?
- Cycloamatic complexity can be used to understand how difficult code could be to test, maintain or troubleshoot.  
- How likely the code will be to produce errors 
- How error prone may be - probability of errors
- Code reuse to remove duplication
- Helps improve readability of code
- Decreases complexity

## How high, how complex should code be


## Who?
When writing code it is best practice to analyse it, however it is most beneficial

If you have small team with little or no documentation you should aim for a complexity level of 5 - 10 

## Where

Visual Studio
Azure DevOps