# throw-without-argument

In the catch scope of a try-catch block, you can write ‘throw’ without
an exception argument and C++ will re-throw the exception. Moreover, the
exception will not be copied (the exact same object is re-thrown) and
its dynamic type is conserved (no slicing).

try
 {
throw std::runtime_error("asdf");
 }
catch(...)
 {
throw; // throw std::runtime_error("asdf");

}
