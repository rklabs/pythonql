#PythonQL EBNF Grammar
This document describes the grammar that we want to implement in PythonQL

##Path expressions.
We start with path expressions, since they are a separate beast and will be
used not only in query context, but elsewhere in the PythonQL language.
They appear under general expression, so we will give the grammar starting
at `expr`. Btw. in the real grammar this production is called `test`, but
its confusing. Instead of `test` in EBNF we use `expr`, and instead of 
`old_test` (the production before we introduced path expressions) we use
`old_expr`.

```jflex
  expr := old_expr ( path_step )*;
  path_step := './' | './/' | '{' expr '}';
```
`'./'` is a child path step. `.//` is a descendent path step. `'{' expr '}'` is a filter.
I don't have descendent or self here, do we need one?

##Query Expression
This is our main grammar piece. Again, all Python expressions are written as
`expr` production, instead of `test`. **Caution:** Python community won't like
a whole lot of new keywords added to the language (this means old code that
uses these keywords might need to be fixed up). So maybe we should converge
to the fewest possible keywords later on.

```jflex
query_expression := select 
                  (for|let) 
                  (for|let|where|window|count|groupby|orderby)* ;

select := ('select'|'return') (expr ['as' NAME] | '*') ( ',' expr ['as' NAME] | '*' ) * ;

for := ('for'|'from') NAME 'in' expr (',' NAME 'in' expr ) ;

let := ('let'|'with') NAME '=' expr (',' NAME '=' expr );

where := expr ;

window := TBD

count := 'count' NAME;

groupby := 'group' 'by' expr (',' expr) * ;

orderby := 'order' 'by' expr ['asc'|'desc'] (',' expr ['asc'|'desc'] ) *;

```