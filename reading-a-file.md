# reading-a-file

Another way to read a file:

std::ifstream file("../../test/asdf.txt");
std::string input;
std::copy(std::istreambuf_iterator<char>{file},
   std::istreambuf_iterator<char>{},
   std::back_inserter(input));

std::cout << input << std::endl;
