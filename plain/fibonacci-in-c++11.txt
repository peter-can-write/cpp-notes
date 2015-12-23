# fibonacci-in-c++11

Simple version:

std::function<int(int)> f = [&] (int x) { return (x < 2) ? x : f(x-1) +
f(x-2); };

Optimized Version:

std::vector<int> cache = {0, 1};
    
     std::function<int(int)> f = [&] (int x)
     {
         if (cache.size() > x) return cache[x];
        
         cache.push_back(f(x-1) + f(x-2));
        
         return cache.back();

    };
