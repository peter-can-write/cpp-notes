# masking-integers-to-chars

void shift(unsigned int x)
 {
     unsigned char arr [4];
    
     for (std::size_t i = 0; i < 4; ++i)
     {
         arr[i] = (x & 0xFF);
        
         x >>= 8;
        
         std::cout << arr[i] << std::endl;
     }

}

or (less efficient, but doesn’t change integer)

void shift(const unsigned int x)

{
     unsigned char arr [4];
    
     for (std::size_t i = 0; i < 4; ++i)
     {
         arr[i] = (x & (0xFF << i*8) >> i*8);
        
         std::cout << arr[i] << std::endl;
     }

}
