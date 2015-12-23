# direct-vs-copy-initialization

Direct initialization is with parantheses or braces (given there is no
equivalent constructor taking an initializer_list):

Object object(argument);

Object object{argument};

Copy initialization works with assignment = (but still calls
constructor):

Object object = argument;

You are allowed to call explicit constructors with
direct-initialization, but not copy-initialization.
