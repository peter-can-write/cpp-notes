# imbue,-callback-and-put_money

void callback(std::ios_base::event event, std::ios_base& stream, int
index)
 {
// There's erase_event, imbue_event and copyfmt_event
if (event != std::ios_base::imbue_event) return;
std::cout << "Change of locale to '" << stream.getloc().name() <<
"'!\n";
 }
int main(int argc, char * argv[])
 {
std::cout.register_callback(callback, std::cout.xalloc());
// Default
std::cout.imbue(std::locale());
// Other
std::cout.imbue(std::locale("en_US"));
std::cout << std::showbase << std::put_money(33) << std::endl;

}
