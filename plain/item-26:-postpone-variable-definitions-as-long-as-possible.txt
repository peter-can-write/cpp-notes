# item-26:-postpone-variable-definitions-as-long-as-possible

Variable definition, construction and destruction use lots of resources.
Therefore, make sure you define your variables as late as possible,
right where you need it and make sure no exceptions can occur after
definition and before use (would make the defined variable unused).

