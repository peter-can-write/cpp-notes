# std::istringstream-and-std::ostringstream

std::ostringstream is used to convert an integer or any (user-defined)
type with appropriate methods to a string

std::stringstream is initialized with a string and then converts that to
an integral or  user-defined type

float value = 3.14;
std::ostringstream os;
 os << value;
auto str = os.str();
std::cout << str << std::endl;
std::istringstream is(str);
 is >> value;

std::cout << value << std::endl;
