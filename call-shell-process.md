# call-shell-process

#include <cstdio>
int main(int argc, char * argv [])
 {
     FILE* process;

   

    if (! (process = popen("rm adsf", "r")))

    {
         throw std::runtime_error("Error opening shell process!");
     }
    
     char buffer [1024];
    
     while (fgets(buffer, sizeof(buffer), process))
     {
         std::cout << buffer << std::endl;
     }

}
