# switch-cases-without-break

Cases fall through without a break

    int x = 5;
    
     switch(x)
     {
         case 1:
         case 2:
         case 3:
             std::cout << "x is between 1 and 3" << std::endl;
            
         default:
             std::cout << "x is not between 1 and 3" << std::endl;

    }


