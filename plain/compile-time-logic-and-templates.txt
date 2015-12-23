# compile-time-logic-and-templates

It’s not possible to perform type-based logic (w/ templates), because
the compiler does not consider branches and must make sure that a
template class or function performs no invalid operations for the type
it is instantiated with (use overloading, template specializations, or
traits).

template<typename Itr, typename Distance>
void advance(Itr itr, const Distance& n)
 {
     if (itr is a random_access_iterator)
     {
         // Compile-time error, itr += not valid
         // for forward iterator, even though at
         // run-time this check would not be performed
         itr += n;
     }
    
     else
     {
         while(n--) itr++;
     }
 }
int main(int argc, char * argv [])
 {
     std::list<int> l{1,2,3};
    
     std::list<int>::iterator itr = l.begin();
    
     advance(itr, 2); // Error

    
