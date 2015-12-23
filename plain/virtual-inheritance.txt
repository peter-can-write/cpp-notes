# virtual-inheritance

When using multiple inheritance and inheriting a class (tier 3) from two
classes (tier 2) with the same base class (tier 1), you would normally
get a lot of ambiguous calls, because the lowest class (tier 3) in the
hierarchy would see the top class’ (tier 1) functions twice (as both its
parents (tier 2) inherit from that base class (tier 1)). A solution is
to use VIRTUAL INHERITANCE.

Virtual inheritance tells the compiler that only one tier 1 base class
should be used in the hierarchy. The tier 3 class must initialize both
tier 2 classes (as always) and the tier 1 base class.

Class Tier1

{

public:

     void foo();

};

class Tier2A : VIRTUAL public Tier1

{};

class Tier2B : VIRTUAL public Tier1

{};

class Tier3 : public Tier2A, public Tier2B

{

public:

     Tier3()

     : Tier1(), Tier2A, Tier2B // INITIALIZE ALL BASE CLASSES IN THE
HIERACHY

     { }

};

Tier3 object;

object.foo(); // OK!

Note that this is expensive and should only be done if no other options
are available.


