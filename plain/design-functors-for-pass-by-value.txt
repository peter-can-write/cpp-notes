# design-functors-for-pass-by-value

Functors are often copied inside standard algorithms, so make sure that
they are designed for pass-by-value. This means that they should be
small and monomorphic (not polymorphic; no virtual methods; else
sliced). If you cannot make them small and monomorphic, use the
Bridge/PImpl idiom:

struct Mammal
 {
 Mammal(const std::string& sp)
 : species(sp)
 { }
virtual ~Mammal() = default;
virtual bool is_happy() const = 0;
const std::string species;
 };
class Human : public Mammal
 {
public:
 Human(bool happy)
 : Mammal{"homo sapiens"}
 , _happy(happy)
 { }
virtual bool is_happy() const override
 {
return _happy;
 }
private:
bool _happy;
 };
// unary_function's argument type can be the type without const and &
// if it's const &, but must be Human* if the functor takes a pointer
class Mammal_Is_Happy : public std::unary_function<Mammal, bool>
 {
public:
virtual ~Mammal_Is_Happy() = default;
virtual inline bool operator()(const Mammal& mammal) const
 {
return false;
 }
 };
class Human_Is_Happy : public Mammal_Is_Happy
 {
public:
 Human_Is_Happy()
 : huge_vector(1E6)
 { }
virtual inline bool operator()(const Mammal& human) const override
 {
return human.is_happy();
 }
private:
// Makes this functor huuuuuuuuuuuge in size
std::vector<std::size_t> huge_vector;
 };
class Mammal_Is_Happy_Wrapper : public std::unary_function<Mammal, bool>
 {
public:
 Mammal_Is_Happy_Wrapper(const Mammal_Is_Happy* impl)
 : _impl(impl)
 { }
inline bool operator()(const Mammal& mammal) const
 {
// Do not derereference! That would only slice the pointer!
return _impl->operator()(mammal);
 }
private:
const Mammal_Is_Happy* _impl;
 };
int main(int argc, char * argv[])
 {
std::vector<Human> v = {{true}, {true}, {false}, {true}};
auto predicate = std::unique_ptr<Mammal_Is_Happy>(new Human_Is_Happy);
auto unhappy = std::find_if(v.begin(),
 v.end(),
std::not1(Mammal_Is_Happy_Wrapper(predicate.get())));
if (unhappy != v.end()) println(std::distance(v.begin(), unhappy));
else println("Everybody's happy!");

}
