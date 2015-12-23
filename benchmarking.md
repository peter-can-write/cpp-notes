# benchmarking

template<typename Function, typename... Args>
auto benchmark(const std::size_t runs, Function function, Args&&...
args)
 {
using duration_t = std::chrono::duration<double, std::milli>;
using clock = std::chrono::high_resolution_clock;
duration_t duration(0);
for (std::size_t i = 0; i < runs; ++i)
 {
auto start = clock::now();
 function(std::forward<Args>(args)...);
 duration += (clock::now() - start);
 }
return (duration/runs).count();
 }
template<typename Return, typename... All, typename... Args>
auto benchmark(Return (&function) (All...), Args&&... args)
 {
return benchmark(1e6, function, std::forward<Args>(args)...);

}

------------------------------------------------------------------------

usingclock =std::chrono::high_resolution_clock;

   
     std::chrono::duration<double, std::milli> time;
    
     const std::size_t n = 1000000;
    
     for (std::size_t i = 0; i < n; ++i)
     {
         auto start = clock::now();
        
         pascal(7);
        
         time += clock::now() - start;
     }
    

    std::cout << (time / n).count() << std::endl;

------------------------------------------------------------------------

// Can still duration_cast later by returning a duration here

// template predicate because std::function requires a signature

template<typename F>

std::chrono::duration<double, std::milli> benchmark(F function,
std::size_t n)
 {
     using clock = std::chrono::high_resolution_clock;
    
     std::chrono::duration<double, std::milli> duration;
    
     for (std::size_t i = 0; i < n; ++i)
     {
         auto start = clock::now();
        
         function();
        
         duration += (clock::now() - start);
     }
    
     return duration / n;
 }
void foo()
 {
     std::vector<int> vec;
    
     for (int i = 0; i < 100; ++i)
     {
         for (int j = 0; j < 100; ++j)
         {
             vec.push_back(i + j);
         }
     }
 }
int main(int argc, char * argv [])

{

    std::cout << benchmark(foo, 1000).count() << std::endl;

}
