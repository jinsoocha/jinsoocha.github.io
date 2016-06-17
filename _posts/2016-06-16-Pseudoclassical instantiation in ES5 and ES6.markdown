---
layout: post
title:  "Pseudoclassical instantiation in ES5 and ES6"
date:   2016-06-16 16:31:38 -0700
categories: frontend 
---

Classes are special functions in Javascript. A parent class is called superclass and it inherits its properties to its children called subclasses. In this post, we will go over how to write superclass and subclasses using pseudoclassical style both in ES5 and ES6. 

The example superclass we will be building is called Cat. It takes name, age and sound as arguments and has name, age, sound, cry as properties. As a convention, the class name always starts with a capital letter. The functions in pseudoclassical classes will be defined outside of the class scope, with prototype method.

This shows how to create a superclass in ES5:

{% highlight javascript %}
// ES5
var Cat = function(name, age, sound) {
  this.name = name;
  this.age = age;
  this.sound = sound;
  this.cry();
};

Cat.prototype.cry = function() {
  console.log(this.sound);
};
{% endhighlight %}

This shows how to create a superclass in ES6:

{% highlight javascript %}
// ES6
var Cat = class {
  constructor(name, age, sound) {
    this.name = name;
    this.age = age;
    this.sound = sound;
    this.cry();
  }

  cry() {
    console.log(this.sound);
  }
};
{% endhighlight %}

To actually create an instance of pseudoclasscial classes, it is required to use 'new' keyword. In this example, cry method is being invoked as soon as the class gets instantiated. For this reason, the below instantiation will return 'meow' once:

{% highlight javascript %}
new Cat('nala', 3, 'meow');
// logs 'meow' once
{% endhighlight %}

Now we have a superclass Cat, which has name, age and sound, and also cries once when it gets instantiated. Next, we will create a subclass CryingCat, which inherits all these properties and methods from Cat. On the top of them, CryingCat will have a modified version of cry method, which allows CryingCat to cry every second after the instantiation. 

This is how to create a subclass in ES5:

{% highlight javascript %}
// ES5
var CryingCat = function(name, age, sound) {
  Cat.call(this, name, age, sound);
};

CryingCat.prototype = Object.create(Cat.prototype);
CryingCat.prototype.constructor = CryingCat;

CryingCat.prototype.cry = function() {
  var context = this;
  Cat.prototype.cry.call(this);
  setInterval(function() { Cat.prototype.cry.call(context); }, 1000);
};
{% endhighlight %}

An important thing to note here is that it is required to use Object.create to correctly inherit the properties from Cat. Let's go over some other examples that are often mistaken as the right way to establish the prototype relationship between super and subclasses.

{% highlight javascript %}
//WRONG
CryingCat.prototype = Cat.prototype;
{% endhighlight %}

This sets CryingCat as the identical object as Cat. When creating a different cry function in CryingCat, the cry function in Cat also gets overwritten.

{% highlight javascript %}
//WRONG
CryingCat.prototype = new Cat();
{% endhighlight %}

This creates an object that inherits all the properties from Cat. However, new Cat() is missing the required arguments, name, age and sound. Also, this is creating an instance of Cat while what we are actually trying to do is merely establishing a prototype relationship between Cat and CryingCat.

{% highlight javascript %}
//CORRECT
CryingCat.prototype = Object.create(Cat.prototype);
{% endhighlight %}

This allows CryingCat to inherit all the properties from Cat. Also, this does not create an instance of Cat so arguments do not need to be passed. 

Another thing to note is that when creating a class, it always comes with constructor property, which refers to the class itself. When creating CryingCat, its constructor was set to CryingCat. However, Object.create overwrites CryingCat's constructor as Cat rather than CryingCat, so we need to reset CryingCat's constructor as CryingCat.

{% highlight javascript %}
CryingCat.prototype.constructor = CryingCat;
{% endhighlight %}

If you skip this step, when checking if a certain instance is an instance of CryingCat, it will return false rather than true.

This is how to create a subclass in ES6:

{% highlight javascript %}
// ES6
var CryingCat = class extends Cat {
  cry() {
    super.cry();
    setTimeout(function() { super.cry(); }, 1000);  
  }
};
{% endhighlight %}


<!-- Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/ -->
