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

1.  unit tests are low-level , focusing on a small functional point
2.  unit tests should completed by developer who are responsible for implement functionality
3.  unit testes should run faster more than other kinds of tests , like : Service Tests , UI tests


### compare unit test with service test and UI test


![unit-test-compare-with-other-tests]({{ site.url }}/assets/image-unit-test.png "unit test compare with other tests")

Unit tests usually tests the functionality olny includes POJO and is the faster , A unit test's cover range is small . The numbers of unit tests is the bigger . the coverage can give is high
The other types of test  , including service test and UI test , can only provide  limited coverage rate , and their coast are higher , so their amount is relatively smaller , and they are run slowly , so they can not give a feedback quickly to the developer.

![business/technology facing]({{ site.url }}/assets/unit-test-business-facing.jpg "business technology facing")

There are lots of tests in this industry, in the above picture, we can see them classified in four quadrants. 

1. The upper quadrants(Q2,Q3) are mainly to verify the function is built according to the requirement, so it is called "business-facing". 

2. The lower quadrants(Q1, Q4) are for the internal quality of our delivery, which means if the code is built in a expressive, low coupling, high cohesive way.

3. The left quadrants(Q1, Q2)  are for supporting the development team to deliver better and easier.

4. The right quadrants(Q3, Q4) are for verifying the quality of the system as whole by involing test team or user to do the test


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
