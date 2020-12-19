# Year_Prediction
## Introduction
The goal of the project is to provide a model able to predict for a given scientific article on NLP the year of publication.
You are in charge to split the data in train/test/validation sets with 80/10/10 respectively for
example. </br>
You can evaluate your model with a MSE error, since making a mistake of ten years or
one year it’s not as important.
The goal is not to reach the highest score. It’s easy, you can train on all the datas and generate a
model that is just a memory and achieve a perfect model, which is not very interesting.</br>
Two mains expectations of the project :
* Propose a model to achieve this goal and explain all your modifications step by step, in
order to improve your result. You can use different models, but you need at least show the evolution
of one model.
* You need to analyse the results : Which years are more difficults ? Why ? Which articles
are more difficult, or which key-words are considered more difficults ?</br>

Answering to this questions is an other way to synthetise the corpus and understand the evolution of
Natural Language Processing.
You can use all tools and models seen during sessions.
You can focus on abstracts or titles (less computational effort) or select a specific subset.
Please : be aware to not use meta-data that help to predict the year ! It will improve dramatically
your precision but destroy your analyse. For example, the place of the conference or edition number
are a strong synonym of the year (the year of articles cited/referenced also)!</br>
The number of articles per year are not equals. Be aware to force balanced year distribution
between train/test/validation sets. For example, if year 2001 is present only in train articles and year
2010 only in evaluation articles, it’s a loss of representativity in evaluation and train respectively.
