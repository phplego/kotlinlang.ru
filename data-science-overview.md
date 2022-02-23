---
type: doc
layout: reference
title: "Kotlin для Data Science"
url: https://kotlinlang.ru/docs/data-science-overview.html
---
      
<!-- При переводе статьи оригинальная версия была от 06 December 2021 -->

<!-- # Kotlin for data science -->
# Kotlin для Data Science

<!-- From building data pipelines to productionizing machine learning models, Kotlin can be a great choice for 
working with data: -->
Начиная с построения конвейеров данных и заканчивая разработкой моделей машинного обучения, Kotlin может стать отличным
выбором для работы с данными:

<!-- * Kotlin is concise, readable, and easy to learn.
* Static typing and null safety help create reliable, maintainable code that is easy to troubleshoot. 
* Being a JVM language, Kotlin gives you great performance and an ability to leverage an entire ecosystem 
of tried and true Java libraries. -->

* Kotlin лаконичен, удобочитаем и прост в освоении;
* Статическая типизация и null-безопасность помогают создавать надежный код, который легко поддерживать и улучшать;
* Будучи JVM языком, Kotlin обеспечивает отличную производительность и возможность использовать целую экосистему
проверенных и надежных Java библиотек.

<a name="interactive-editors"></a>

<!-- ## Interactive editors -->
## Интерактивные редакторы

<!-- Notebooks such as [Jupyter Notebook](https://jupyter.org/) and [Apache Zeppelin](https://zeppelin.apache.org/) provide 
convenient tools for data visualization and exploratory research.
Kotlin integrates with these tools to help you explore data, share your findings with 
colleagues, or build up your data science and machine learning skills. -->
Блокноты, такие как [Jupyter Notebook](https://jupyter.org/) и [Apache Zeppelin](https://zeppelin.apache.org/),
предоставляют удобные инструменты для визуализации данных и исследовательских исследований. Kotlin интегрируется с этими
инструментами, чтобы помочь вам изучать данные, делиться своими выводами с коллегами или развивать свои навыки в области
Data Science и машинного обучения.

<a name="jupyter-kotlin-kernel"></a>

<!-- ### Jupyter Kotlin kernel -->
### Kotlin ядро для Jupyter

<!-- The Jupyter Notebook is an open-source web application that allows you to create and share documents 
(aka "notebooks") that can contain code, visualizations, and Markdown text. 
[Kotlin-jupyter](https://github.com/Kotlin/kotlin-jupyter) is an open source project that brings Kotlin 
support to Jupyter Notebook. -->
Jupyter Notebook - это веб-приложение с открытым исходным кодом, которое позволяет создавать и обмениваться документами
(также известными как "ноутбуки"), которые могут содержать код, визуализации и Markdown разметку.
[Kotlin-jupyter](https://github.com/Kotlin/kotlin-jupyter) - это проект с открытым исходным кодом, который обеспечивает
поддержку Kotlin для Jupyter Notebook.

<img src="https://kotlinlang.org/docs/images/kotlin-jupyter-kernel.png" alt="Kotlin в Jupyter-ноутбуке" title="Kotlin в Jupyter-ноутбуке">

<!-- Check out Kotlin kernel's [GitHub repo](https://github.com/Kotlin/kotlin-jupyter) for installation 
instructions, documentation, and examples. -->
Ознакомьтесь с [GitHub репозиторием](https://github.com/Kotlin/kotlin-jupyter) ядра Kotlin для получения инструкций по
установке, документации и примеров.

<a name="zeppelin-kotlin-interpreter"></a>

<!-- ### Zeppelin Kotlin interpreter -->
### Kotlin интерпретатор для Zeppelin

<!-- Apache Zeppelin is a popular web-based solution for interactive data analytics. It provides strong support 
for the Apache Spark cluster computing system, which is particularly useful for data engineering. 
Starting from [version 0.9.0](https://zeppelin.apache.org/docs/0.9.0-preview1/), Apache Zeppelin comes with 
bundled Kotlin interpreter. -->
Apache Zeppelin - популярное веб-решение для интерактивного анализа данных. Оно обеспечивает мощную поддержку кластерной
вычислительной системы Apache Spark, которая особенно полезна для дата-инжиниринга. Начиная с [версии 0.9.0](https://zeppelin.apache.org/docs/0.9.0-preview1/),
в Apache Zeppelin есть встроенный Kotlin интерпретатор.

<img src="https://kotlinlang.org/docs/images/kotlin-zeppelin-interpreter.png" alt="Kotlin в Zeppelin-ноутбуке" title="Kotlin в Zeppelin-ноутбуке">

<a name="libraries"></a>

<!-- ## Libraries -->
## Библиотеки

<!-- The ecosystem of libraries for data-related tasks created by the Kotlin community is rapidly expanding. 
Here are some libraries that you may find useful: -->
Экосистема библиотек для задач, связанных с данными, созданная сообществом Kotlin, быстро расширяется. Вот некоторые
библиотеки, которые могут быть полезными:

<a name="kotlin-libraries"></a>

<!-- ### Kotlin libraries -->
### Kotlin библиотеки

<!-- * [Multik](https://github.com/Kotlin/multik): multidimensional arrays in Kotlin. The library provides Kotlin-idiomatic, 
  type- and dimension-safe API for mathematical operations over multidimensional arrays. Multik offers swappable 
  JVM and native computational engines, and a combination of the two for optimal performance.

* [KotlinDL](https://github.com/jetbrains/kotlindl) is a high-level Deep Learning API written in Kotlin and inspired
  by Keras. It offers simple APIs for training deep learning models from scratch, importing existing Keras models
  for inference, and leveraging transfer learning for tweaking existing pre-trained models to your tasks.

* [Kotlin for Apache Spark](https://github.com/JetBrains/kotlin-spark-api) adds a missing layer of compatibility between
  Kotlin and Apache Spark. It allows Kotlin developers to use familiar language features such as data classes, and
  lambda expressions as simple expressions in curly braces or method references.

* [kotlin-statistics](https://github.com/thomasnield/kotlin-statistics) is a library providing extension functions for 
exploratory and production statistics. It supports basic numeric list/sequence/array functions (from `sum` to `skewness`),
slicing operators (such as `countBy`, `simpleRegressionBy`), binning operations, discrete PDF sampling,
naive bayes classifier, clustering, linear regression, and much more.

* [kmath](https://github.com/mipt-npm/kmath) is a library inspired by [NumPy](https://numpy.org/).
This library supports algebraic structures and operations, array-like structures, math expressions, histograms,
streaming operations, a wrapper around [commons-math](https://commons.apache.org/proper/commons-math/) and
[koma](https://github.com/kyonifer/koma), and more.

* [krangl](https://github.com/holgerbrandl/krangl) is a library inspired by R's [dplyr](https://dplyr.tidyverse.org/)
and Python's [pandas](https://pandas.pydata.org/). This library provides functionality for data manipulation using
a functional-style API; it also includes functions for filtering, transforming, aggregating, and reshaping tabular data.

* [lets-plot](https://github.com/JetBrains/lets-plot) is a plotting library for statistical data written in Kotlin.
Lets-Plot is multiplatform and can be used not only with JVM, but also with JS and Python. 

* [kravis](https://github.com/holgerbrandl/kravis) is another library for the visualization of tabular data inspired by
R's [ggplot](https://ggplot2.tidyverse.org/).

* [londogard-nlp-toolkit](https://github.com/londogard/londogard-nlp-toolkit/) is a library that provides utilities when working with natural language processing such as word/subword/sentence embeddings, word-frequencies, stopwords, stemming, and much more. -->

* [Multik](https://github.com/Kotlin/multik): многомерные массивы в Kotlin. Библиотека предоставляет
Kotlin-идиоматический, типо- и размерно-безопасный API для математических операций над многомерными массивами. Multik
предлагает JVM с возможностью замены и собственные вычислительные движки, а также их комбинацию для оптимальной
производительности;

* [KotlinDL](https://github.com/jetbrains/kotlindl) - это высокоуровневый API глубокого обучения, написанный на Kotlin
и вдохновленный Keras. Он предлагает простые API для обучения моделей глубокого обучения с нуля, импорта существующих
моделей Keras для вывода и использования обучения передаче для настройки существующих предварительно обученных моделей
для ваших задач;

* [Kotlin для Apache Spark](https://github.com/JetBrains/kotlin-spark-api) увеличивает уровень совместимости между
Kotlin и Apache Spark. Это позволяет разработчикам Kotlin использовать знакомые особенности языка, такие как классы
данных и лямбда-выражения, в виде простых выражений в фигурных скобках или ссылок на методы;

* [kotlin-statistics](https://github.com/thomasnield/kotlin-statistics) - это библиотека, которая предоставляет функции-расширения для исследовательской и производственной статистики. Она поддерживает основные функции для обработки числовых списков, последовательностей, массивов (от `sum` до `skewness`), операциями группировки (ориг.: *slicing operators*) (такие как `countBy`, `simpleRegressionBy`), операции группировки, выборки дискретной плотности вероятности, наивный байесовский классификатор, кластеризацию, линейную регрессию и многое другое;

* [kmath](https://github.com/mipt-npm/kmath) - это библиотека, вдохновленная [NumPy](https://numpy.org/). Эта библиотека
поддерживает алгебраические структуры и операции, структуры, подобные массивам, математические выражения, гистограммы,
потоковые операции, обертку вокруг [commons-math](https://commons.apache.org/proper/commons-math/) и [koma](https://github.com/kyonifer/koma)
и многое другое;

* [krangl](https://github.com/holgerbrandl/krangl) - это библиотека, вдохновленная [dplyr](https://dplyr.tidyverse.org/)
в R и [pandas](https://pandas.pydata.org/) из Python. Эта библиотека предоставляет инструментарий для обработки данных с
использованием API функционального стиля; она также включает функции фильтрации, преобразования, агрегирования и
изменения формы табличных данных;

* [lets-plot](https://github.com/JetBrains/lets-plot) - это библиотека построения графиков для статистических данных.
Она мультиплатформенная и может использоваться не только с JVM, но также с JS и Python;

* [kravis](https://github.com/holgerbrandl/kravis)  - это еще одна библиотека для визуализации табличных данных,
вдохновленная [ggplot](https://ggplot2.tidyverse.org/) в R;

* [londogard-nlp-toolkit](https://github.com/londogard/londogard-nlp-toolkit/) - это библиотека, которая предоставляет инструменты для обработки естественных языков, например для работы со словами/частями слов/предложениями, частоты использования слов, стоп-слов, поиском основы слова и многим другим.

<a name="java-libraries"></a>

<!-- ### Java libraries -->
### Java библиотеки

<!-- Since Kotlin provides first-class interop with Java, you can also use Java libraries for data science in your Kotlin code.
Here are some examples of such libraries: -->
Поскольку Kotlin полностью совместим с Java, вы также можете использовать Java-библиотеки для Data Science в своем
Kotlin-коде. Вот несколько примеров таких библиотек:

<!-- * [DeepLearning4J](https://deeplearning4j.org/) - a deep learning library for Java

* [ND4J](https://github.com/deeplearning4j/nd4j) - an efficient matrix math library for JVM

* [Dex](https://github.com/PatMartin/Dex) - a Java-based data visualization tool

* [Smile](https://github.com/haifengl/smile) - a comprehensive machine learning, natural language processing,
linear algebra, graph, interpolation, and visualization system. Besides Java API, Smile also provides a functional
[Kotlin API](https://haifengl.github.io/api/kotlin/smile-kotlin/index.html) along with Scala and Clojure API.
  * [Smile-NLP-kt](https://github.com/londogard/smile-nlp-kt) - a Kotlin rewrite of the Scala implicits for the natural
    language processing part of Smile in the format of extension functions and interfaces.

* [Apache Commons Math](https://commons.apache.org/proper/commons-math/) - a general math, statistics, and machine learning
library for Java

* [NM Dev](https://nm.dev/) - a Java mathematical library that covers all of classical mathematics.

* [OptaPlanner](https://www.optaplanner.org/) - a solver utility for optimization planning problems

* [Charts](https://github.com/HanSolo/charts) - a scientific JavaFX charting library in development

* [CoreNLP](https://stanfordnlp.github.io/CoreNLP/) - a natural language processing toolkit

* [Apache Mahout](https://mahout.apache.org/) - a distributed framework for regression, clustering and recommendation

* [Weka](https://www.cs.waikato.ac.nz/ml/index.html) - a collection of machine learning algorithms for data mining tasks

* [Tablesaw](https://github.com/jtablesaw/tablesaw) - a Java dataframe. It includes a visualization library based on Plot.ly -->

* [DeepLearning4J](https://deeplearning4j.org/) - библиотека глубокого обучения для Java;

* [ND4J](https://github.com/deeplearning4j/nd4j) - эффективная матричная математическая библиотека для JVM;

* [Dex](https://github.com/PatMartin/Dex) - инструмент на основе Java для визуализации данных;

* [Smile](https://github.com/haifengl/smile) - комплексная система машинного обучения, обработки естественного языка,
линейной алгебры, графики, интерполяции и визуализации. Помимо Java API, Smile также предоставляет функциональный
[Kotlin API](https://haifengl.github.io/api/kotlin/smile-kotlin/index.html), а также Scala и Clojure API.;
  * [Smile-NLP-kt](https://github.com/londogard/smile-nlp-kt) - переписанные на Kotlin Scala имплициты для части Smile, отвечающей за обработку естественного языка, в виде функций-расширений и интерфейсов;

* [Apache Commons Math](https://commons.apache.org/proper/commons-math/) - библиотека общей математики, статистики и
машинного обучения для Java;

* [NM Dev](https://nm.dev/) - математическая библиотека Java, охватывающая всю классическую математику;

* [OptaPlanner](https://www.optaplanner.org/) - инструмент поиска решения для проблем оптимизации планирования;

* [Charts](https://github.com/HanSolo/charts) - библиотека JavaFX для научных графиков (в разработке);

* [CoreNLP](https://stanfordnlp.github.io/CoreNLP/) - инструментарий для обработки естественного языка;

* [Apache Mahout](https://mahout.apache.org/) - распределённая программная среда для регрессии, кластеризации и рекомендаций;

* [Weka](https://www.cs.waikato.ac.nz/ml/index.html) - набор алгоритмов машинного обучения для решения задач сбора данных;

* [Tablesaw](https://github.com/jtablesaw/tablesaw) - Java датафрейм (инструменты для загрузки, преобразования,
фильтрации и суммирования данных), включающий в себя основанную на Plot.ly библиотеку
для визуализации данных.

<!-- If this list doesn’t cover your needs, you can find more options in the 
**[Kotlin Machine Learning Demos](https://github.com/thomasnield/kotlin-machine-learning-demos)** GitHub repository with showcases from Thomas Nield. -->
Если в этом списке нет того, что вам нужно, вы можете найти дополнительные варианты в GitHub репозитории
**[Kotlin Machine Learning Demos](https://github.com/thomasnield/kotlin-machine-learning-demos)** с демонстрационными
материалами от Томаса Нилда.
