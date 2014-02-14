# JavaScript Array: slice vs splice

In JavaScript, mistaking `slice` for `splice` (or vice versa) is a common
mistake among rookies and even experts. These two functions, although they have
**similar names**, are doing two **completely different** things. In practice,
such a confusion can be avoided by choosing an API that telegraphs the const-
correctness of the function.

Array’s `slice` (ECMAScript 5.1 Specification Section [15.4.4.10][1]) is quite
similar to String’s `slice`. According to the specification, `slice` needs to
accept two arguments, *start* and *end*. It will return **a new array**
containing the elements from the given start index up the one right before the
specified end index. It’s not very difficult to understand what slice does:

    'abc'.slice(1,2)           // "b"
    [14, 3, 77].slice(1, 2)    //  [3]

An important aspect of slice is that it **does not change** the array which
invokes it. The following code fragment illustrates the behavior. As you can
see, x keeps its elements and y gets the sliced version thereof.

    var x = [14, 3, 77];
    var y = x.slice(1, 2);
    console.log(x);          // [14, 3, 77]
    console.log(y);          // [3]

Although `splice` (Section [15.4.4.12][2]) also takes two arguments (at
minimum), the meaning is very different:

    [14, 3, 77].slice(1, 2)     //  [3]
    [14, 3, 77].splice(1, 2)    //  [3, 77]

On top of that, `splice` also **mutates** the array that calls it. This is not
supposed to be a surprise, after all the name splice implies it.

    var x = [14, 3, 77]
    var y = x.splice(1, 2)
    console.log(x)           // [14]
    console.log(y)           // [3, 77]

When you build your own module, it is important to choose an API which minimizes
this *slice* vs *splice* confusion. Ideally, the user of your module should not
always read the documentation to figure out which one they really want. What
kind of naming convention shall we follow?

A convention I’m familiar with (from my past time involvement with Qt) is by
choosing the right form of the verb: *present* to indicate a possibly modifying
action and past participle to return a new version without mutating the object.
If possible, provide a pair of those methods. The following example illustrates
the concept.

    var p = new Point(100, 75);
    p.translate(25, 25);
    console.log(p);       // { x: 125, y: 100 }
 
    var q = new Point(200, 100);
    var s = q.translated(10, 50);
    console.log(q);       // { x: 200, y: 100 }
    console.log(s);       // { x: 210, y: 150 }

Note the difference between `translate()` that moves the point (in 2-D Cartesian
coordinate system) and `translated()` which only creates a translated version.
The point object *p* changed because it calls `translate`. Meanwhile, the object
*q* stays the same since `translated()` does **not modify** it and it only
returns a **fresh copy** as the new object *s*.

If this convention is used consistently throughout your application, that kind
of confusion will be massively reduced. And one day, you can let your users sing
*I Can See Clearly Now* happily!

[1]: http://es5.github.io/#x15.4.4.10
[2]: http://es5.github.io/#x15.4.4.12