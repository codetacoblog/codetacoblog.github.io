---
layout: post
title:  "Team Standards Solve Problems"
date:   2014-11-20 09:45:00 -0600
categories: articles
tags: process teamwork management
---

Starting out on a new team can prove difficult, especially in software, where every individual and team has their own opinions on how to get work done and what constitutes good work.

I recently left a company that does not maintain development standards and accepted a new position at The Nerdery.

The Nerdery has a set of standards for every team's discipline. For example, my primary discipline is JavaScript, with a secondary emphasis in HTML/CSS, so I have two sets of standards at my disposal. When I started digging in to the documentation, I realized this investment by other, more knowledgeable engineers would pay off in several big ways.

## Why Implement Team Standards?

On-boarding a new software engineer should be an efficient and conflict-free process. Team Standards are the best way to implement efficient on-boarding for three reasons:

### 1. Standards benefit the business

I was billable in a matter of hours in my first week at The Nerdery because I could enter a codebase and know what was expected of me. I self-reviewed my code against standards before checking it in. When I had a question for my team lead, it was to clarify a rule, rather than to define one from the ground up.

This drastically cuts down on the wasted time at the beginning of a new hire's introduction to a company, and as a consequence, makes engineer churn less of an issue. A company with standards can scale faster and make up for the loss of established employees with less effort.

### 2. Standards benefit the engineering team

In previous positions, I have been brought in to a team only after working through initial fake projects and getting feedback from other engineers. There is no need for this with a clearly defined set of instructions for meeting the level of code quality expected by your team. With standards, the new engineer does not disrupt interoperability<sup id="footnote-src-1"><a href="#footnote-1">1</a></sup> in the code.

### 3. Standards benefit the new engineer

Learning the important concepts that drive the creation of a standard is just as valuable as following the standard. This is the popular Why Not What<sup id="footnote-src-2"><a href="#footnote-2">2</a></sup> leadership paradigm. A new engineer hits the ground running with standards, but also has a chance to grow in their discipline.

## What Should Standards Look Like?

### Issue : Rule : Example

I think this is largely open to interpretation, but it seems like a great place to start is the issue : rule : example format. A good showcase of this format is [@mdo](https://twitter.com/mdo)'s [codeguide](http://codeguide.co/). Here is an issue he identified:

> Reducing markup

and now the rule:

> Whenever possible, avoid superfluous parent elements when writing HTML. Many times this requires iteration and refactoring, but produces less HTML. Take the following example:

and finally, the example:

{% highlight html %}
<!-- Not so great -->
<span class="avatar">
  <img src="...">
</span>

<!-- Better -->
<img class="avatar" src="...">
{% endhighlight %}

### Rule : Example

Including an issue is helpful and makes reading standards easy, but there is a legitimate argument for not including one. If all the standards provide are a rule : example format, the new engineer is forced to think about why the rule was made. It's a simple teaching method, and I know firsthand it is an effective one. Here is a rule from The Nerdery's Front End Development Standards pertaining to SCSS:

> Do not use @extend.

and now the example:

{% highlight scss %}
/* DO */
.message {
    padding: 10px;
    border: 1px solid #cccccc;
}

.message_success { color: #00ff00; }
.message_error { color: #ff0000; }

/* DO NOT*/
.message {
    padding: 10px;
    border: 1px solid #cccccc;
}

.message_success {
    @extend .message;
    color: #00ff00;
}

.message_error {
    @extend .message;
    color: #ff0000;
}
{% endhighlight %}

When I first saw this part of the standards, I didn't understand why senior engineers did not want me to use `@extend`. In my past experiences, it had seemed like a helpful tool for extending a class. When I thought about the rule in the context of the other Front End standards, however, I realized `@extend` had the potential to create CSS relationships in the stylesheet output that defeat the module patterns engineers should be creating in their markup and styles.<sup id="footnote-src-3"><a href="#footnote-3">3</a></sup> For example:

{% highlight scss %}
.box {
    padding: 10px;
    border: 1px solid #cccccc;
}

 /* Message Module */
.message {
  @extend .box;
}

.message_success {
    @extend .box;
    color: #00ff00;
}

.message_error {
    @extend .box;
    color: #ff0000;
}

 /* Feature Module */
.feature {
    @extend .box;
}

.feature_dark {
    @extend .box;
    background: #000000;
}
{% endhighlight %}

Alone, these modules look unrelated, as they should. But when we look at the output:

{% highlight scss %}
.message,
.message_success,
.message_error,
.feature,
.feature_dark {
    padding: 10px;
    border: 1px solid #cccccc;
}

.message_success {
    color: #00ff00;
}

.message_error {
    color: #ff0000;
}

.feature_dark {
    background: #000000;
}
{% endhighlight %}

Perhaps unexpectedly, these two unrelated modules are forced into the same stack of selectors because `@extend` outputs them that way when they share the same property and value pairs. An issue statement could have explained that to me, but I may have glossed over it, unlike when I was confronted with the stark rule : example format, which forced me to think critically.

## Use Standards

I've seen first-hand this practice solving problems for businesses, teams, and individuals. Some of the stress points I mentioned in this post:

- On-boarding a new engineer
- Employee churn
- Interoperability

Identify those issues in your business or team, and then resolve them using a set of standards.

<hr>

<p id="footnote-1"><a href="#footnote-src-1">1</a> With respect to software, the ability to exchange and make use of information in a program. <a href="http://en.wikipedia.org/wiki/Interoperability#Software">[source]</a></p>

<p id="footnote-2"><a href="#footnote-src-2">2</a> There is a popular <a href="http://ted.com/talks/simon_sinek_how_great_leaders_inspire_action">TED Talk</a> on the subject.</p>

<p id="footnote-3"><a href="#footnote-src-3">3</a> A senior engineer later linked the team to <a href="http://csswizardry.com/2014/11/when-to-use-extend-when-to-use-a-mixin/">this article</a>, which explains the <code>@extend</code> no-no thoroughly.</p>

