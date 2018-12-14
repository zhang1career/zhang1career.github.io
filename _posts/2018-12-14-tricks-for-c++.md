---
layout: post
title:  Tricks for C++
date:   2018-12-14 10:27:00 +0800
categories: c++
---

> 


## 1. unordered_map[^1]

### use vector as key[^n]

When using vector<int> as key, definition of hash is needed.

Define our hash function:

{% highlight c++ %}
template <typename Container>
struct container_hash {
    std::size_t operator()(Container const& c) const {
        return boost::hash_range(c.begin(), c.end());
    }
};
{% endhighlight %}

Then use like this:

{% highlight c++ %}
std::unordered_map<keyVector, int, container_hash<keyVector>> map;
{% endhighlight %}

keyVector is defined here:
{% highlight c++ %}
typedef std::vector<int> keyVector;
{% endhighlight %}

## Reference
[^1]: http://www.cplusplus.com/reference/unordered_map/unordered_map/
[^n]: https://stackoverflow.com/questions/10405030/c-unordered-map-fail-when-used-with-a-vector-as-key
