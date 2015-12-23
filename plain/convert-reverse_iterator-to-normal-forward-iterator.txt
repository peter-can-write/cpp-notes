# convert-reverse_iterator-to-normal-forward-iterator

std::string::reverse_iterator r_iter = mystring.rend();

std::string::iterator iter = ( r_iter + 1).base()

+ 1 because reverse iterators point to one element before (to the left),
since rbegin() must be end() which is one after the last character.


