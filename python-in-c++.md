# python-in-c++

zip:

template<typename T>
class Zip
 {
    
 public:
    

     using container_t = std::vector<T>;

   
     template<typename... Args>
     Zip(const T& head, const Args&... args)
     : _items(head.size()),
       _min(head.size())
     {
         _zip(head, args...);
     }
    
     inline operator container_t() const
     {
         return _items;
     }
    
     inline container_t operator()() const
     {
         return _items;
     }
    
 private:
    
     template<typename... Args>
     void _zip(const T& head, const Args&... tail)
     {
         // If the current item's size is less than
         // the one stored in _min, reset the _min
         // variable to the item's size
         if (head.size() < _min) _min = head.size();
        
         for (std::size_t i = 0; i < _min; ++i)
         {
             // Use begin iterator and advance it instead
             // of using the subscript operator adds support
             // for lists. std::advance has constant complexity
             // for STL containers whose iterators are
             // RandomAccessIterators (e.g. vector or deque)
             typename T::_constiterator j = head.begin();
            
             std::advance(j, i);
            
             // Append to current set of items
             _items[i]._pushback(*j);
         }
        
         // Recursive call to _zip(T, Args...)
         // while the expansion of tail... is not empty
         // else calls the overload with no parameters
         _zip(tail...);
     }
     inline void _zip()
     {
         // If _min has shrunk since the
         // constructor call we resize
         // one last time
         _items.resize(_min);
     }
     /*! Holds the items for iterating. */
     container_t _items;
    
     /*! The minimum number of values held by all items */
     std::size_t _min;
    
 };
template<typename T, typename... Args>
typename Zip<T>::container_t zip(const T& head, const Args&... tail)
 {
     return Zip<T>(head, tail...);

}

range:

class range
 {
    
 public:
    
     using iterator = std::vector<long>::iterator;
    
     using const_iterator = std::vector<long>::const_iterator;
    
     range(long end)
     : range(0, end)
     { }
    
     range(long begin, long end, long step = 1)
     {
if (end - begin > 0 && step > 0)
 {
while (begin < end)
 {
 _range.push_back(begin);
 begin += step;
 }
 }
else if (end - begin < 0 && step < 0)
 {
while(begin > end)
 {
 _range.push_back(begin);
 begin += step;
 }
 }
     }
    
     iterator begin()
     {
         return _range.begin();
     }
    
     const_iterator begin() const
     {
         return _range.begin();
     }
    
     iterator end()
     {
         return _range.end();
     }
    
     const_iterator end() const
     {
         return _range.end();
     }
    
 private:
    
     std::vector<long> _range;

};

print:

void print()
 {
std::cout << std::endl;
 }
template<typename Head, typename... Tail>
void print(Head&& head, Tail&&... tail)
 {
std::cout << std::forward<Head>(head);
 print(std::forward<Tail>(tail)...);

}
