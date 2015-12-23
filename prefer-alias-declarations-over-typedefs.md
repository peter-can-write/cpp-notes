# prefer-alias-declarations-over-typedefs

There are three main reasons why to prefer alias-declarations over
typedefs:

1. They look clearer and better.

2. ^ especially for function pointers:

typedef void (*pointer) (std::string, bool);

vs.

using pointer = void (*) (std::string, bool);

(Note how the name is no longer nested as horribly).

3. Alias declarations can be templated!

template<typename T>

using Map = std::unordered_map<std::string, T>;

Previously, you would have had to nest that in a struct:

template<typename T>

struct Map

{

     typedef std::unordered_map<std::string, t> type;

}

Meaning you would not only have had to access it using Map<T>::TYPE, but
also you would have had to use _typename_ everywhere:

typename Map<T>::type map;

Because the compiler could not know if there were still a specialization
of Map where _typ_e were not actually a type, but for example a static
variable. This is not the case for alias declarations, as alias
declarations _must_ alias a type, meaning there is no need for either
the ::type nor for the typename:

Map<T> map; // using alias-declarations

Much better!

One last thing to note is that for all the type_traits operations in the
<type_traits> header which use a typedef and a struct, there is also a
corresponding version using alias declarations. These alias-declaration
versions are accessible by appending a ‘_t’ at the end of e.g.
std::remove_reference (making it std::remove_reference_t):

// C++98

typename std::remove_const<T>::type object;

// C++14

std::remove_const_t<T> object;


