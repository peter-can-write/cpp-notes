# abort()-and-exit()

There are two standard ways to terminate a program: std::abort() and
std::exit()

std::abort() stops the program at this point and does not call any
destructors of any object, it literally halts the program at that point
in time, with not a single line of code executed further. Moreover,
std::abort() sends a SIGABRT signal.

std::exit(int) also does not call destructors on automatic objects /
temporary objects, that are destructed when they go out of scope, but
does destruct static and globally scoped data. Moreover, you can
register a specific function / handler that is called when std::exit is
called. This function is set via std::atexit:

// Allocate some memory
int* p = new int(5);
// Will be called upon std::exit() call
void foo()
 {
     std::cout << "foo called" << std::endl;
    
     delete p;
 }
int main(int argc, char * argv [])
 {
     // Register the exit handler
     std::atexit(foo);
    
     // another code for std::exit(int) is EXIT_FAILURE
     std::exit(EXIT_SUCCESS);

}
