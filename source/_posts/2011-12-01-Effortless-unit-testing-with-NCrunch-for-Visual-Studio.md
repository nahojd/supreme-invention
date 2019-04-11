---
title: "Effortless unit testing with NCrunch for Visual Studio"
extid: posts/8193
author: johan
date: 2011-12-01 23:24:47
updated: 2011-12-01 23:24:48
slug: Effortless-unit-testing-with-NCrunch-for-Visual-Studio
tags: [tdd, test, ncrunch, tools, visual studio]
---

I’ve been using a great little Visual Studio plugin lately, called [NCrunch](http://www.ncrunch.net/). It’s a continuous test runner, meaning that it finds and runs all my unit test in the background all the time. And whenever I change some code, it knows what tests (if any) are effected, and re-runs those tests. It also lets me see exactly what tests are covering a particular line of code. And I don’t even have to save my files for it to work! 

I’ll give you a small example. Say that I’m writing a class, let’s call it [Foo](http://en.wikipedia.org/wiki/Foobar), with a method, let’s call it Bar, that takes a number as a parameter, and if it is a multiple of three doubles it. Otherwise, it just returns it. Very useful. Anyway, I start by writing a test for the simple case of just returning a number.

[![Failing test](/images/Windows-Live-Writer/9b5b0960a0ac_14132/Failing%20test_thumb.png "Yes, I know that this test only tests that the number 1 returns 1. This is not a TDD course.")](/images/Windows-Live-Writer/9b5b0960a0ac_14132/Failing%20test.png)

See the little red dots, and the x to the left? That’s NCrunch telling me that the test is being run, but is failing at line 19. What’s more, if I hover over the x, I get this helpful little box telling me why it failed:

[![Failed test hover](/images/Windows-Live-Writer/9b5b0960a0ac_14132/Failed%20test%20hover_thumb.png "Oh, noes!")](/images/Windows-Live-Writer/9b5b0960a0ac_14132/Failed%20test%20hover_2.png)

Ok, so the test is failing because Bar(1) is returning 0, instead of the expected 1. Let’s look at the method:

[![Bar returning 0](/images/Windows-Live-Writer/9b5b0960a0ac_14132/Bar%20returning%200_thumb.png "I prefer implementing my methods by returning the default value instead of throwing a NotImplementedException.")](/images/Windows-Live-Writer/9b5b0960a0ac_14132/Bar%20returning%200.png)

Aha, no wonder it’s returning 0! But look at those little red dots again! This time they show me that this code is, in fact, covered by tests, but unfortunately at least one of them are failing. If I click one of the red dots I can find out exactly what tests are failing (or not failing):

[![Failing coverage](/images/Windows-Live-Writer/9b5b0960a0ac_14132/Failing%20coverage_thumb.png "If the line of code was covered by more test, this box would list them all.")](/images/Windows-Live-Writer/9b5b0960a0ac_14132/Failing%20coverage_2.png)

Ok then, let’s fix the failing test. Let’s return the number instead.

[![green coverage](/images/Windows-Live-Writer/9b5b0960a0ac_14132/green%20coverage_thumb_1.png "Or maybe I should have just returned 1, since that is all I'm testing for... Oh, right, still not a TDD course.")](/images/Windows-Live-Writer/9b5b0960a0ac_14132/green%20coverage_1.png)

The dots immediately turns green, indicating that this code is covered only by successful tests! And I didn’t even save the file first (as you can see by the asterisk). Awesome! Let’s look at the test again:

[![green test](/images/Windows-Live-Writer/9b5b0960a0ac_14132/green%20test_thumb_1.png "Win!")](/images/Windows-Live-Writer/9b5b0960a0ac_14132/green%20test_1.png)

All green here as well! Great, let’s move on. Wasn’t the Bar method supposed to double the number when it was a multiple of three? Let’s add that code! (Yes, I know that you’re supposed to write the test first, but that’s kind of the point…)

[![black dot](/images/Windows-Live-Writer/9b5b0960a0ac_14132/black%20dot_thumb.png "And there, the very useful method is done! Feel free to use it in your own projects!")](/images/Windows-Live-Writer/9b5b0960a0ac_14132/black%20dot.png)

Now I have a black dot! This means that this particular line is not covered by any tests at all! Bad TDD-Johan!

I think I’ll end my example there. I’ve been using NCrunch for a few weeks now, and I love it! It just makes the test running completely effortless. And whenever I have to back and refactor some old code it makes it very easy to find out what tests are covering it (if any). The only possible downside is that you do need a pretty fast computer for it to run, preferably a quad core.
