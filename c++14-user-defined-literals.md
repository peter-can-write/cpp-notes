# c++14-user-defined-literals

In pre-14 C++, it was already possible to use postfixes for literals to
specify what primitive type they were.

#define FOO 5

int main(int argc, char * argv [])
 {

    std::cout << FOO - 10 << std::endl;

}

Output:

>>> -5

#define FOO 5U
int main(int argc, char * argv [])
 {
     std::cout << FOO - 10 << std::endl;

}

Output:

>>> 4,294,967,291

Notice how changing the ‘5’ to ‘5U’ in the definition alters FOO from
being a 32-bit signed integer to being a 32-bit unsigned integer.

Also, certain prefixes would alter the base used:

int main(int argc, char * argv [])
 {
     int dec = 10;
    
     int oct = 010;
    
     int hex = 0x10;
    
     int bin = 0b10;
    
     std::cout << dec << " " << oct << " " << hex << " " << bin <<
std::endl;

}

Note that already here, the binary cast is a C++11 feature and was not
possible before.

Now, in C++11, we the people, can define our own user-defined literals.
Also the standard has added a few literals in certain places that make
life more beautiful, while also dangerous. In the std::chrono time
library, there are literals for specifying time durations, here are two:

constexpr std::chrono::seconds operator""s(unsigned long long dur)

{
     return std::chrono::seconds(dur);
 }

constexpr std::chrono::duration<long double> operator""s(long double
dur)

{
     return std::chrono::duration<long double>(dur);
 }

constexpr std::chrono::minutes operator""m(unsigned long long dur)

    return std::chrono::minutes(dur);
 }

constexpr std::chrono::duration<long double, std::ratio<60>>
operator""m(long double dur)

{
     return std::chrono::duration<long double, std::ratio<60>>(dur);

}

There are more, for hours (“”h), and down to nanoseconds (“”ns). They
can be made visible to the current namespace either via using
std::literals (also includes string literals),
std::literals::chrono_literals or std::chrono_literals. For each
duration, there is one that takes care of integers (with unsigned long
long) and returns the respective duration alias from the std::chrono
header, e.g. std::chrono::seconds, while the other deals with floating
point values. You use them like so:

int main(int argc, char * argv [])
 {
     using namespace std::chrono_literals;
    
     auto inSeconds = 1h + 25min + 34s;
    
     auto inMinutes =
std::chrono::duration_cast<std::chrono::minutes>(inSeconds);
    
     using hour_t = std::chrono::duration<float,
std::chrono::hours::period>;
    
     auto inHours = std::chrono::duration_cast<hour_t>(inMinutes);
    
     std::cout << "Seconds: " << inSeconds.count()
               << "\nMinutes: " << inMinutes.count()
               << "\nHours: " << inHours.count() << std::endl;

}

Output:

>>> SECONDS: 5134

>>> MINUTES: 85

>>> HOURS: 1.41667

Another literal operator defined in the standard library is one for
string literals:

Before:

std::string foo = "Hello World!";

Now:

using namespace std::string_literals;

auto foo = "Hello World!"s;

Complex numbers:

using namespace std::literals::complex_literals;
    

auto z = 1.0 + 5i;

   

std::cout << z << std::endl; // >> (1, 5)

Lastly, you can now also use single quotation marks to separate large
integer values:

int big = 1’000'000; // valid

User defined literal operators are just as easy to declare and use.
However, they must be prefixed by an underscore and not start uppercase,
else they’re reserved for the standard. For the parameter lists, only
specific types and combinations thereof are allowed:

-   ( const char * ) 
-   ( unsigned long long int )
-   ( long double )   
-   ( char )   
-   ( wchar_t )  
-   ( char16_t ) 
-   ( char32_t ) 
-   ( const char * , std::size_t ) 
-   ( const wchar_t * , std::size_t )
-   ( const char16_t * , std::size_t )  
-   ( const char32_t * , std::size_t )  

And also a template parameter pack of type char:

constexpr template<char . . .> operator””...

If you want your literal operators to be constexpr for user-defined
types, the constructor of the user-defined type must be constexpr and
also all other requirements for constexpr must be satisfied ();


