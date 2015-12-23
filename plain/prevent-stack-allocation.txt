# prevent-stack-allocation

To make an allocation on the stack illegal, but allow heap allocation,
make the constructor private and have a static function returning a
pointer:

class HeapClass
 {
public:
static std::unique_ptr<HeapClass> create()
 {
return std::make_unique<HeapClass>();
 }
private:
 HeapClass()
 { }
 };
int main(int argc, const char* argv[])
 {
std::unique_ptr<HeapClass> heap = HeapClass::create();

}
