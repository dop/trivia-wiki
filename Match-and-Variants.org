
* Macro /Match/

+ Syntax :: *match* /argument/ &body /{clause}*/ ---> /result/
+ clause :: -- ( /pattern/ &body /{bodyform}*/ )
+ pattern :: a pattern language.
+ bodyform :: an implicit progn.

Matches /argument/ against the patterns provided in /clauses/. Evaluate the /bodyform/ of the first clause whose pattern matches against /argument/. /bodyform/ is treated as an implicit progn.
