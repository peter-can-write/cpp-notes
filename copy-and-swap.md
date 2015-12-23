# copy-and-swap

Use the COPY-AND-SWAP IDIOM for copy-assignment operators.

Improves exception safety because once swapping happens, all data is
already copied and allocated via the copy constructor in the parameter
list of the copy-assignment operator. Also, when the copied object goes
out of scope it will contain the resources of the destination object and
those are then deleted automatically.

http://stackoverflow.com/questions/3279543/what-is-the-copy-and-swap-idiom

It implies defining a public swap member function:

void Foo::swap(Foo& other)

{

     // For argument-dependent-lookup (Foo’s data members may have

     // their own swap functions, else std::swap is used)

     using std::swap;

     swap(a.memberA, b.memberA);

     swap(a.memberB, b.memberB);

     // swap other members 

}

and a non-member swap function that calls the public member one:

friend void swap(Foo& a, Foo& b)

{

     a.swap(b);

}

And then calling that swap method in the copy-assignment operator:

Foo& operator=(Foo foo) // note the copying

{

     swap(*this, foo);

return *this

}

Moreover, if Foo is not a template class, you should TOTALLY TEMPLATE
SPECIALIZE STD::SWAP:

namespace std

{

     template<>

     void swap<Foo>(Foo& a, Foo& b)

     {

          a.swap(b);

     }

};


