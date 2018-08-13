---
layout: post
title: unit test introduction
date: 2018-08-10
categories: unit test
---


## what is UT actually looked like

```java
@Test
public void should_pring_success_given_list(){
	//given
	Purchase purchase = new Puchase(Arrays.asList(new Milk(),new Book()));
    Print print = new Print(puchase);
    //when
    String result = print.printPurchase();
   //then
   assertThat(result).contains("milk","book");
} 
```

## compare UT with other tests

### what does it mean by "unit" of a unit test

First , unit tests are low-level , focusing on a small functional point
second , unit tests should completed by developer who are responsible for implement functionality
third , unit testes should run faster more than other kinds of tests , like : Service Tests , UI tests


### compare unit test with service test and UI test


![unit-test-compare-with-other-tests]({{site.url}}/asserts/image-unit-test.png "unit test compare with other tests")


## why to write UT

test

### brain overload

test

### shotgun change

test

### broken safenet

test

### hard to hit the root cause of the problem

test

### high risk to make change

test

## the ROI of automation change

testddd
