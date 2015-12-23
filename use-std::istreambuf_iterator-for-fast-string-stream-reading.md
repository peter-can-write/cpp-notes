# use-std::istreambuf_iterator-for-fast-string-stream-reading

std::istream_iterator is very flexible, powerful but also expensive. It
uses operator>> and has to do lots of formatting and error/flag checking
and SKIPS WHITESPACE. std::istreambuf_iterator reads all characters
(also whitespace), one by one and doesn’t skip anything. It’s not as
good for formatting, but is a lot faster and the better choice to just
read the damn string.

std::istringstream stream(std::string("Hello World"));
// One way, requires concatenation:
  std::string input, temp;
  while (std::getline(stream, temp)) input += temp;
// Second way, skips whitespace, is expensive (uses operator>>)
// Output: Hello World
    std::string input(std::istream_iterator<char>{stream},
 std::istream_iterator<char>{});
// Third way, doesn't skip whitespace
// Output: Hello World
 stream.unsetf(std::ios::skipws);
 std::string input(std::istream_iterator<char>{stream},
   std::istream_iterator<char>{});
// Best way, reads in all characters,
// one by one, raw, no formatting, faster
// Output: Hello World
   std::string input(std::istreambuf_iterator<char>{stream},
 std::istreambuf_iterator<char>{});

std::cout << input << std::endl;
