# weak_ptr

An std::weak_ptr lets you hold references to a shared resource and check
if it’s still accessible via std::weak_ptr::expired(), without taking
active or “strong” ownership of that object, unless you specify so. If
you choose to access the data, you must promote it to a shared_ptr via
std::weak_ptr::lock().

// Create a shared_ptr
auto ptr = std::make_shared<int>(5);
// Have a weak_ptr point to the shared_ptr
std::weak_ptr<int> weak = ptr;
// The shared pointer 'weak' points to is still valid, holds the data
std::cout << std::boolalpha << weak.expired() << std::endl; // false
// _Promote_ the weak_ptr to a shared_ptr
auto other = weak.lock();
// Remove ptr's reference to the data
 ptr.reset();
// Outputs '5', other points to ptr's previous data
std::cout << *other << std::endl;
// 'weak' still hasn't expired, because other points to the data
std::cout << std::boolalpha << weak.expired() << std::endl;
// Remove other's reference to the resource too
 other.reset();

// Now 'weak' has really expired

std::cout << std::boolalpha << weak.expired() << std::endl; // true
