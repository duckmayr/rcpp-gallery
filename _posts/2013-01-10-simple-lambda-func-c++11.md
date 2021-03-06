---
title: A first lambda function with C++11 and Rcpp
author: Dirk Eddelbuettel
license: GPL (>= 2)
tags: basics c++11 featured
summary: This post shows how to play with lambda functions in C++11 
layout: post
src: 2013-01-10-simple-lambda-func-c++11.Rmd
---                                                                                                                                                          
[Yesterday's post](../first-steps-with-C++11) started to explore the nice
additions which the new C++11 standard is bringing to the language. One
particularly interesting feature are <em>lambda functions</em> which resemble
the anonymous functions R programmers have enjoyed all along.  This shows a
simple example.

First, we again make sure the compiler knows that we want C++11. We can either 
set the flag directly: 


{% highlight r %}
Sys.setenv("PKG_CXXFLAGS"="-std=c++11")
{% endhighlight %}


or rely on Rcpp (version 0.10.3 or newer) to do it for us via the plugin.

We will revisit an [earlier example on `stl::transform`](../stl-transform)
but use a lamba function


{% highlight cpp %}

#include <Rcpp.h>

using namespace Rcpp;

// Enable C++11 via this plugin (Rcpp 0.10.3 or later)
// [[Rcpp::plugins("cpp11")]]

// [[Rcpp::export]]
std::vector<double> transformEx(const std::vector<double>& x) {
    std::vector<double> y(x.size());
    std::transform(x.begin(), x.end(), y.begin(), 
                   [](double x) { return x*x; } );
    return y;
}
{% endhighlight %}


In this example, the function being swept over all elements of `x` does not
have to be declared as a separate function as [we did here](../stl-transform)
but can be defined <em>inline</em> as we would in R. The return type is
deduced automatically, similar to the use auto `auto` in the [previous C++11
example](../first-steps-with-C++11). We can run the example:


{% highlight r %}
x <- c(1,2,3,4)
transformEx(x)
{% endhighlight %}



<pre class="output">
[1]  1  4  9 16
</pre>


Unsurprisingly, the result is the same.  We can also retake the [second
example](../stl-transform) from the previous post:


{% highlight cpp %}

#include <Rcpp.h>

using namespace Rcpp;

// [[Rcpp::export]]
NumericVector transformEx2(NumericVector x, NumericVector y) {
    NumericVector z(x.size());
    std::transform(x.begin(), x.end(), y.begin(), z.begin(), 
                   [](double x, double y) { return sqrt(x*x + y*y); } );
    return z;
}
{% endhighlight %}


It also matches the previous result.


{% highlight r %}
x <- c(1,2,3,4)
y <- c(2,2,3,3)
transformEx2(x,y)
{% endhighlight %}



<pre class="output">
[1] 2.236 2.828 4.243 5.000
</pre>




Once again, we need to remind the reader that this still requires setting the
`-std=c++11` option for `g++`, and that CRAN will not allow this in uploads,
at least not yet. In the meantime, C++11 can of course be used for non-CRAN
projects.

