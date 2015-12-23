# integer-or-float-to-string

std::string to_string(int/float value);

or stringstreams

float value = 3.14;
    
     auto str = std::to_string(value);
    
     std::cout << str << std::endl;
    
     std::ostringstream os;
    
     os << value;
    
     str = os.str();
    

    std::cout << str << std::endl;


