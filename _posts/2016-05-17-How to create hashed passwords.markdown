---
layout: post
title:  "How to create hashed & salted passwords with Node.js Crypto library"
date:   2016-05-17 16:31:38 -0700
categories: backend 
---
For backend developers, it is important to establish the secure authentication systems to protect the users. If the supplied passwords from users are stored in the bare form, user information can be easily exposed to anybody who has access to the database. For this reason, passwords are often hashed and salted.

Hashing is a process that transforms string into an alternate representation that cannot be restored to its original form. For example, if a user inputs the password called "awesome", then the password goes through the hashing function, which produces an alternate string such as "fdjafhlqtejl210135jlhjafd3". 

To provide another layer of protection, the password can be salted first and the salted password gets hashed afterwards. Salting is a process of adding the random bits of information to the supplid password. For example, when a uesr provides the password called "awesome", it gets salted, producing "awesome83938825ae3". Then it gets hashed, returning an alternate string, "qtjhlaeajhlejahflkjasfjjqj". 

Node.js has its own library called Crypto to make hashing and salting easier:

{% highlight javascript %}
//initialize Crypto in Node.js
var crypto = require('crypto');

var salt = function() {
	return crypto.randomBytes(128).toString('base64');
};

var hash = function(password) {
	password = password + salt();
	var shasum = crypto.createHash('sha1');
	shasum.update(password);
	return shasum.digest('hex');
};

hash(password);
//returns the hashed & salted password
{% endhighlight %}

<!-- Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/ -->
