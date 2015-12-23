# template-template-parameters

If you want to have template parameters that are itself template
classes, but want to be able to specify the template parameters of the
template class yourself, you can mark the template class a "template
template parameter” using “template<typename> class The_Class”.

Case 1) You just want a template class but don’t want to be able to
specify the template parameters of the class yourself:

template<typename Container>

void foo(const Container& container)

{

     std::cout << container.size() << std::endl;

}

Case 2) You want to specify the template parameters of the template
parameter yourself. For this, you must have template parameters for the
template parameters of the template template parameter and also declare
the template template parameter as a template parameter. 

template<typename T, class Alloc, template<typename,class> class
Container>

void foo(const Container<T, Alloc>& container)

{

     std::cout << container.size() << std::endl;

}

Case 3) VariadicsL

template<typename… Args, template<typename…> class Container>

void foo(const Container<Args…>& container)

{

     std::cout << container.size() << std::endl;

}
