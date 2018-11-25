# Грамматика
# Предисловие
<!-- This section informally explains the grammar notation used below. -->

Этот раздел неофициально объясняет грамматические обозначения, используемые ниже.

## Символы и наименования
<!-- Terminal symbol names start with an uppercase letter, e.g. SimpleName.
Nonterminal symbol names start with a lowercase letter, e.g. kotlinFile.
Each production starts with a colon (:).
Symbol definitions may have many productions and are terminated by a semicolon (;).
Symbol definitions may be prepended with attributes, e.g. start attribute denotes a start symbol. -->

Терминальные символьные имена начинаются с прописной буквы, например SimpleName . 
Нетерминальные символьные имена начинаются с строчной буквы, например kotlinFile . 
Каждое производство начинается с двоеточием ( : ). 
Определения символов могут иметь множество производств и заканчиваются точкой с запятой ( ; ). 
Определения символов могут быть добавлены с атрибутами , например, startатрибут обозначает символ начала.

## EBNF expressions
Operator | denotes alternative.
Operator * denotes iteration (zero or more).
Operator + denotes iteration (one or more).
Operator ? denotes option (zero or one).
alpha{beta} denotes a nonempty beta-separated list of alpha's. 
Operator ++ means that no space or comment is allowed between operands.

# Semicolons
Kotlin provides "semicolon inference": syntactically, subsentences (e.g., statements, declarations etc) are separated by the pseudo-token SEMI, which stands for "semicolon or newline". In most cases, there's no need for semicolons in Kotlin code.

# Syntax
Relevant pages: Packages

start
kotlinFile
  : preamble topLevelObject*
  ;
start
script
  : preamble expression*
  ;
preamble (used by script, kotlinFile)
  : fileAnnotations? packageHeader? import*
  ;
fileAnnotations (used by preamble)
  : fileAnnotation*
  ;
fileAnnotation (used by fileAnnotations)
  : "@" "file" ":" ("[" unescapedAnnotation+ "]" | unescapedAnnotation)
  ;
packageHeader (used by preamble)
  : modifiers "package" SimpleName{"."} SEMI?
  ;
See Packages

import (used by preamble)
  : "import" SimpleName{"."} ("." "*" | "as" SimpleName)? SEMI?
  ;
See Imports

topLevelObject (used by kotlinFile)
  : class
  : object
  : function
  : property
  : typeAlias
  ;
typeAlias (used by memberDeclaration, declaration, topLevelObject)
  : modifiers "typealias" SimpleName typeParameters? "=" type
  ;
## Classes
See Classes and Inheritance

class (used by memberDeclaration, declaration, topLevelObject)
  : modifiers ("class" | "interface") SimpleName
      typeParameters?
      primaryConstructor?
      (":" annotations delegationSpecifier{","})?
      typeConstraints
      (classBody? | enumClassBody)
  ;
primaryConstructor (used by class, object)
  : (modifiers "constructor")? ("(" functionParameter{","} ")")
  ;
classBody (used by objectLiteral, enumEntry, class, companionObject, object)
  : ("{" members "}")?
  ;
members (used by enumClassBody, classBody)
  : memberDeclaration*
  ;
delegationSpecifier (used by objectLiteral, class, companionObject, object)
  : constructorInvocation 
  : userType
  : explicitDelegation
  ;
explicitDelegation (used by delegationSpecifier)
  : userType "by" expression 
  ;
typeParameters (used by typeAlias, class, property, function)
  : "<" typeParameter{","} ">"
  ;
typeParameter (used by typeParameters)
  : modifiers SimpleName (":" userType)?
  ;
See Generic classes

typeConstraints (used by class, property, function)
  : ("where" typeConstraint{","})?
  ;
typeConstraint (used by typeConstraints)
  : annotations SimpleName ":" type
  ;
See Generic constraints

Class members
memberDeclaration (used by members)
  : companionObject
  : object
  : function
  : property
  : class
  : typeAlias
  : anonymousInitializer
  : secondaryConstructor
  ;
anonymousInitializer (used by memberDeclaration)
  : "init" block
  ;
companionObject (used by memberDeclaration)
  : modifiers "companion" "object" SimpleName? (":" delegationSpecifier{","})? classBody?
  ;
valueParameters (used by secondaryConstructor, function)
  : "(" functionParameter{","}? ")"
  ;
functionParameter (used by valueParameters, primaryConstructor)
  : modifiers ("val" | "var")? parameter ("=" expression)?
  ;
block (used by catchBlock, anonymousInitializer, secondaryConstructor, functionBody, controlStructureBody, try, finallyBlock)
  : "{" statements "}"
  ;
function (used by memberDeclaration, declaration, topLevelObject)
  : modifiers "fun"
      typeParameters?
      (type ".")?
      SimpleName
      typeParameters? valueParameters (":" type)?
      typeConstraints
      functionBody?
  ;
functionBody (used by getter, setter, function)
  : block
  : "=" expression
  ;
variableDeclarationEntry (used by for, lambdaParameter, property, multipleVariableDeclarations)
  : SimpleName (":" type)?
  ;
multipleVariableDeclarations (used by for, lambdaParameter, property)
  : "(" variableDeclarationEntry{","} ")"
  ;
property (used by memberDeclaration, declaration, topLevelObject)
  : modifiers ("val" | "var")
      typeParameters?
      (type ".")?
      (multipleVariableDeclarations | variableDeclarationEntry)
      typeConstraints
      ("by" | "=" expression SEMI?)?
      (getter? setter? | setter? getter?) SEMI?
  ;
See Properties and Fields

getter (used by property)
  : modifiers "get"
  : modifiers "get" "(" ")" (":" type)? functionBody
  ;
setter (used by property)
  : modifiers "set"
  : modifiers "set" "(" modifiers (SimpleName | parameter) ")" functionBody
  ;
parameter (used by functionType, setter, functionParameter)
  : SimpleName ":" type
  ;
object (used by memberDeclaration, declaration, topLevelObject)
  : modifiers "object" SimpleName primaryConstructor? (":" delegationSpecifier{","})? classBody?
  ;
secondaryConstructor (used by memberDeclaration)
  : modifiers "constructor" valueParameters (":" constructorDelegationCall)? block
  ;
constructorDelegationCall (used by secondaryConstructor)
  : "this" valueArguments
  : "super" valueArguments
  ;
See Object expressions and Declarations

Enum classes
See Enum classes

enumClassBody (used by class)
  : "{" enumEntries (";" members)? "}"
  ;
enumEntries (used by enumClassBody)
  : (enumEntry{","} ","? ";"?)?
  ;
enumEntry (used by enumEntries)
  : modifiers SimpleName valueArguments? classBody?
  ;
Types
See Types

type (used by namedInfix, simpleUserType, getter, atomicExpression, whenCondition, property, typeArguments, function, typeAlias, parameter, functionType, variableDeclarationEntry, lambdaParameter, typeConstraint)
  : typeModifiers typeReference
  ;
typeReference (used by typeReference, nullableType, type)
  : "(" typeReference ")"
  : functionType
  : userType
  : nullableType
  : "dynamic"
  ;
nullableType (used by typeReference)
  : typeReference "?"
  ;
userType (used by typeParameter, catchBlock, callableReference, typeReference, delegationSpecifier, constructorInvocation, explicitDelegation)
  : simpleUserType{"."}
  ;
simpleUserType (used by userType)
  : SimpleName ("<" (projection? type | "*"){","} ">")?
  ;
projection (used by simpleUserType)
  : varianceAnnotation
  ;
functionType (used by typeReference)
  : (type ".")? "(" parameter{","}? ")" "->" type
  ;
Control structures
See Control structures

controlStructureBody (used by whenEntry, for, if, doWhile, while)
  : block
  : blockLevelExpression
  ;
if (used by atomicExpression)
  : "if" "(" expression ")" controlStructureBody SEMI? ("else" controlStructureBody)?
  ;
try (used by atomicExpression)
  : "try" block catchBlock* finallyBlock?
  ;
catchBlock (used by try)
  : "catch" "(" annotations SimpleName ":" userType ")" block
  ;
finallyBlock (used by try)
  : "finally" block
  ;
loop (used by atomicExpression)
  : for
  : while
  : doWhile
  ;
for (used by loop)
  : "for" "(" annotations (multipleVariableDeclarations | variableDeclarationEntry) "in" expression ")" controlStructureBody
  ;
while (used by loop)
  : "while" "(" expression ")" controlStructureBody
  ;
doWhile (used by loop)
  : "do" controlStructureBody "while" "(" expression ")"
  ;
Expressions
Precedence
Precedence	Title	Symbols
Highest	Postfix	++, --, ., ?., ?
 	Prefix	-, +, ++, --, !, labelDefinition
 	Type RHS	:, as, as?
 	Multiplicative	*, /, %
 	Additive	+, -
 	Range	..
 	Infix function	SimpleName
 	Elvis	?:
 	Named checks	in, !in, is, !is
 	Comparison	<, >, <=, >=
 	Equality	==, \!==
 	Conjunction	&&
 	Disjunction	||
Lowest	Assignment	=, +=, -=, *=, /=, %=
Rules
expression (used by for, atomicExpression, longTemplate, whenCondition, functionBody, doWhile, property, script, explicitDelegation, jump, while, arrayAccess, blockLevelExpression, if, when, valueArguments, functionParameter)
  : disjunction (assignmentOperator disjunction)*
  ;
disjunction (used by expression)
  : conjunction ("||" conjunction)*
  ;
conjunction (used by disjunction)
  : equalityComparison ("&&" equalityComparison)*
  ;
equalityComparison (used by conjunction)
  : comparison (equalityOperation comparison)*
  ;
comparison (used by equalityComparison)
  : namedInfix (comparisonOperation namedInfix)*
  ;
namedInfix (used by comparison)
  : elvisExpression (inOperation elvisExpression)*
  : elvisExpression (isOperation type)?
  ;
elvisExpression (used by namedInfix)
  : infixFunctionCall ("?:" infixFunctionCall)*
  ;
infixFunctionCall (used by elvisExpression)
  : rangeExpression (SimpleName rangeExpression)*
  ;
rangeExpression (used by infixFunctionCall)
  : additiveExpression (".." additiveExpression)*
  ;
additiveExpression (used by rangeExpression)
  : multiplicativeExpression (additiveOperation multiplicativeExpression)*
  ;
multiplicativeExpression (used by additiveExpression)
  : typeRHS (multiplicativeOperation typeRHS)*
  ;
typeRHS (used by multiplicativeExpression)
  : prefixUnaryExpression (typeOperation prefixUnaryExpression)*
  ;
prefixUnaryExpression (used by typeRHS)
  : prefixUnaryOperation* postfixUnaryExpression
  ;
postfixUnaryExpression (used by prefixUnaryExpression, postfixUnaryOperation)
  : atomicExpression postfixUnaryOperation*
  : callableReference postfixUnaryOperation*
  ;
callableReference (used by postfixUnaryExpression)
  : (userType "?"*)? "::" SimpleName typeArguments?
  ;
atomicExpression (used by postfixUnaryExpression)
  : "(" expression ")"
  : literalConstant
  : functionLiteral
  : "this" labelReference?
  : "super" ("<" type ">")? labelReference?
  : if
  : when
  : try
  : objectLiteral
  : jump
  : loop
  : collectionLiteral
  : SimpleName
  ;
labelReference (used by atomicExpression, jump)
  : "@" ++ LabelName
  ;
labelDefinition (used by prefixUnaryOperation, annotatedLambda)
  : LabelName ++ "@"
  ;
literalConstant (used by atomicExpression)
  : "true" | "false"
  : stringTemplate
  : NoEscapeString
  : IntegerLiteral
  : CharacterLiteral
  : FloatLiteral
  : "null"
  ;
stringTemplate (used by literalConstant)
  : "\"" stringTemplateElement* "\""
  ;
stringTemplateElement (used by stringTemplate)
  : RegularStringPart
  : ShortTemplateEntryStart (SimpleName | "this")
  : EscapeSequence
  : longTemplate
  ;
longTemplate (used by stringTemplateElement)
  : "${" expression "}"
  ;
declaration (used by statement)
  : function
  : property
  : class
  : typeAlias
  : object
  ;
statement (used by statements)
  : declaration
  : blockLevelExpression
  ;
blockLevelExpression (used by statement, controlStructureBody)
  : annotations ("\n")+ expression
  ;
multiplicativeOperation (used by multiplicativeExpression)
  : "*" : "/" : "%"
  ;
additiveOperation (used by additiveExpression)
  : "+" : "-"
  ;
inOperation (used by namedInfix)
  : "in" : "!in"
  ;
typeOperation (used by typeRHS)
  : "as" : "as?" : ":"
  ;
isOperation (used by namedInfix)
  : "is" : "!is"
  ;
comparisonOperation (used by comparison)
  : "<" : ">" : ">=" : "<="
  ;
equalityOperation (used by equalityComparison)
  : "!=" : "=="
  ;
assignmentOperator (used by expression)
  : "="
  : "+=" : "-=" : "*=" : "/=" : "%="
  ;
prefixUnaryOperation (used by prefixUnaryExpression)
  : "-" : "+"
  : "++" : "--"
  : "!"
  : annotations
  : labelDefinition
  ;
postfixUnaryOperation (used by postfixUnaryExpression)
  : "++" : "--" : "!!"
  : callSuffix
  : arrayAccess
  : memberAccessOperation postfixUnaryExpression 
  ;
callSuffix (used by constructorInvocation, postfixUnaryOperation)
  : typeArguments? valueArguments annotatedLambda
  : typeArguments annotatedLambda
  ;
annotatedLambda (used by callSuffix)
  : ("@" unescapedAnnotation)* labelDefinition? functionLiteral
  ;
memberAccessOperation (used by postfixUnaryOperation)
  : "." : "?." : "?"
  ;
typeArguments (used by callSuffix, callableReference, unescapedAnnotation)
  : "<" type{","} ">"
  ;
valueArguments (used by callSuffix, enumEntry, constructorDelegationCall, unescapedAnnotation)
  : "(" ((SimpleName "=")? "*"? expression){","} ")"
  ;
jump (used by atomicExpression)
  : "throw" expression
  : "return" ++ labelReference? expression?
  : "continue" ++ labelReference?
  : "break" ++ labelReference?
  ;
functionLiteral (used by atomicExpression, annotatedLambda)
  : "{" statements "}"
  : "{" lambdaParameter{","} "->" statements "}"
  ;
lambdaParameter (used by functionLiteral)
  : variableDeclarationEntry
  : multipleVariableDeclarations (":" type)?
  ;
statements (used by block, functionLiteral)
  : SEMI* statement{SEMI+} SEMI*
  ;
constructorInvocation (used by delegationSpecifier)
  : userType callSuffix
  ;
arrayAccess (used by postfixUnaryOperation)
  : "[" expression{","} "]"
  ;
objectLiteral (used by atomicExpression)
  : "object" (":" delegationSpecifier{","})? classBody
  ;
collectionLiteral (used by atomicExpression)
  : "[" element{","}? "]"
  ;
When-expression
See When-expression

when (used by atomicExpression)
  : "when" ("(" expression ")")? "{"
        whenEntry*
    "}"
  ;
whenEntry (used by when)
  : whenCondition{","} "->" controlStructureBody SEMI
  : "else" "->" controlStructureBody SEMI
  ;
whenCondition (used by whenEntry)
  : expression
  : ("in" | "!in") expression
  : ("is" | "!is") type
  ;
Modifiers
modifiers (used by typeParameter, getter, packageHeader, class, property, object, function, typeAlias, secondaryConstructor, enumEntry, setter, companionObject, primaryConstructor, functionParameter)
  : (modifier | annotations)*
  ;
typeModifiers (used by type)
  : (suspendModifier | annotations)*
  ;
modifier (used by modifiers)
  : classModifier
  : accessModifier
  : varianceAnnotation
  : memberModifier
  : parameterModifier
  : typeParameterModifier
  : functionModifier
  : propertyModifier
  ;
classModifier (used by modifier)
  : "abstract"
  : "final"
  : "enum"
  : "open"
  : "annotation"
  : "sealed"
  : "data"
  ;
memberModifier (used by modifier)
  : "override"
  : "open"
  : "final"
  : "abstract"
  : "lateinit"
  ;
accessModifier (used by modifier)
  : "private"
  : "protected"
  : "public"
  : "internal"
  ;
varianceAnnotation (used by modifier, projection)
  : "in"
  : "out"
  ;
parameterModifier (used by modifier)
  : "noinline"
  : "crossinline"
  : "vararg"
  ;
typeParameterModifier (used by modifier)
  : "reified"
  ;
functionModifier (used by modifier)
  : "tailrec"
  : "operator"
  : "infix"
  : "inline"
  : "external"
  : suspendModifier
  ;
propertyModifier (used by modifier)
  : "const"
  ;
suspendModifier (used by typeModifiers, functionModifier)
  : "suspend"
  ;
Annotations
annotations (used by catchBlock, prefixUnaryOperation, blockLevelExpression, for, typeModifiers, class, modifiers, typeConstraint)
  : (annotation | annotationList)*
  ;
annotation (used by annotations)
  : "@" (annotationUseSiteTarget ":")? unescapedAnnotation
  ;
annotationList (used by annotations)
  : "@" (annotationUseSiteTarget ":")? "[" unescapedAnnotation+ "]"
  ;
annotationUseSiteTarget (used by annotation, annotationList)
  : "field"
  : "file"
  : "property"
  : "get"
  : "set"
  : "receiver"
  : "param"
  : "setparam"
  : "delegate"
  ;
unescapedAnnotation (used by annotation, fileAnnotation, annotatedLambda, annotationList)
  : SimpleName{"."} typeArguments? valueArguments?
  ;
Lexical structure
helper
LongSuffix (used by IntegerLiteral)
  : "L"
  ;
IntegerLiteral (used by literalConstant)
  : DecimalLiteral LongSuffix?
  : HexadecimalLiteral LongSuffix?
  : BinaryLiteral LongSuffix?
  ;
helper
Digit (used by DecimalLiteral, HexDigit)
  : ["0".."9"]
  ;
DecimalLiteral (used by IntegerLiteral)
  : Digit
  : Digit (Digit | "_")* Digit
  ;
FloatLiteral (used by literalConstant)
  : <Java double literal>
  ;
helper
HexDigit (used by HexadecimalLiteral, UnicodeEscapeSequence)
  : Digit | ["A".."F", "a".."f"]
  ;
HexadecimalLiteral (used by IntegerLiteral)
  : "0" ("x" | "X") HexDigit
  : "0" ("x" | "X") HexDigit (HexDigit | "_")* HexDigit
  ;
helper
BinaryDigit (used by BinaryLiteral)
  : ("0" | "1")
  ;
BinaryLiteral (used by IntegerLiteral)
  : "0" ("b" | "B") BinaryDigit
  : "0" ("b" | "B") BinaryDigit (BinaryDigit | "_")* BinaryDigit
CharacterLiteral (used by literalConstant)
  : <character as in Java>
  ;
See Basic types

NoEscapeString (used by literalConstant)
  : <"""-quoted string>
  ;
RegularStringPart (used by stringTemplateElement)
  : <any character other than backslash, quote, $ or newline>
  ;
ShortTemplateEntryStart (used by stringTemplateElement)
  : "$"
  ;
EscapeSequence (used by stringTemplateElement)
  : UnicodeEscapeSequence | RegularEscapeSequence
  ;
UnicodeEscapeSequence (used by EscapeSequence)
  : "\u" HexDigit{4}
  ;
RegularEscapeSequence (used by EscapeSequence)
  : "\" <any character other than newline>
  ;
See String templates

SEMI (used by whenEntry, if, statements, packageHeader, property, import)
  : <semicolon or newline>
  ;
SimpleName (used by typeParameter, catchBlock, simpleUserType, atomicExpression, LabelName, packageHeader, class, object, infixFunctionCall, function, typeAlias, parameter, callableReference, variableDeclarationEntry, stringTemplateElement, enumEntry, setter, import, companionObject, valueArguments, unescapedAnnotation, typeConstraint)
  : <java identifier>
  : "`" <java identifier> "`"
  ;
See Java interoperability

LabelName (used by labelReference, labelDefinition)
  : SimpleName
  ;
See Returns and jumps
