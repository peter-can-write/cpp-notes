# clearing-an-std::stringstream

std::string s("10 25");
 std::string t("34");
int a;
int b;
 std::stringstream ss;
 ss << s;
 ss >> a; // a is 10 and extracted from ss
// stringstream not cleared
 ss << t; // t is "added"

ss >> b; // b is 2534 (25 still inside)

------------------------------------------------------------------------

std::string s("10 25");
 std::string t("34");
int a;
int b;
 std::stringstream ss;
 ss << s;
 ss >> a; // a is 10 and extracted from ss
 ss.clear(); // clears error states (EOF if s had ended here)

ss.str(std::string()); // Reset stream to empty string (could also
insert t directly)

 ss << t; // t is "added"

ss >> b; // b is 34


