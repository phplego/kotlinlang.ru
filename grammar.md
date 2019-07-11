---
type: doc
layout: reference
title: "Грамматика"
url: https://kotlinlang.ru/docs/reference/grammar.html
---



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

## РБНФ выражения
<!--## EBNF expressions-->
Оператор | означает альтернативу.
Оператор * означает повторение (ноль или больше).
Оператор + означает повторение (один или больше).
Оператор ? означает вариант (ноль или один).
альфа{бета} означает непустой бета-разделенный список альф. 
Оператор ++ значит что ни пробел ни комментарий не разрешен между операндами.
<!--Operator | denotes alternative.
Operator * denotes iteration (zero or more).
Operator + denotes iteration (one or more).
Operator ? denotes option (zero or one).
alpha{beta} denotes a nonempty beta-separated list of alpha's. 
Operator ++ means that no space or comment is allowed between operands.-->

