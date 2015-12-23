# item-47:-define-non-member-functions-inside-templates-when-type-conversions-are-desired

When writing a class template that offers functions related to the
template that support implicit type conversions on all  parameters,
define those functions as friends inside the class template.

In Item 24 it gave the advice that if you want to support binary
operator overloads for both sides of the equation, e.g. foo * 2 (which
would call Foo::operator*(const Foo& foo) if it defined one) as well as
2 * foo (no 2::operator*(const Foo& foo), what to do?), you should
define a non-member non-friend function so that implicit conversion can
happen on either argument:

class Foo
 {
public:
    
     Foo(int x)
     : x_(x)
     { }
    
     int x() const
     {
         return x_;
     }
    
 private:
    
     int x_;
 };
Foo operator* (const Foo& left,
                const Foo& right)
 {
     return left.x() * right.x();

}

Now we can do 2 * foo as well as foo * 2, as well as foo1 * foo2 and
foo2 * foo1.

But for class template, this won’t work, because template argument
deduction does not consider implicit conversions:

template<typename T>
class Foo
 {
public:
    
     Foo(const T& x)
     : x_(x)
     { }
    
     T x() const
     {
         return x_;
     }
    
 private:
    
     T x_;
 };
template<typename T>
Foo<T> operator* (const Foo<T>& left,
                   const Foo<T>& right)
 {
     return left.x() * right.x();

}

If we declare an object Foo<int> foo(2) now and try to do what we did
before, so either 5 * foo or foo * 5, this will fail, because for the
template function, one of the arguments can be deduced to Foo<int>, but
the other is just int, which doesn’t fit. The problem is that the int
doesn’t implicitly convert to Foo<int>. The solution is to declare this
non-member non-friend function as a friend inside the template class
Foo:

    // Foo is shorthand for Foo<T> inside the class itself

    friend Foo operator* (const Foo& left,
                           const Foo& right)
     {
         return left.x() * right.x();

    }

The only way to declare a non-member function inside a class is to make
it a friend. It doesn’t actually have to make us of its ability to
access internals of the class as a friend. It’s just the only way to
make it non-member. Now, whenever a class of Foo is instantiated, such
as Foo<int>, the compiler will also instantiate a copy of this method
for Foo<int>. This is then no longer a function template, but a normal,
declared function. Therefore, implicit conversions work again and we can
call 2 * foo or foo * 2!

Also: all methods of a class that are defined inside the class are
implicitly declared inline.
