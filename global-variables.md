# global-variables

To share variables between different modules (i.e. files) of your
program, you can have one global.hpp file with a namespace and inside
declare the variables ‘extern’. Extern indicates external linkage, i.e.
it tells the compiler that the variable exists and is defined
_somewhere_ in the program. All the compiler needs, after all, is the
type and name of the variable. The variable itself must be defined
somewhere, outside the files cope (e.g. in a global.cpp file) and the
linker will then resolve all references to the variable during the
linkage process.

—— global.hpp —— 

namespace Global
 {
/*! One minute in samples. */
extern unsigned short Minute;
/*! Half an hour in samples. */
extern unsigned short Halfhour;
/*! One hour in samples. */
extern unsigned short Hour;
/*! One day in samples. */
extern unsigned int Day;
/*! One week in samples. */
extern unsigned int Week;
/*! One month in samples. */
extern unsigned int Month;
/*! One year in samples. */
extern unsigned int Year;

extern void initialize(std::size_t period = 1);

}

—— global.cpp ——

#include "global.hpp"
namespace Global
 {
     unsigned short Minute;
     unsigned short Halfhour;
unsigned short Hour;
unsigned int Day;
unsigned int Week;
unsigned int Month;
unsigned int Year;
void initialize(std::size_t period)
 {
 Minute = 60 / period;
 Halfhour = (Minute * 30) / period;
 Hour = (Minute * 60) / period;
 Day = (Hour * 24) / period;
 Week = (Day * 7) / period;
 Month = (Day * 30) / period;
 Year = (Day * 365) / period;
 }

}
