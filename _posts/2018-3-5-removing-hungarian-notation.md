---
layout: post
title: Removing Hungarian Notation from Java project
draft: false
---
If you hate Hungarian notation for it's unreadable format, you might experience hard time replacing it into standard Google Java Style variable notation. I found myself in this situation.

There are couple ideas of how to change that:

<b>1. Manual refactor</b>

Well, it is as simple as it sounds, it may work for small project but if you have couple hundred files plagued with disease it will not be practical to spend couple hours to change them all.

<b>2. IntelliJ structural search and replace</b>

It is common idea to replace variable names automatically with IDE. It is easy to work with structural search, it will find all mVariables but structural replace is broken. [I filed a bug](https://issuetracker.google.com/issues/68843136) to issue tracker without response.

<b>3. Writing custom script</b>

If none of the above fit, our team came to conclusion that we must write custom script.
And thats what my colleague did.

<script src="https://gist.github.com/TKolbusz/1862bfa57b506a7b5e0afde4ea3e371f.js"></script>

Feel free to customize this script, add prefixes to search for as necessary. You can run it in a test case.

Caveats:

1. It will brutally replace hungarian notation in a given directory so be careful, have a backup. 
2. If you have field assignment from setter/constructor like 
{% highlight java %}
mValue = value;
{% endhighlight %}
it will replace it with 
{% highlight java %}
value = value;
{% endhighlight %} 
instead of
{% highlight java %}
this.value = value;
{% endhighlight %}
so be careful and test your code. Such bugs can be found by running IntelliJ's Structural Search with 'Variable is assigned to itself' lint.

Enjoy more readable code!
