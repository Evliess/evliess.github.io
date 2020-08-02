---
layout: index
title: "Clean Code"
category: java
date: 2020-07-05 11:03:55
---

# Refactoring

> Any fool can write code that a computer can understand. Good programmers write code that humans can understand.

1. When you have to add a feature to a program but the code is not structured in a convenient way, first refactor the program to make it easy to add the feature, then add the feature.

2. Before you start refactoring, make sure you have a solid suite of tests. These tests must be self-checking.

3. Refactoring changes the programs in small steps, so if you make a mistake, it is easy to find where the bug is.

4. When programming, follow the camping rule: Always leave the code base healthier than when you found it.

5. You have to refactor when you run into ugly code—but excellent code needs plenty of refactoring too.

6. The whole purpose of refactoring is to make us program faster, producing more value with less effort.


## Bad Smells in Code

1. Mysterious Name  

2. Duplicated Code  

3. Long Function  

4. Long Parameter List  

5. Global Data - Then, it’s good to limit its scope as much as possible by moving it within a class or module where only that module’s code can see it.

6. Mutable Data -  Use Combine Functions into Class (144) or Combine Functions into Transform (149) to limit how much code needs to update a variable. 

6. Divergent Change

7. Shotgun Surgery

8. Comments - If you need a comment to explain what a block of code does, try Extract Function (106). If the method is already extracted but you still need a comment to explain what it does, use Change Function Declaration (124) to rename it. If you need to state some rules about the required state of the system, use Introduce Assertion (302).



# Clean Code

## Meaningful Names

> Say what you mean. Mean what you say.

### Use Intention-Revealing Names

```java
//bad
int d; // elapsed time in days

//good
int daysSinceCreation;
int daysSinceModification;
int fileAgeInDays;
```

### AVOID DISINFORMATION

```java
//bad
int a = l;
if ( O == l )
  a = O1;
else
  l = 01;
```

### MAKE MEANINGFUL DISTINCTIONS

```java

//bad Noise words are redundant.

getActiveAccount();
getActiveAccounts();
getActiveAccountInfo();

```

### USE PRONOUNCEABLE NAMES

```java
//bad

class DtaRcrd102 {
  private Date genymdhms;
  private Date modymdhms;
  private final String pszqint = ”102”;
  /* … */
};

//good

class Customer {
  private Date generationTimestamp;
  private Date modificationTimestamp;;
  private final String recordId = ”102”;
  /* … */
};

```

### PICK ONE WORD PER CONCEPT

## Function

### Small

### DO one thing

### USE DESCRIPTIVE NAMES

The smaller and more focused a function is, the easier it is to choose a descriptive name.

### FUNCTION ARGUMENTS

The ideal number of arguments for a function is zero (niladic). Next comes one (monadic), followed closely by two (dyadic). Three arguments (triadic) should be avoided where possible. More than three (polyadic) requires very special justification—and then shouldn’t be used anyway. Functions that take variable arguments can be monads, dyads, or even triads. But it would be a mistake to give them more arguments than that.

```java
//It's ok
void monad(Integer… args);
void dyad(String name, Integer… args);
void triad(String name, int count, Integer… args);
```

### Flag Arguments

Flag arguments are ugly.

### Argument Objects 

When a function seems to need more than two or three arguments, it is likely that some of those arguments ought to be wrapped into a class of their own. 

### Output Arguments

In general output arguments should be avoided. If your function must change the state of something, have it change the state of its owning object.

### PREFER EXCEPTIONS TO RETURNING ERROR CODES

If you use exceptions instead of returned error codes, then the error processing code can be separated from the happy path code and can be simplified:  

```java

try {
  deletePage(page);
  registry.deleteReference(page.name);
  configKeys.deleteKey(page.name.makeKey());
}
catch (Exception e) {
  logger.log(e.getMessage());
}
```

### Extract Try/Catch Blocks

```java

public void delete(Page page) {
  try {
    deletePageAndAllReferences(page);
  }
  catch (Exception e) {
    logError(e);
  }
}

private void deletePageAndAllReferences(Page page) throws Exception {
  deletePage(page);
  registry.deleteReference(page.name);
  configKeys.deleteKey(page.name.makeKey());
}
```

### DON’T RETURN NULL, DON’T PASS NULL
Returning null from methods is bad, but passing null into methods is worse.

## Error Handling

### USE EXCEPTIONS RATHER THAN RETURN CODES

## Classes

### CLASSES SHOULD BE SMALL!

### ORGANIZING FOR CHANGE  

**Isolating from Change**
Needs will change, therefore code will change. We learned in OO 101 that there are concrete classes, which contain implementation details (code), and abstract classes, which represent concepts only. A client class depending upon concrete details is at risk when those details change. We can introduce interfaces and abstract classes to help isolate the impact of those details.
