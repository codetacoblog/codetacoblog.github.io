---
layout: post
title:  "An Example of the Importance of Using Getters and Setters"
date:   2016-01-27 09:45:00 -0600
categories: articles degreef
tags: software-design
author: degreef
authorname: "Dan DeGreef"
---

“Getters” and “setters” are methods on an object that allow a client to access and manipulate the properties of that object. A key aspect of designing classes that use getters and setters is that each property on the object should be private. This means that access to the property, both reading and writing, is controlled through the object’s methods. This is known as encapsulation.

Not Using Getters and Setters:

{% highlight php %}
<?php

class Building {
  public $address;
}

$house = new Building();

$house->address = "123 Fake St.";

echo $house->address;
{% endhighlight %}

Using Getters and Setters:

{% highlight php %}
<?php

class Building {

    public $address;

}

$house = new Building();

$house->address = "123 Fake St.";

echo $house->address;
{% endhighlight %}

Using Getters and Setters:

{% highlight php %}
<?php

class Building {

  private $address;

  public function getAddress()

  {

    return $this->address;

  }

  public function setAddress($newAddress)

  {

    $this->address = $newAddress;

  }
}

$house = new Building();

$house->setAddress("123 Fake St.");

echo $house->getAddress();
{% endhighlight %}

## Why are Getters and Setters Important?

It is a widely-discussed topic that I find myself participating in every so often. “Should I take the easy and fast route and just directly access an object’s property, or should I spend the time to create getters and setters and mark the property as private and make sure to use these throughout my application?” Usually the temptation to directly access the property arises when the programmer thinks the system is small and that there is nothing interesting or complicated about a particular property that would warrant using getters and setters.

Although it is easy to skip the encapsulation and directly access the properties of an object, it is very likely that you will run in to problems down the road. Imagine that you have set a property on an object as a string, and you have used this property around your application in hundreds of places. Then a requirement changes and that string property now needs to be an array, or an integer, or needs to be broken into multiple properties. Now all of these instances of directly accessing the property and treating it as a string will fail. It is usually very difficult to find each instance and time consuming to write tests that would catch the failures. That is just one of the issues caused by directly accessing properties on objects.

## A Real World Example


I was recently working on an application that returned a collection of people for a particular call. Each person, as part of its properties, had an array of groups in which this person belonged. This array of groups was directly accessed throughout the application with the assumption that the groups would always be present (or an empty array if the person belonged to zero groups). This was seemingly harmless and had been working fine for years.

But as the application grew, this collection of people and the groups in which they belonged grew to a large amount of data and it became a rather slow call. So the decision was made to stop including the groups as part of the person’s properties by default in the collection. This made the application much faster, but required the client to call a new getGroups method on a person object if the groups were needed. This method fetched the groups from the database and returned them. This was a fine decision in isolation and it made the application faster. But alas! So many bugs popped up due to the groups array not being present where the application assumed it would be present! This was unexpected because, after all, there was no change in the data model. The groups was still an array, all that changed was a single collection fetching method stopped fetching groups by default. This ultimately caused a handful of bugs in production and required changes in dozens of locations throughout the application. That is considered a big problem!

## How This Problem Arises

Original Class Definition:

{% highlight php %}
class Person {

    public $groups;

}
{% endhighlight %}

Original Usage:

{% highlight php %}
foreach ($person->groups as $group) {

  echo $group;

}
{% endhighlight %}

As you can see, this will cause an error if groups is not present on the person object. And if we were to check for the presence of groups before performing this loop, and then fetching the groups if needed, it would be a real mess! This would be a major break down in encapsulation. It would look something like this:

{% highlight php %}
if ($person->groups === null) {
  // populate the person's groups...

  $person->groups = someFunctionToFindTheGroups();
}

foreach ($person->groups as $group) {
  echo $group;
}
{% endhighlight %}

What a headache! I don’t even want to try to remember all that every time I want to make use of a person’s groups. Now, we could avoid all of this by using a getGroups method on Person:

{% highlight php %}
Class Person {

  private $groups;

  public function getGroups()

  {

    return $this->groups;

  }
{% endhighlight %}

New Usage:

{% highlight php %}
foreach ($person->getGroups() as $group) {

  echo $group;

}
{% endhighlight %}

If we had started with this, we would find ourselves in a much better situation when we try to implement lazy loading of a person’s groups. In fact, we would only need to make a change in one location! The getGroups method would need to be changed to check for and fetch the groups before returning them:

{% highlight php %}
Class Person {

    private $groups;

    public function getGroups()

    {

        if ($this->groups === null) {

            // fetch the groups from the database

            $this->groups = $this->fetchGroupsFromDatabase();

        }

        return $this->groups;

    }

}
{% endhighlight %}

We don’t have to change our usage of the class at all! The object will fetch its groups when it is required without the application checking for their presence. This is the power of encapsulation and using getters and setters.

## What I Learned

I learned that there are a wide variety of code changes that will affect the state of an object’s properties. It is best to future-proof your application by making smart use of encapsulation techniques like getter and setter methods before the problem becomes overly expensive to change.
