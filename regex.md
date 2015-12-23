# regex

std::string string("Hello world");
 std::regex pattern("l{1,2}$");
 std::smatch result;
try
 {
if (std::regex_search(string, result, pattern) && ! result.empty())
 {
 std::cout << result.str() << std::endl;
 }
else std::cout << "Not found!" << std::endl;
 }
catch(const std::regex_error& e)
 {
 std::cout << e.what() << "\n";

}

http://www.regular-expressions.info/stdregex.html
