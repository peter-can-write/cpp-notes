# erasing-iterator-in-loop

std::vector<int> vec(100);

for (std::vector<int>::iterator itr = vec.begin();

     itr != vec.end();

     /* Incremented in loop */)

{
     if (*itr == 5)
     {
         // Automatically returns iterator after itr
         itr = vec.erase(itr);
     }
    
     else ++itr;

}
