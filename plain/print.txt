# print

void print(const std::string& end)
 {
std::cout << end;
 }
template<typename Head, typename... Tail>
void print(Head&& head, Tail&&... tail, const std::string& end)
 {
std::cout << std::forward<Head>(head);
 print(std::forward<Tail>(tail)..., end);

}

void println()

{
std::cout << std::endl;
 }
template<typename Head, typename... Tail>
void println(Head&& head, Tail&&... tail)
 {
std::cout << std::forward<Head>(head);
 println(std::forward<Tail>(tail)...);

}

template<typename Itr>
void printall(Itr begin,
   Itr end,
   const std::string& final = "\n",
   const std::string& delimiter = ", ")
 {
while(begin != end)
 {
std::cout << *begin;
if (++begin != end) std::cout << delimiter;
 }
std::cout << final;

}
