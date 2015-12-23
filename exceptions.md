# exceptions

#include <stdexcept>

-   std::logic_error
-   std::invalid_argument
-   std::runtime_error
-   std::range_error
-   std::length_error
-   std::domain_error
-   std::overflow_error
-   std::underflow_error
-   std::out_of_range

void foo()
 {
     throw std::runtime_error("An error occured!");
 }
int main(int argc, char * argv [])
 {
     try
     {
         foo();
     }
    
     catch (const std::exception& e)
     {
         std::cout << e.what() << std::endl;
     }
    

}
