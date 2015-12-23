# split-string

std::vector<std::string> split(const std::string& str, char delim = ' ')
 {
     std::string temp;
    
     std::istringstream is(str);
    
     std::vector<std::string> split;
    
     while(std::getline(is, temp, delim))
     {
         split.push_back(temp);
     }
    
     return split;
 }
std::vector<std::string> split1(const std::string& str, char delim = '
')
 {
     std::vector<std::string> split;
    
     auto last = str.begin();
    
     auto itr = std::find(str.begin(), str.end(), delim);
    
     for ( ; itr != str.end(); itr = std::find(itr, str.end(), delim))
     {
         split.push_back({last, itr});
        
         last = ++itr;
     }
    
     split.push_back({last, itr});
    
     return split;
 }
std::vector<std::string> split2(const std::string& str, char delim = '
')
 {
     std::vector<std::string> split;
    
     std::size_t i = str.find(delim), last = 0;
    
     for ( ; i != std::string::npos; i = str.find(delim, i) )
     {
         split.push_back(str.substr(last, i - last));
        
         last = ++i;
     }
    
     split.push_back(str.substr(last, i - last));
    
     return split;
 }
int main(int argc, char * argv [])
 {
     std::string str("string to split");

    

    std::cout << benchmark(std::bind(split, str, ' '), 1000000).count()
<< std::endl;
    
     std::cout << benchmark(std::bind(split1, str, ' '),
1000000).count() << std::endl;
    
     std::cout << benchmark(std::bind(split2, str, ' '),
1000000).count() << std::endl;
    

}

Output:

0.00246193
 0.00206184

0.0016398
