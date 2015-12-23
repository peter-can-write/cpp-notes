# use-constexpr-whenever-possible

constexpr has different semantics when applied to objects than when
applied to functions. Generally, however it indicates that the
expression (function or object) should evaluated at compile-time.

FOR OBJECTS:

constexpr makes an object not only const, but also makes it a
LiteralType, i.e. an object whose value is known and can be used at
compile time. The compile-time evaluation of a constexpr object means it
can be used to specify array-sizes (built-in array), numeric template
arguments and thus, for example, also the size of a std::array. Note
that while constexpr means const, const does not mean constexpr, i.e.
you will not _necessarily_ be able to do these things with const
objects:

constexpr int x = 5;
 std::array<std::string, x> array; // OK

double doubles[x]; // OK

But:

int x = 5;

const int y = x;

std::array<std::string, y> array; // Error, y is not known (even though
const -> not constexpr!)

FOR FUNCTIONS:

A vital thing is that constexpr functions have two faces:

-   When called with constexpr arguments, it produces a
    constexpr result.
-   Else it acts as a normal function.

This is fantastic, because it means that you can declare a function
constexpr without any loss in functionality from before, while _only
adding functionality_ for the constexpr-arguments case. There are a few
attributes of constexpr functions, which differ between C++11 and C++14:

-   C++11
    -   Can contain only one single statement, which returns a
        value. However this function may:
        -   Use recursion to replace loops.
        -   Use the ternary operator (?:) to replace
            conditional statements.
    -   When a member function, always makes it const.
    -   Cannot return void (because void is not a LiteralType in C++11).
-   C++14
    -   Can contain anything
    -   Can return anything, including void
    -   When a member function, no longer implicitly const.

This is how you could construct a _pow(base, exponent)_ function in
C++11, with its stricter requirements:

template<typename T, typename U>
constexpr int power(const T& base, const U& exponent) noexcept
 {
return exponent ? base * power(base, exponent - 1) : 1;

}

And this is how you could do it, more expressively (arguably), in C++14:

template<typename T, typename U>
constexpr auto power(const T& base, const U& exponent) noexcept
 {
decltype(base * base) result = 1;
for (std::size_t i = 0; i < exponent; ++i) result *= base;
return result;

}

Also user-defined types can be made LiteralType and used at
compile-time, when their constructor is declared constexpr:

template<typename T = std::size_t>
class Point
 {
public:
constexpr Point(const T& x, const T& y) noexcept
 : _x(x)
 , _y(y)
 { }
private:
 T _x;
 T _y;

};

Now, you can declare a Point that is known at compile-time:

constexpr Point<> point(4, 5);

Furthermore, you can make its getters constexpr, meaning you’ll be able
to call them at compile-time if called on a constexpr object (else at
runtime for non-const). Make sure to nevertheless declare such a
function const, because starting with C++11 const is no longer implicit
through constexpr (which is the case for C++11, but doesn’t hurt to
overspecify):

template<typename T = std::size_t>
class Point
 {
public:
constexpr Point(const T& x, const T& y) noexcept
 : _x(x)
 , _y(y)
 { }
constexpr const T& x() const noexcept
 {
return _x;
 }
constexpr const T& y() const noexcept
 {
return _y;
 }
private:
 T _x;
 T _y;

}; 

Now you can have a constexpr function use such a point at compile-time,
for example to compute the mid-point:

template<typename T = std::size_t>

constexpr Point<T> midpoint(const Point<T>& first, const Point<T>&
second)  noexcept

{
// Overflow safe, better than (first.x() + second.x())/2
return {
 first.x() + (second.x() - first.x())/2,
 first.y() + (second.y() - first.y())/2
 };
 }
int main(int argc, const char* argv[])
 {
constexpr Point<> point_1(4, 5);
constexpr Point<> point_2(11, 3);
constexpr auto middle = midpoint(point_1, point_2);

}

Lastly, in C++14 it is no longer the case that constexpr functions are
implicitly const, thus even the setters can be constexpr:

template<typename T = std::size_t>
class Point
 {
public:
constexpr Point(const T& x, const T& y) noexcept
 : _x(x)
 , _y(y)
 { }
constexpr const T& x() const noexcept
 {
return _x;
 }
constexpr const T& y() const noexcept
 {
return _y;
 }

constexpr void x(const T& new_x) noexcept
 {
 _x = new_x;
 }
constexpr void y(const T& new_y) noexcept
 {
 _y = new_y;

}

private:
 T _x;
 T _y;

};

Meaning you can really do totally awesome compile-time things:

template<typename T = std::size_t>
constexpr Point<T> scale(const Point<T>& point, double factor) noexcept
 {
Point<T> result(point.x(), point.y());
 result.x(result.x() * factor);
 result.y(result.y() * factor);
return result;
 }
int main(int argc, const char* argv[])
 {
constexpr Point<double> point(4, 5);
constexpr auto scaled = scale(point, 1.23);
print::ln(scaled.x(), scaled.y());

}
