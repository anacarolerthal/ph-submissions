---
title: "Analyzing Documents with TF-IDF"
collection: lessons
layout: lesson
slug: analyzing-documents-with-tfidf
date: 2019-05-13
authors:
- Matthew J. Lavin
reviewers:
- Quinn Dombrowski
- Catherine Nygren
editors:
- Zoe LeBlanc
review-ticket: https://github.com/programminghistorian/ph-submissions/issues/206
difficulty: 2
activity: analyzing
topics: [distant-reading]
abstract: This lesson focuses on a foundational natural language processing and information retrieval method called Term Frequency - Inverse Document Frequency (tf-idf). This lesson explores the foundations of tf-idf, and will also introduce you to some of the questions and concepts of computationally oriented text analysis.
mathjax: true
---

{% include toc.html %}

# Aperçu

Cette leçon couvre une méthode de traitement du langage naturel et d'extraction d'informations nommée __tf-idf__, une appellation tirée de l'anglais _Term Frequency - Inverse Document Frequency_. Vous avez peut-être déjà rencontré __tf_idf__ dans le contexte d'une discussion de la modélisation thématique, de l'apprentissage automatique ou d'autres méthodes d'analyse textuelle. __Tf-idf__ apparaît régulièrement dans la littérature scientifique car il s'agit à la fois d'une méthode d'exploration de [corpus](https://fr.wikipedia.org/wiki/Corpus) et d'une étape de prétraitement utile pour plusieurs autres méthodes de forage de textes et de modélisation.

En étudiant __tf_idf__, vous découvrirez une méthode d'analyse textuelle que vous pourrez appliquer immédiatement. Cette leçon vous permettra aussi de vous familiariser avec certaines des questions et certains des concepts de l'analyse textuelle par ordinateur. Notamment, cette leçon explique comment isoler les mots les plus importants d'un document des mots qui ont tendance à apparaître fréquemment dans de nombreux documents rédigés dans une même langue. Outre __tf_idf__, il existe de nombreuses méthodes numériques qui permettent de déterminer les mots et les locutions spécifiques à un ensemble de documents. Je recommande fortement la lecture de ce billet de blogue de Ted Underwood[^1] en complément d'information.

# Préparation

## Connaissances préalables recommandées

- Une familiarité avec Python ou un langage de programmation similaire. Le code de cette leçon a été programmé en Python 3.6 mais vous pouvez exécuter __tf_idf__ dans toutes les versions courantes de Python, en utilisant l'un des divers modules appropriés, ainsi que dans plusieurs autres langages de programmation. Le niveau de compétence en programmation requis est difficile à évaluer mais vous devrez au moins être confortable avec les types de données et les opérations élémentaires. Pour tirer le maximum de cette leçon, il serait aussi souhaitable de suivre un cours comme ["Learn Python3" de Codecademy](https://www.codecademy.com/learn/learn-python-3) ou de compléter certaines des [leçons d'introduction à la programmation en Python](https://programminghistorian.org/en/lessons/introduction-and-installation) du _Programming Historian_ (en anglais pour le moment).
- À défaut de pouvoir suivre la recommandation précédente, vous pourriez [réviser les bases de Python](https://www.learnpython.org/fr/), dont les types de données élémentaires (chaînes de caractères, nombres entiers, nombres réels, tuples, listes et dictionnaires), les variables, les boucles, les classes d'objets et leurs instances.
- La maîtrise des bases d'Excel ou d'un autre tableur pourrait être utile si vous souhaitez examiner les feuilles de calcul en format CSV liées à cette leçon de plus près. Vous pouvez aussi employer le module 'pandas' du langage Python pour lire ces fichiers CSV.

## Avant de commencer

- Installez la version Python 3 de l'environnement de développement Anaconda. La méthode à suivre est expliquée dans la leçon [Text Mining in Python through the HTRC Feature Reader](/en/lessons/text-mining-with-extracted-features) (en anglais). Vous obtiendrez d'un coup le langage Python 3.6 (ou mieux), le module [Scikit-Learn](https://scikit-learn.org/stable/install.html) (qui contient notre version de __tf-idf__) et tout ce qu'il faut pour exécuter du code Python dans un [carnet Jupyter](https://jupyter.org/).
- Il est possible d'obtenir toute cette fonctionnalité sans installer Anaconda ou en choisissant plutôt une alternative plus légère comme [Miniconda](https://docs.conda.io/fr/latest/miniconda.html). Pour plus d'information, consultez la section ["Alternatives à Anaconda"](#alternatives-à-anaconda) à la fin de cette leçon.

## Jeu de données

__Tf-idf__, comme bien d'autres algorithmes informatiques, est plus facile à comprendre en suivant un exemple. J'ai donc préparé pour vous un jeu de données formé de 366 [nécrologies](https://fr.wikipedia.org/wiki/N%C3%A9crologie) historiques publiées dans le _New York Times_ et moissonnées sur le site [https://archive.nytimes.com/www.nytimes.com/learning/general/onthisday/](https://archive.nytimes.com/www.nytimes.com/learning/general/onthisday/) sur lequel, à chaque jour de l'année, le _New York Times_ mettait en vedette la nécrologie d'une personne dont c'était l'anniversaire de naissance.

Les fichiers requis pour suivre la leçon, dont ce jeu de données, peuvent être téléchargés [ici](/assets/tf-idf/lecon-fichiers.zip). Le jeu de données est assez petit pour que vous puissiez ouvrir et lire au moins quelques-uns des fichiers textes. Les données moissonnées sont aussi disponibles dans le répertoire "necrologies", qui contient les fichiers '.html' téléchargés du site web "On This Day" de 2011, et dans le répertoire "txt", qui contient des fichiers '.txt' où l'on retrouve le corps du texte de chaque nécrologie. Ces fichiers ont été générés à l'aide du [module Python](https://docs.python.org/fr/3/library/intro.html) nommé [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/), dont l'utilisation est expliquée dans une autre leçon du _Programming Historian_, [Intro to BeautifulSoup](/en/lessons/intro-to-beautiful-soup) (en anglais).

Ce corpus nécrologique constitue un artéfact historique en soi. Il incarne, d'une certaine manière, la façon dont les questions d'inclusion et de représentation peuvent affecter la décision de publier ou non la nécrologie d'un individu et celle de mettre en évidence cette publication de nombreuses années plus tard. La signification de ce genre de décisions a été soulignée par le _New York Times_ lui-même en mars 2018, lorsque le journal a commencé à publier les nécrologies de "femmes négligées".[^2] Comme l'ont souligné à ce moment Amisha Padnani et Jessica Bennett, "de qui l'on se souvient - et comment on le fait - dépend invariablement d'un jugement. Revoir l'archive nécrologique peut ainsi constituer une leçon brutale sur la manière dont la société évaluait certaines réalisations et les personnes qui en sont responsables" (traduction libre). Vu sous cet angle, le jeu de données proposé ici constitue non pas un échantillon représentatif des nécrologies historiques, mais plutôt une vitrine vers les textes que le _New York Times_ jugeait dignes d'être mis en valeur en 2010-2011. Vous remarquerez que plusieurs des personnages historiques mentionnés sont bien connus, ce qui suggère un effort conscient de se pencher sur l'histoire du _New York Times_ pour choisir les nécrologies selon des critères particuliers.[^3]

## Définition et description de Tf-idf

L'opération appelée 'Term Frequency - Inverse Document Frequency' a été présentée pour la première fois, sous le nom de 'term specificity' (spécificité terminologique), dans un article de Karen Spärck Jones publié en 1972.[^4] Plutôt que de représenter un terme par la fréquence brute de ses apparitions dans un document (c'est-à-dire son nombre d'occurrences) ou par sa fréquence relative (soit son nombre d'occurrences divisé par la longueur du document), l'importance de chaque terme est pondérée en divisant son nombre d'occurrences par le nombre de documents qui contiennent le mot dans le corpus. Cette pondération a pour effet d'éviter un problème fréquent en analyse textuelle: les mots les plus courants dans un document sont souvent les mots les plus courants dans _tous_ les documents. Résultat: les termes dont les pointages __tf_idf__ sont les plus élevés dans un document sont ceux qui apparaissent dans ce document particulièrement souvent par rapport à leur fréquence dans les autres documents du corpus.

Si cette explication n'est pas tout à fait claire, voici une analogie qui pourrait vous aider. Supposez que vous passez un week-end de vacances dans la ville de Idf. Vous désirez choisir un restaurant pour votre dîner en tenant compte de deux facteurs. Premièrement, vous voulez très bien manger. Deuxièmement, vous aimeriez essayer une cuisine locale pour laquelle la ville d'Idf est particulièrement réputée. Autrement dit: vous ne voulez pas vous contenter d'un plat que vous pourriez manger n'importe où. Vous pourriez passer la journée à consulter des évaluations de restaurants en ligne, ce qui serait approprié pour atteindre votre premier objectif. Mais si vous voulez aussi atteindre le second, il vous faudra un moyen de faire la différence entre ce qui est bon, sans plus, et ce qui est particulièrement (ou uniquement) bon.

Il est assez facile, je crois, de constater que la nourriture servie dans un restaurant peut être soit:

1. à la fois bonne et originale;
2. bonne mais pas très originale;
3. originale mais pas très bonne;
4. ni bonne, ni originale.

On peut caractériser les fréquences d'occurence des mots de la même façon. Un mot peut être:

1. Utilisé couramment dans une langue comme le français ou l'anglais et particulièrement fréquent (ou rare) dans un document spécifique;
2. Utilisé couramment dans une langue et ni plus ni moins fréquent dans un document spécifique qu'à l'habitude;
3. Rarement utilisé dans une langue et particulièrement fréquent (ou rare) dans un document spécifique;
4. Rarement utilisé dans une langue et ni plus ni moins fréquent dans un document spécifique qu'à l'habitude.

Pour comprendre comment des mots peuvent apparaître fréquemment sans laisser de traces significatives, ou apparaître rarement et être fortement caractéristiques d'un document, examinons un exemple. Le tableau suivant contient une liste des dix mots les plus fréquents dans l'une des nécrologies de notre corpus du _New York Times_ et leurs nombres d'occurrences respectifs:

| Rang | Terme    | Décompte |
| ---- | -------- | -------- |
| 1    | the      | 21       |
| 2    | of       | 16       |
| 3    | her      | 15       |
| 4    | in       | 14       |
| 5    | and      | 13       |
| 6    | she      | 10       |
| 7    | at       | 8        |
| 8    | cochrane | 4        |
| 9    | was      | 4        |
| 10   | to       | 4        |

Observez cette liste et imaginez-vous en train d'essayer de deviner le sujet de la nécrologie représentée par le tableau. On pourrait émettre l'hypothèse que la présence de _her_ ('elle' ou 'sa') et de _cochrane_ signifie que l'on parle d'une femme nommée Cochrane. Mais il pourrait tout aussi bien s'agir d'une personne originaire de la ville de Cochrane, au Wisconsin (États-Unis), ou d'une personne impliquée dans l'organisation non gouvernementale sans but lucratif [Cochrane](https://fr.wikipedia.org/wiki/Cochrane_(organisation)). Le problème, c'est que la plupart des mots qui apparaissent dans cette liste feraient partie de la liste des mots les plus fréquents dans n'importe quelle nécrologie et même dans n'importe quel bloc de texte en langue anglaise d'une taille le moindrement considérable. C'est que la plupart des langues reposent sur une utilisation massive de mots structurels comme les articles, les conjonctions et les prépositions (dont _the,_ _as,_ _of,_ _to_ et _from_ en anglais) qui forment l'ossature grammaticale des textes et qui apparaissent donc partout, quels que soient les sujets dont les textent traitent. Une liste des mots les plus fréquents dans une nécrologie ne nous fournit donc pas nécessairement beaucoup d'information sur la personne à qui le texte rend hommage. Utilisons maintenant __tf-idf__ pour pondérer les décomptes d'occurrences des mots et comparer cette même nécrologie au reste du corpus nécrologique du _New York Times_. Les dix mots qui obtiennent les pointages les plus élevés sont les suivants:

| Rang | Terme     | Décompte |
| ---- | --------- | -------- |
| 1    | cochrane  | 24.85    |
| 2    | her       | 22.74    |
| 3    | she       | 16.22    |
| 4    | seaman    | 14.88    |
| 5    | bly       | 12.42    |
| 6    | nellie    | 9.92     |
| 7    | mark      | 8.64     |
| 8    | ironclad  | 6.21     |
| 9    | plume     | 6.21     |
| 10   | vexations | 6.21     |

Dans ce nouveau tableau, _she_ ('elle') et _her_ ('elle' ou 'sa') gagnent en importance. _cochrane_ fait toujours partie de la liste, mais on y retrouve aussi deux autres mots qui ressemblent à des noms propres: _nellie_ et _bly_. Or, [Nellie Bly](https://fr.wikipedia.org/wiki/Nellie_Bly) était une journaliste du début du XXe siècle, renommée pour ses enquêtes de fond dont une particulièrement célèbre au cours de laquelle elle s'est fait enfermer dans une institution psychiatrique pendant dix jours pour dénoncer les mauvais traitements infligés aux patients victimes de maladies mentales. De son vrai nom Elizabeth Cochrane Seaman, elle utilisait Nellie Bly comme nom de plume. Ces quelques détails biographiques suffisent à expliquer la présence de sept des dix mots qui apparaissent dans le tableau des pointages __tf-idf__: _cochrane,_ _her,_ _she,_ _seaman,_ _bly,_ _nellie_ et _plume._ Pour comprendre la présence de _mark_, _ironclad_ et _vexations_, il suffit de consulter la nécrologie. Bly est morte à l'hôpital St. Mark. [Son mari](https://fr.wikipedia.org/wiki/Robert_Seaman) était le président de la Ironclad Manufacturing Company. Enfin, une série de _vexations_ ('tracas'), dont des fraudes commises par ses employés, des litiges juridiques et une faillite, ont anéanti sa fortune.[^5] Plusieurs des termes qui apparaissent dans cette liste ne sont mentionnés dans la nécrologie qu'une, deux ou trois fois; ils ne sont donc absolument pas fréquents, mais leur présence dans ce texte se distingue malgré tout de la norme du corpus.

# Exécution de Tf-idf

## Fonctionnement de l'algorithme

__Tf-idf__ peut être implanté de plusieurs façons, certaines plus complexes que d'autres. Avant d'entrer dans les détails, j'aimerais décrire les grandes lignes du fonctionnement d'une version particulière de l'algorithme. Pour ce faire, nous allons revenir à la nécrologie de Nellie Bly et convertir les décomptes des mots les plus fréquents dans ce texte en pointages __tf-idf__. Nous le ferons en répétant les étapes suivies par l'implantation de __tf-idf__ que l'on retrouve dans le module [Scikit-Learn](https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html), qui a servi à produire l'exemple présenté à la section précédente. La plupart des opérations mathématiques requises sont de simples additions, multiplications et divisions. Il faudra cependant, à un moment donné, calculer le [logarithme naturel](https://fr.wikipedia.org/wiki/Logarithme_naturel) d'une variable; la plupart des calculatrices en ligne et des applis de calcul pour appareils mobiles en sont capables. Le tableau ci-dessous présente les décomptes d'occurrences bruts pour les 30 premiers mots qui apparaissent dans la nécrologie de Nellie Bly, en ordre alphabétique; la dernière colonne (__df__) contient le nombre de documents du corpus dans lesquels ces mots sont présents, une mesure appelée la _fréquence de document_. (La fréquence de document d'un mot particulier 'i' peut être représentée par __df<sub>i</sub>__.)

| Indice | Mot        | Décompte | Df  |
| -----  | ---------- | -------- | --- |
| 1      | afternoon  | 1        | 66  |
| 2      | against    | 1        | 189 |
| 3      | age        | 1        | 224 |
| 4      | ago        | 1        | 161 |
| 5      | air        | 1        | 80  |
| 6      | all        | 1        | 310 |
| 7      | american   | 1        | 277 |
| 8      | an         | 1        | 352 |
| 9      | and        | 13       | 364 |
| 10     | around     | 2        | 149 |
| 11     | as         | 2        | 357 |
| 12     | ascension  | 1        | 6   |
| 13     | asylum     | 1        | 2   |
| 14     | at         | 8        | 362 |
| 15     | avenue     | 2        | 68  |
| 16     | balloon    | 1        | 2   |
| 17     | bankruptcy | 1        | 8   |
| 18     | barrel     | 1        | 7   |
| 19     | baxter     | 1        | 4   |
| 20     | be         | 1        | 332 |
| 21     | beat       | 1        | 33  |
| 22     | began      | 1        | 241 |
| 23     | bell       | 1        | 24  |
| 24     | bly        | 2        | 1   |
| 25     | body       | 1        | 112 |
| 26     | born       | 1        | 342 |
| 27     | but        | 1        | 343 |
| 28     | by         | 3        | 349 |
| 29     | career     | 1        | 223 |
| 30     | character  | 1        | 89  |

La formule la plus directe pour calculer la fréquence _inverse_ de document __idf__ d'un mot _i_, requise par __tf-idf__, est __N/df<sub>i</sub>__, où _N_ représente le nombre total de documents dans le corpus. Plusieurs implantations normalisent cependant les résultats à l'aide de calculs additionnels. En règle générale, __Tf-idf__ utilise la normalisation pour deux raisons: d'abord pour éviter que les calculs de fréquences ne soient biaisés par la présence de documents très courts ou très longs, ensuite pour calculer les valeurs de fréquence inverse de document de chaque mot. Par exemple, l'implantation de Scikit-Learn remplace __N__ par __N+1__, calcule le logarithme naturel de __(N+1)/df<sub>i</sub>__ et ajoute 1 au résultat. Nous reviendrons sur la notion de normalisation dans la section intitulée ["Paramètres Scikit-Learn"](#paramètres-scikit-learn)

L'équation suivante décrit les opérations que Scikit-Learn applique pour calculer les valeurs d'__idf__[^6]:

$$ idf_i = ln[\, ({N}+1) /\, {df_i}] + 1 $$

Une fois __idf<sub>i</sub>__ calculé, __tf-idf<sub>i</sub>__ est __tf<sub>i</sub>__ multiplié par __idf<sub>i</sub>__.

$$ tf{\text -}idf_i = tf_i \, \times \, idf_i $$

Les équations mathématiques comme celles-ci peuvent être intimidantes lorsque l'on n'a pas l'habitude d'en lire. Cependant, une fois que l'on a acquis l'expérience nécessaire, elles expliquent le fonctionnement d'un algorithme plus clairement que n'importe quel paragraphe de texte. (Pour plus de détails sur ce sujet, le billet "Do Digital Humanists Need to Understand Algorithms?" de Ben Schmidt constitue un bon point de départ.[^7]) Afin de rendre la signification des équations du __idf__ et du __tf-idf__ plus concrètes, j'ai ajouté deux nouvelles colonnes au tableau des fréquences de termes que nous avons vu précédemment. La première nouvelle colonne contient les pointages __idf__ calculés, tandis que la seconde multiplie les valeurs __Décompte__ et __Idf__ pour obtenir les pointages __tf-idf__ finaux. Notez que les valeurs __idf__ sont plus élevées lorsque les documents apparaissent dans moins de documents (c'est-à-dire, lorsque leurs valeurs __Df__ sont basses), mais que les valeurs __idf__ sont toutes entre 1 et 6. D'autres méthodes de [normalisation](https://fr.wikipedia.org/wiki/Variable_centr%C3%A9e_r%C3%A9duite) pourraient produire des échelles de valeurs différentes.

Notez aussi que la formule de calcul de __tf-idf__ implantée par cette version de l'algorithme fait en sorte que les valeurs ne peuvent jamais être inférieures aux décomptes d'occurrences. Il s'agit d'un effet secondaire de la méthode de normalisation: en ajoutant 1 à la valeur __idf__, nous nous assurons de ne jamais multiplier nos __Décomptes__ par des nombres inférieurs à 1. Cela évite de trop perturber la distribution des valeurs.

| Indice | Mot       | Décompte | Df  | Idf        | Tf-idf      |
| ------ | --------- | -------- | --- | ---------- | ----------- |
| 1      | afternoon  | 1       | 66  | 2.70066923 | 2.70066923  |
| 2      | against    | 1       | 189 | 1.65833778 | 1.65833778  |
| 3      | age        | 1       | 224 | 1.48926145 | 1.48926145  |
| 4      | ago        | 1       | 161 | 1.81776551 | 1.81776551  |
| 5      | air        | 1       | 80  | 2.51091269 | 2.51091269  |
| 6      | all        | 1       | 310 | 1.16556894 | 1.16556894  |
| 7      | american   | 1       | 277 | 1.27774073 | 1.27774073  |
| 8      | an         | 1       | 352 | 1.03889379 | 1.03889379  |
| 9      | and        | 13      | 364 | 1.00546449 | 13.07103843 |
| 10     | around     | 2       | 149 | 1.89472655 | 3.78945311  |
| 11     | as         | 2       | 357 | 1.02482886 | 2.04965772  |
| 12     | ascension  | 1       | 6   | 4.95945170 | 4.95945170  |
| 13     | asylum     | 1       | 2   | 5.80674956 | 5.80674956  |
| 14     | at         | 8       | 362 | 1.01095901 | 8.08767211  |
| 15     | avenue     | 2       | 68  | 2.67125534 | 5.34251069  |
| 16     | balloon    | 1       | 2   | 5.80674956 | 5.80674956  |
| 17     | bankruptcy | 1       | 8   | 4.70813727 | 4.70813727  |
| 18     | barrel     | 1       | 7   | 4.82592031 | 4.82592031  |
| 19     | baxter     | 1       | 4   | 5.29592394 | 5.29592394  |
| 20     | be         | 1       | 332 | 1.09721936 | 1.09721936  |
| 21     | beat       | 1       | 33  | 3.37900132 | 3.37900132  |
| 22     | began      | 1       | 241 | 1.41642412 | 1.41642412  |
| 23     | bell       | 1       | 24  | 3.68648602 | 3.68648602  |
| 24     | bly        | 2       | 1   | 6.21221467 | 12.42442933 |
| 25     | body       | 1       | 112 | 2.17797403 | 2.17797403  |
| 26     | born       | 1       | 342 | 1.06763140 | 1.06763140  |
| 27     | but        | 1       | 343 | 1.06472019 | 1.06472019  |
| 28     | by         | 3       | 349 | 1.04742869 | 3.14228608  |
| 29     | career     | 1       | 223 | 1.49371580 | 1.49371580  |
| 30     | character  | 1       | 89  | 2.40555218 | 2.40555218  |

Rappelons que les tableaux ci-dessus représentent une version spécifique de l'algorithme __tf-idf__. Il en existe d'autres. Et bien entendu, on calcule généralement des valeurs __tf-idf__ pour tous les mots et pour tous les documents du corpus, pas seulement pour 30 mots dans un seul document. C'est ce qui nous permet de savoir quels mots ont les plus forts pointages __tf-idf__ dans chaque document. Pour avoir une meilleure idée de ce à quoi ressemblent les résultats d'un calcul __tf-idf__ complet, veuillez télécharger et ouvrir le fichier Excel des valeurs calculées pour la nécrologie de Bly dans [les documents d'accompagnement de la leçon](/assets/tf-idf/lecon-fichiers.zip). Pour ce faire, ouvrez le fichier d'archive (de type '.zip') et choisissez le fichier 'bly_tfidf_complet.xlsx'

## Comment exécuter tf_idf en Python 3

Dans cette section de la leçon, nous retracerons pas à pas le chemin que j'ai parcouru pour calculer des valeurs de __tf-idf__ pour tous les termes apparaissant dans tous les documents du corpus nécrologique. Si vous désirez suivre le processus de plus près, vous pouvez télécharger les fichiers associés à la leçon, ouvrir l'archive '.zip' et exécuter le carnet Jupyter Notebook intitulé 'TF-IDF-code-fr.ipynb' qui se trouve dans le dossier 'lecon-fichiers'. Vous pouvez aussi créer votre propre carnet Jupyter au même endroit et copier-coller les blocs de code qui apparaissent ci-dessous au moment approprié. Si vous travaillez dans l'environnement Anaconda, consultez la [documentation des carnets Jupyter](https://jupyter-notebook-beginner-guide.readthedocs.io/en/latest/execute.html) pour savoir comment changer le répertoire de travail des carnets. Notez que, comme dans tous les langages de programmation, il existe plusieurs manières de compléter chacune des étapes que nous étudierons ci-dessous.

Mon premier bloc de code est conçu pour récupérer les noms de tous les fichiers '.txt' qui se trouvent dans le répertoire 'txt'. Ces lignes de code importent la classe `Path` du module `pathlib` et invoquent la méthode `Path().rglob()` pour produire une liste de tous les fichiers qui se trouvent dans le répertoire 'txt' et dont les noms se terminent avec l'extension '.txt'. `pathlib` concaténera le nom du répertoire, `file.parent`, à chaque nom de fichier pour construire des chemins complets pour chaque fichier (sous macOS ou Windows).

J'ajoute ainsi chaque nom de fichier à une liste nommée `tous_fichiers_txt`. Enfin, je retourne la longueur de `tous_fichiers_txt` pour vérifier que j'ai bien trouvé les 366 fichiers attendus. Cette approche boucler-et-ajouter est très courante en Python.

```python
from pathlib import Path

tous_fichiers_txt =[]
for fichier in Path("txt").rglob("*.txt"):
     tous_fichiers_txt.append(fichier.parent / fichier.name)

# décompte du nombre de fichiers dans la liste
n_fichiers = len(tous_fichiers_txt)
print(n_fichiers)
```

Un mot au sujet des noms de variables. Les deux méthodes les plus communes pour choisir ces noms accordent respectivement la priorité à la commodité et à la sémantique. Par commodité, on pourrait choisir de nommer une variable __x__ pour qu'il soit facile et rapide de taper son nom au besoin. Un nom de variable sémantique tente, quant à lui, de transmettre au lecteur une information sur la fonction ou l'usage de la variable. En nommant ma liste de fichiers textuels `tous_fichiers_txt` et la variable qui contient la taille de cette liste `n_fichiers`, j'accorde la priorité à la sémantique. En même temps, j'utilise des abbréviations comme `txt` pour 'texte' et `n` pour 'nombre' pour sauver du temps d'entrée et j'ai choisi `tous_fichiers_txt` plutôt que `les_noms_de_tous_les_fichiers_textuels` parce que la concision demeure un objectif important. Les normes concernant l'utilisation des majuscules et des barres de soulignement en Python sont codifiées dans PEP-8, le guide stylistique officiel du langage, avec lequel je vous recommande de vous familiariser.[^8]

Pour diverses raisons, nous voulons que nos calculs s'effectuent en ordre de jour et de mois. (Le corpus contient un fichier pour chaque jour et pour chaque mois de l'année.) Pour ce faire, nous pouvons utiliser la méthode `sort()` pour classer les fichiers en ordre numérique ascendant, puis afficher le premier nom de fichier pour nous assurer qu'il s'agit bien de 'txt/0101.txt'.

```python
tous_fichiers_txt.sort()
tous_fichiers_txt[0]
```

Nous pouvons ensuite utiliser la liste des noms de fichiers pour lire chaque fichier en mémoire et le convertir en un format que Python peut interpréter comme du texte. Le prochain bloc de code contient une autre opération de type boucler-et-ajouter qui parcourt la liste de noms de fichiers et ouvre chacun d'entre eux. J'invoque ensuite la méthode `read()` de Python pour convertir le contenu de chaque fichier textuel en une chaîne de caractères (`str`), ce qui constitue la manière d'indiquer à Python que les données doivent être interprétées comme du texte. J'ajoute chacune de ces chaînes de caractères, une par une, à une nouvelle liste nommée `tous_documents`. Note importante: les chaînes de caractères qui constituent cette liste y apparaissent dans le même ordre que les noms de fichiers dans la liste `tous_fichiers_txt`.

```python
tous_documents = []
for fichier_txt in tous_fichiers_txt:
    with open(fichier_txt) as f:
        fichier_txt_chaine = f.read()
    tous_documents.append(fichier_txt_chaine)
```

C'est tout le travail de mise en place dont nous avons besoin. Les étapes de traitement du texte comme la [division en lexèmes (ou en 'tokens')](https://fr.wikipedia.org/wiki/Analyse_lexicale) et l'élimination de la ponctuation seront effectuées automatiquement lorsque nous utiliserons le `TfidfVectorizer` de Scikit-Learn pour convertir les chaînes de caractères qui représentent nos documents en valeurs __tf-idf__. Le bloc de code ci-dessous importe `TfidfVectorizer` du module Scikit-Learn, qui est préinstallé avec Anaconda. `TfidfVectorizer` est une classe d'objets Python développée en programmation orientée-objet; je construis donc une instance de cette classe, nommée `vectoriseur`, à laquelle je fournis des paramètres spécifiques. (J'aurai plus à dire au sujet de ces paramètres dans la section intitulée [Paramètres Scikit-Learn](#paramètres-scikit-learn)). J'applique ensuite la méthode `fit_transform()` de cet objet à ma liste de chaînes de caractères (la variable nommée `tous_documents`). La variable `documents_transformes` contient les résultats de l'opération `fit_transform()`. Notez que nous pourrions aussi fournir à `TfidfVectorizer` une liste de mots vides (rappelons qu'il s'agit de de mots structurels communs) dont nous ne voulons pas nous préoccuper. Notez aussi que pour réaliser certaines opérations, comme la division en lexèmes ou le filtrage des mots vides, dans une langue autre que l'anglais, il pourrait être nécessaire de pré-traiter les textes à l'aide d'un autre module Python ou de fournir à `TfidfVectorizer` un analyseur ('tokenizer') et/ou une liste de mots vides sur mesure.

```python
# importer le vectoriseur TfidfVectorizer de Scikit-Learn.  
from sklearn.feature_extraction.text import TfidfVectorizer

vectoriseur = TfidfVectorizer(max_df=.65, min_df=1, stop_words=None, use_idf=True, norm=None)
documents_transformes = vectoriseur.fit_transform(tous_documents)
```

La méthode `fit_transform()` ci-dessus transforme la liste de chaînes de caractères en une [matrice creuse](https://fr.wikipedia.org/wiki/Matrice_creuse). Dans le cas qui nous concerne, la matrice contient des valeurs __tf-idf__ pour tous les mots et tous les textes. Les matrices creuses épargnent de la mémoire en laissant de côté toutes les valeurs égales à zéro. Nous avons cependant besoin d'accéder à toutes les valeurs alors le prochain bloc de code invoque la méthode `toarray()` pour convertir la matrice creuse en un [tableau numpy](https://docs.scipy.org/doc/numpy/reference/generated/numpy.array.html). Nous pouvons afficher la longueur de ce tableau pour nous assurer qu'il est de la même taille que notre liste de documents.

```python
documents_transformes_tableau = documents_transformes.toarray()
# la prochaine ligne de code vérifie que le tableau numpy contient le même nombre
# de documents que notre liste de fichiers
len(documents_transformes_tableau)
```
Un tableau numpy ressemble à une liste sans y être identique. Je pourrais rédiger une leçon complète rien que sur les différences entre les deux, mais une seule des caractéristiques des tableaux numpy est importante pour le moment: ils convertissent les données stockées dans `documents_transformes` dans un format qui contient explicitement les pointages __tf-idf__ de tous les mots dans tous les documents. (Rappelons que la matrice creuse, elle, excluait toutes les valeurs égales à zéro.)

Nous voulons que toutes les valeurs soient représentées pour que chaque document soit associé au même nombre de valeurs, soit une pour chaque mot qui existe dans le corpus. Chaque ligne du tableau `documents_transformes_tableau` est elle-même un tableau qui représente un des documents du corpus. Nous disposons donc essentiellement d'une grille dans laquelle chaque rangée représente un document et chaque colonne, un mot. (Imaginez un tableau semblable à ceux des sections précédentes pour chaque document, mais sans étiquettes pour identifier les rangées et les colonnes.)

Pour combiner les valeurs avec leurs étiquettes, il nous faut deux éléments d'information: l'ordre des documents et l'ordre dans lequel les différentes mesures calculées pour chaque mot apparaissent. L'ordre des documents est facile à obtenir puisqu'il s'agit du même que dans la liste `tous_documents`. La liste de tous les mots du corpus, elle, est stockée dans la variable `vectoriseur` et elle suit le même ordre que celui dans lequel `documents_transformes_tableau` utilise pour emmagasiner les données. Nous pouvons utiliser la méthode `get_feature_names()` de la classe `TFIDFVectorizer` pour accéder à cette liste de mots. Puis, chaque rangée de `documents_transformes_tableau` (qui contient les valeurs __tf-idf__ d'un document) peut être jumelée avec la liste de mots. (Pour plus de détails sur les structures de données de type DataFrame du module Pandas de Python, veuillez consulter la leçon ["Visualizing Data with Bokeh and Pandas"](/en/lessons/visualizing-with-bokeh).)

```python
import pandas as pd

# créer un répertoire de sortie s'il n'existe pas déjà
Path("./tf_idf_resultats").mkdir(parents=True, exist_ok=True)

# construire une liste de noms de fichiers de résultats à partir de
# la liste de noms de fichiers de données et du répertoire de sortie
fichiers_resultats = [str(fichier_txt).replace(".txt", ".csv").replace("txt/", "tf_idf_resultats/") for fichier_txt in tous_fichiers_txt]

# traiter chacun des documents du tableau documents_transformes_tableau
# en utilisant enumerate() pour conserver la trace de la position courante dans le tableau
for compteur, document in enumerate(documents_transformes_tableau):
    # construire un objet de la classe DataFrame
    tf_idf_tuples = list(zip(vectoriseur.get_feature_names(), document))
    un_document_format_df = pd.DataFrame.from_records(tf_idf_tuples, columns=['terme', 'pointage']).sort_values(by='pointage', ascending=False).reset_index(drop=True)

    # enregistrer les résultats dans un document CSV, en utilisant
    # la variable 'compteur' pour choisir le nom de fichier
    un_document_format_df.to_csv(fichiers_resultats[compteur])
```

Le bloc de code ci-dessus est composé de trois parties:

1. Après avoir importé le module pandas, le code vérifie l'existence du répertoire de sortie 'tf_idf_resultats'. Si ce répertoire n'existe pas déjà, il est créé à ce moment.

2. Un chemin vers un fichier '.csv' est construit à partir de chacun des noms de fichiers '.txt' qui apparaissent dans la liste construite plus haut. Le processus de construction de la variable `fichiers_resultats` convertira, par exemple, 'txt/0101.txt' (le chemin du premier fichier '.txt' de la liste) en 'tf_idf_resultats/0101.csv', et ainsi de suite pour tous les fichiers du corpus.

3. À l'aide d'une boucle, on associe chaque vecteur de pointages __tf-idf__ avec la liste des mots extraite de `vectoriseur`, on convertit les paires mot/pointage en objets de type DataFrame, et on enregistre chaque DataFrame dans son propre fichier '.csv' (un format textuel courant pour les feuilles de calcul.)

## Interpréter les listes de mots: meilleures pratiques et mises en garde

Lorsque vous exécuterez les blocs de code ci-dessus, vous obtiendrez un répertoire nommé 'tf_idf_resultats' contenant 366 fichiers de type '.csv'. Chacun de ces fichiers contient une liste de mots et de leurs pointages __tf-idf__ pour un document spécifique. Comme nous avons pu le constater dans le cas de la nécrologie de Nellie Bly, ces listes de mots peuvent être très éloquentes; cependant, il faut bien comprendre qu'une surinterprétation de ce genre de résultats peut déformer notre compréhension du texte sous-jacent.

En général, il vaut mieux approcher ces listes de mots en se disant qu'elles seront utiles pour susciter des hypothèses ou des questions de recherche mais que les résultats de __tf-idf__ ne justifieront peut-être pas de conclusions définitives à eux seuls. À titre d'exemple, j'ai assemblé une liste de nécrologies d'individus ayant vécu à la fin du XIXe et au début du XXe siècle qui ont écrit pour des journaux ou pour des magazines et qui étaient associés d'une quelconque façon aux mouvements de réforme sociale. Cette liste inclut Nellie Bly, [Willa Cather](https://fr.wikipedia.org/wiki/Willa_Cather), [W.E.B. Du Bois](https://fr.wikipedia.org/wiki/W._E._B._Du_Bois), [Upton Sinclair](https://fr.wikipedia.org/wiki/Upton_Sinclair) et [Ida Tarbell](https://fr.wikipedia.org/wiki/Ida_Minerva_Tarbell), mais il est possible que d'autres individus dont les nécrologies apparaissent dans le corpus correspondent également à cette description.[^9]

Je m'attendais initialement à ce que plusieurs mots significatifs soient partagés entre ces individus, mais ce n'est pas toujours le cas. Le tableau ci-dessous présente les 20 mots dont les pointages __tf-idf__ sont les plus élevés dans chacune des cinq nécrologies. Chaque liste est dominée par des mots spécifiques à son document (noms propres, lieux, entreprises, etc.) que l'on peut filtrer à l'aide des paramètres de __tf-idf__ ou tout simplement ignorer. D'autre part, on peut chercher des mots qui expriment clairement la relation entre un individu et sa profession littéraire.

| Rang Tf-idf | Nellie Bly | Willa Cather | W.E.B. Du Bois | Upton Sinclair | Ida Tarbell |
| 1 | cochrane | cather | dubois | sinclair | tarbell |
| 2 | her | her | dr | socialist | she |
| 3 | she | she | negro | upton | her |
| 4 | seaman | nebraska | ghana | __books__ | lincoln |
| 5 | bly | miss | peace | lanny | miss |
| 6 | nellie | forrester | __encyclopedia__ | social | oil |
| 7 | mark | sibert | communist | budd | abraham |
| 8 | ironclad | twilights | barrington | jungle | mcclure |
| 9 | __plume__ | willa | fisk | brass | easton |
| 10 | vexations | antonia | atlanta | california | __volumes__ |
| 11 | phileas | mcclure | folk | __writer__ | minerva |
| 12 | 597 | __novels__ | booker | vanzetti | standard |
| 13 | elizabeth | pioneers | successively | macfadden | business |
| 14 | __nom__ | cloud | souls | sacco | titusville |
| 15 | balloon | __book__ | council | __wrote__ | __articles__ |
| 16 | forgeries | calif | party | meat | bridgeport |
| 17 | mcalpin | __novel__ | disagreed | __pamphlets__ | expose |
| 18 | asylum | southwest | harvard | my | trusts |
| 19 | fogg | __verse__ | __arts__ | industry | mme
| 20 | verne | __wrote__ | soviet | __novel__ | __magazine__ |

J'ai utilisé les caractères gras pour souligner des termes qui semblent particulièrement reliés à l'écriture. Cette liste inclut _articles_, _arts_, _book_ (livre), _books_ (livres), _encyclopedia_ (encyclopédie), _magazine_, _nom_, _novel_ (roman), _novels_ (romans), _pamphlets_, _plume_, _verse_ (vers/poésie), _volumes_, _writer_ (auteur/autrice) et _wrote_ (écrit), auxquels on pourrait ajouter les titres de livres spécifiques ou les noms de magazines. Passons outre à ces détails pour le moment et remarquons que, si les listes de Cather et de Sinclair contiennent plusieurs mots associés aux livres et à l'écriture, ce n'est pas le cas pour Bly, Du Bois et Tarbell.

On pourrait facilement sauter aux conclusions. L'identité de Cather semble fortement reliée à son genre, à son attachement à des lieux, à sa fiction et à sa poésie. Sinclair est plus fortement associé à la politique et à ses écrits au sujet de la viande, de l'industrie et du procès controversé de [Nicola Sacco et Bartolomeo Vanzetti](https://fr.wikipedia.org/wiki/Affaire_Sacco_et_Vanzetti) qui a mené à l'exécution des deux individus. Bly est reliée à son pseudonyme, à son mari et à ses écrits portant sur les institutions psychiatriques. Du Bois est relié aux questions de race et à sa carrière universitaire. Quant à Tarbell, ce sont les thèmes sur lesquels elle écrit qui la définissent: les affaires, les monopoles, le géant du pétrole Standard Oil et le président américain Abraham Lincoln. En poussant un peu plus loin, je pourrais argumenter que la discussion du genre semble plus caractéristique des nécrologies de femmes, tandis que la question raciale n'apparaît parmi les termes les plus importants que dans le cas du seul Afro-Américain de la liste.

Chacune de ces observations suscite une question à approfondir, mais sans justifier des généralisations. D'abord, je dois vérifier si les paramètres que j'ai choisis pour __tf-idf__ produisent des effets qui pourraient disparaître dans d'autres conditions; des résultats probants devraient être assez stables pour résister à ce genre d'ajustements. (Nous discuterons de certains de ces paramètres dans la section [Paramètres Scikit-Learn](#paramètres-scikit-learn).) Je devrai ensuite lire au moins quelques-unes des nécrologies pour m'assurer que certains termes ne me transmettent pas de faux signaux. En lisant la nécrologie de Du Bois, par exemple, je pourrais constater que les mentions de son oeuvre "The Encyclopedia of the Negro" contribue au moins en partie au pointage du mot _negro_ dans le texte.

Par ailleurs, je pourrais découvrir que la nécrologie de Bly inclut effectivement des mots comme _journalism_, _journalistic_, _newspapers_ (journaux) et _writing_ (écriture), mais cette nécrologie est très courte et la plupart des mots qui y apparaissent ne le font qu'une ou deux fois. Des mots qui ont de très forts pointages __idf__ sont donc plus susceptibles d'apparaître au sommet de sa liste. Puisque je veux vraiment équilibrer les poids de __tf__ et d'__idf__, je pourrais ne pas tenir compte des mots qui apparaissent seulement dans quelques documents ou encore ignorer les résultats provenant de nécrologies dont la longueur est inférieure à un certain seuil.

Enfin, je peux concevoir des tests pour répondre directement à des questions comme: est-ce que les nécrologies d'Afro-Américains sont plus susceptibles de mentionner la race? Je crois que l'hypothèse 'oui' est plausible mais je devrais tout de même asujettir mes généralisations à l'épreuve d'un examen minutieux avant de tirer des conclusions.

## Quelques manières d'utiliser Tf-idf en histoire numérique

Comme je l'ai déjà mentionné, __tf-idf__ provient du domaine de l'extraction d'informations. La pondération de la fréquence d'occurrence de mots dans les différents documents d'un corpus constitue d'ailleurs toujours une opération courante dans l'industrie du développement Web, notamment dans le cas des moteurs de recherche textuels. En contexte d'analyse culturelle ou d'histoire numérique, cependant, la pertinence de __tf-idf__ se limite à des tâches bien précises. En général, celles-ci appartiennent à l'une de trois catégories:

### 1. En tant qu'outil d'exploration ou de visualisation

Nous avons déjà démontré que des listes de mots accompagnés de pointages __tf-idf__ pour chacun des documents d'un corpus peuvent constituer de puissants outils d'interprétation. Elles peuvent notamment suggérer des hypothèses ou des questions de recherche. Ces listes peuvent aussi former les bases de stratégies d'exploration et de visualisation plus sophistiquées. L'article ["A full-text visualization of the Iraq War Logs"](http://jonathanstray.com/a-full-text-visualization-of-the-iraq-war-logs), de Jonathan Stray et Julian Burgess, en constitue un bon exemple.[^10] Stray et Burgess utilisent des valeurs __tf-idf__ pour construire une visualisation de réseau dans laquelle des registres de la guerre en Irak sont reliés à leurs mots-clés les plus distinctifs. Cette technique de visualisation d'information textuelle a permis à Stray de développer le [Projet Overview](https://www.overviewdocs.com), qui propose aux usagers un tableau de bord à partir duquel naviguer dans des milliers de documents pour visualiser leurs contenus. Nous pourrions employer cette approche pour visualiser notre corpus nécrologique et peut-être y identifier des groupes d'articles dont les mots-clés se ressemblent.

### 2. Pour calculer la similarité des textes et des ensembles de traits caractéristiques

Puisque __tf-idf__ produit souvent des pointages bas pour les mots structurels fréquents et des pointages plus élevés pour les mots associés à la signature thématique d'un texte, cette méthode est appropriée pour les tâches qui requièrent l'identification de similarités entre des textes. Un moteur de recherche appliquera souvent __tf-idf__ à un corpus pour ensuite proposer à l'usager des résultats classés en fonction de la [similarité cosinus](https://fr.wikipedia.org/wiki/Similarit%C3%A9_cosinus) entre les documents et les mots-clés de recherche entrés par l'usager. Le même raisonnement s'applique à des questions comme: "Quelle nécrologie de notre corpus ressemble le plus à celle de Nellie Bly?"

Nous pouvons aussi utiliser __tf-idf__ pour découvrir les mots les plus importants dans un document ou dans un groupe de documents. Par exemple, je pourrais regrouper un ensemble de nécrologies de journalistes (dont celle de Nellie Bly) dans un seul document avant d'appliquer __tf-idf__ à celui-ci. Les résultats de l'opération pourraient servir de règle heuristique pour identifier des termes spécifiques aux nécrologies de journalistes, en comparaison avec l'ensemble des nécrologies du corpus. La liste de mots ainsi obtenue pourrait ensuite servir dans une variété d'autres tâches informatiques.

### 3. En tant qu'étape de prétraitement

Les paragraphes ci-dessus ont effleuré la raison pour laquelle __tf-idf__ sert souvent d'étape de prétraitement dans les calculs d'apprentissage automatique. Par exemple, les pointages __tf-idf__ ont tendance à être plus révélateurs que les décomptes bruts lorsque l'on développe un modèle de classification par apprentissage automatique supervisé, notamment parce qu'ils augmentent les poids des mots reliés aux thèmes des documents tout en réduisant ceux des mots structurels fréquents. Il existe cependant une exception notable à cette règle: l'identification de l'auteur d'un texte anonyme, pour laquelle les mots structurels ont une forte valeur prédictive. (NOTE DU TRADUCTEUR: La leçon intitulée [Introduction à la stylométrie en Python](https://programminghistorian.org/fr/lecons/introduction-a-la-stylometrie-avec-python) présente une application de ce genre de calculs.)

Comme nous le verrons dans la section sur les [paramètres de Scikit-Learn](#paramètres-scikit-learn), __tf-idf__ peut aussi émonder les listes de traits caractéristiques des modèles d'apprentissage automatique; or, il est souvent préférable de développer des modèles basés sur le moins de traits caractéristiques possible.

## Variations sur le thème de Tf-idf

### Paramètres Scikit-Learn

L'objet `TfidfVectorizer` de Scikit-Learn dispose de plusieurs paramètres internes que l'on peut modifier pour influencer les résultats de calcul. En règle générale, tous ces paramètres ont leurs avantages et leurs inconvénients: il n'existe pas de configuration parfaite unique. Il est donc préférable de bien connaître chacun des réglages possibles afin de pouvoir expliquer et défendre vos choix le moment venu. La liste complète des paramètres peut être consultée dans la [documentation de Scikit-Learn](https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html); en voici quelques-uns parmi les plus importants:

#### 1. Mots vides ('stopwords')

Dans le code ci-dessus, j'ai utilisé `python stop_words=None` mais `python stop_words='english'` est aussi disponible. Ce réglage filtrera automatiquement de votre corpus les mots très courants, comme 'the', 'to', and 'of', qui apparaissent dans une [liste prédéfinie](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/feature_extraction/_stop_words.py). Notez que la plupart de ces mots vides ont probablement déjà des pointages __tf-idf__ très bas en raison de leur ubiquité, quoique vos autres réglages pourraient influencer ces pointages. Pour une discussion des listes de mots vides que l'on retrouve dans divers outils de traitement du langage naturel à code source ouvert, veuillez lire ["Stop Word Lists in Free Open-source Software Packages"](https://aclweb.org/anthology/W18-2502).

NOTE DU TRADUCTEUR: Il est aussi possible de remplacer 'None' par une liste de mots vides personnalisée, comme `python stop_words=['le', 'la', 'les']`. Si vous travaillez avec des documents en français, il s'agit d'une alternative potentiellement plus efficace que de se fier au faible pointage __tf-idf__ de la plupart des mots-vides.

#### 2. min_df, max_df

Ces paramètres contrôlent le nombre minimal et le nombre maximal de documents dans lesquels un mot doit apparaître pour être inclus dans les calculs. Les deux paramètres peuvent être exprimés sous forme de nombres réels entre 0 et 1, qui représentent alors des pourcentages de l'ensemble du corpus, ou sous forme de nombres entiers qui représentent des décomptes de documents bruts. En règle générale, spécifier une valeur inférieure à 0.9 pour max_df éliminera la majorité (voire la totalité) des mots vides.

#### 3. max_features

Ce paramètre élague les termes les moins fréquents du corpus avant d'appliquer __tf-idf__. Il peut être particulièrement utile en contexte d'apprentissage automatique, où l'on ne souhaite habituellement pas dépasser le nombre de traits caractéristiques recommandé par les concepteurs de l'algorithme choisi.

#### 4. norm, smooth_idf, and sublinear_tf

Chacun de ces paramètres influencera l'éventail de valeurs numériques que l'algorithme __tf-idf__ produira. Le paramètre 'norm' est compatible avec la normalisation l1 et l2, expliquée sur [machinelearningmastery.com](https://machinelearningmastery.com/vector-norms-machine-learning/). 'Smooth_idf' lisse les résultats en ajoutant la valeur 1 à chaque fréquence de document, comme s'il existait un document additionnel qui contient exactement une occurrence de tous les mots qui apparaissent dans le corpus. 'Sublinear_tf' applique une opération de changement d'échelle aux résultats en remplaçant tf par log(tf). Pour plus de détails au sujet du lissage et de la normalisation dans le contexte de __tf-idf__, veuillez consulter Manning, Raghavan et Schütze.[^11]

### Traits caractéristiques: au-delà des mots

Le concept fondamental de __tf-idf__, qui consiste à pondérer les décomptes d'occurrences en fonction du nombre de documents dans lesquels les mots apparaissent, peut s'appliquer à d'autres traits caractéristiques des textes. Par exemple, il est relativement facile de combiner __tf-idf__ avec la [racinisation](https://fr.wikipedia.org/wiki/Racinisation) ou la [lemmatisation](https://fr.wikipedia.org/wiki/Lemme_(linguistique)), deux méthodes courantes qui permettent de regrouper de multiples déclinaisons et conjugaisons du même mot en une seule forme. Par exemple, la racine de _happy_ et _happiness_ est _happi_ tandis que le lemme qui les regroupe est _happy_. Une fois la racinisation ou la lemmatisation complétée, on peut remplacer les décomptes de mots par les décomptes de racines ou de lemmes avant d'appliquer __tf-idf__. Notez que, puisque ces opérations fusionnent plusieurs formes apparentées en une seule, les lemmes et les racines auront des décomptes d'occurrences plus élevés que chacun des mots qu'ils regroupent, et donc des valeurs __tf-idf__ habituellement plus basses.

On peut aussi appliquer la transformation __tf-idf__ à des locutions ou à des n-grammes, c'est-à-dire à des séquences de mots consécutifs. Un article intitulé  ["These Are The Phrases Each GOP Candidate Repeats Most"](https://fivethirtyeight.com/features/these-are-the-phrases-each-gop-candidate-repeats-most/), publié sur fivethirtyeight.com en mars 2016, utilise cette approche pour calculer les fréquences inverses de documents de phrases entières plutôt que celles de mots.[^12]

## Tf-idf et méthodes alternatives communes

On peut comparer __Tf-idf__ à plusieurs autres méthodes qui servent à isoler et/ou à classifier les mots les plus importants dans un document ou dans une collection de documents. Cette section mentionne brièvement trois de ces méthodes alternatives, apparentées mais distinctes, qui mesurent des aspects similaires mais non identiques de l'information textuelle.

### 1. Spécificité ('Keyness')

Plutôt que de transformer les décomptes d'occurrences à l'aide de calculs, la spécificité produit une valeur numérique qui indique jusqu'à quel point la présence d'un mot dans un document est statistiquement typique ou atypique par rapport à l'ensemble du corpus. Par exemple, à l'aide d'un [test du khi-carré](https://fr.wikipedia.org/wiki/Test_du_%CF%87%C2%B2), il est possible de mesurer l'écart entre la fréquence d'occurrence d'un mot et la norme du corpus, puis de dériver une [valeur p](https://fr.wikipedia.org/wiki/Valeur_p) qui indique la probabilité d'observer cette fréquence d'occurrence dans un échantillon aléatoire. Pour plus d'information sur la spécificité, voir Bondi et Scott.[^13]

NOTE DU TRADUCTEUR: En anglais, 'keyness' est un terme générique qui regroupe toute une panoplie de mesures statistiques qui tentent d'assigner une signification quantifiable à la présence d'un terme dans un document ou dans un ensemble de documents, en comparaison avec un corpus plus étendu. En français, le terme 'spécificité' a acquis un sens plus précis suite aux travaux de Pierre Lafon; voir notamment l'article de 1980 "Sur la variabilité de la fréquence des formes dans un corpus", publié dans la revue _Mots_, vol. 1, no. 1.

### 2. Modèles thématiques

La modélisation thématique et __tf-idf__ sont des techniques radicalement différentes, mais je constate que les néophytes en matière d'humanités numériques désirent souvent modéliser les thèmes d'un corpus dès le début alors que __tf-idf__ constituerait parfois un meilleur choix.[^14] Puisque l'algorithme est transparent et que ses résultats sont reproductibles, __tf-idf__ est particulièrement utile lorsque l'on souhaite obtenir une vue d'ensemble d'un corpus, à vol d'oiseau, pendant la phase d'exploration initiale de la recherche. Comme le mentionne Ben Schmidt, les chercheurs qui emploient la modélisation thématique doivent reconnaître que les thèmes qui en ressortent ne sont pas forcément aussi cohérents qu'on le souhaiterait.[^15] C'est l'une des raisons pour lesquelles __tf-idf__ a été intégré au [Projet Overview](https://www.overviewdocs.aycom).

Les modèles thématiques peuvent aussi aider les chercheurs à explorer leurs corpus et ils offrent de nombreux avantages, notamment la capacité de suggérer de vastes catégories ou "communautés" de textes, mais il s'agit d'une caractéristique commune à l'ensemble des méthodes d'apprentissage automatique non supervisées. Les modèles thématiques sont particulièrement attrayants parce qu'ils assignent à chaque document des valeurs numériques qui mesurent jusqu'à quel point chacun des thèmes y est important et parce qu'ils représentent ces thèmes sous forme de listes de mots coprésents, ce qui suscite de fortes impressions de cohérence. Cependant, l'algorithme probabiliste qui sous-tend la modélisation thématique est très sophistiqué et il est facile d'en déformer les résultats si l'on n'est pas assez prudent. Les mathématiques derrière __tf-idf__, elles, sont assez simples pour être expliquées dans une feuille de calcul Excel.

### 3. Résumé automatique des textes

Le résumé automatique est une autre manière d'explorer un corpus. Rada Mihalcea et Paul Tarau, par exemple, ont publié au sujet de TextRank, un modèle de classement basé sur la théorie des graphes, aux possibilités prometteuses pour l'extraction automatique de mots et de phrases-clés.[^16] Comme dans le cas de la modélisation thématique, TextRank approche l'extraction d'informations d'une manière complètement différente que __tf-idf__ mais les objectifs des deux algorithmes ont beaucoup en commun. Cette méthode pourrait être appropriée pour votre propre recherche, surtout si votre but consiste à obtenir assez rapidement une impression générale du contenu de vos documents avant de construire un projet de recherche plus poussé.

# Références et lectures supplémentaires

- Beckman, Milo. "These Are The Phrases Each GOP Candidate Repeats Most," _FiveThirtyEight_, 10 mars 2016. https://fivethirtyeight.com/features/these-are-the-phrases-each-gop-candidate-repeats-most/

- Bennett, Jessica et Amisha Padnani. "Overlooked," 8 mars 2018. https://www.nytimes.com/interactive/2018/obituaries/overlooked.html

- Blei, David M., Andrew Y. Ng et Michael I. Jordan, "Latent Dirichlet Allocation", _Journal of Machine Learning Research_ 3 (Janvier 2003): 993-1022.

- Bondi, Marina et Mike Scott, dirs. _Keyness in Texts_. Philadelphie: John Benjamins, 2010.

- Documentation de TfidfVectorizer (en anglais). https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html

- Grimmer, Justin et Gary King. "Quantitative Discovery from Qualitative Information: A General-Purpose Document Clustering Methodology (2009)". Rencontre APSA 2009 à Toronto. Disponible au https://ssrn.com/abstract=1450070

- "Ida M. Tarbell, 86, Dies in Bridgeport" _The New York Times_, 7 janvier 1944, 17. https://www.nytimes.com

- Lafon, Pierre. "Sur la variabilité de la fréquence des formes dans un corpus." _Mots_ 1, no. 1 (1980): 127-165.

- Manning, C.D., P. Raghavan et H. Schütze, _Introduction to Information Retrieval_. Cambridge: Cambridge University Press, 2008.

- Mihalcea, Rada et Paul Tarau. "Textrank: Bringing order into text." Dans _Proceedings of the 2004 conference on empirical methods in natural language processing_, 2004.

- "Nellie Bly, Journalist, Dies of Pneumonia" _The New York Times_, 28 janvier 1922, 11. https://www.nytimes.com

- Salton, G. et M.J. McGill, _Introduction to Modern Information Retrieval_. New York: McGraw-Hill, 1983.

- Schmidt, Ben. "Do Digital Humanists Need to Understand Algorithms?" _Debates in the Digital Humanities 2016_. Édition en ligne. Minneapois: University of Minnesota Press. http://dhdebates.gc.cuny.edu/debates/text/99

- --. "Words Alone: Dismantling Topic Models in the Humanities," _Journal of Digital Humanities_. Vol. 2, No. 1 (2012): n.p. http://journalofdigitalhumanities.org/2-1/words-alone-by-benjamin-m-schmidt/

- Spärck Jones, Karen. "A Statistical Interpretation of Term Specificity and Its Application in Retrieval." _Journal of Documentation_ 28, no. 1 (1972): 11–21.

- Stray, Jonathan et Julian Burgess. "A Full-text Visualization of the Iraq War Logs," 10 décembre 2010 (dernière mise à jour en avril 2012). http://jonathanstray.com/a-full-text-visualization-of-the-iraq-war-logs

- Underwood, Ted. "Identifying diction that characterizes an author or genre: why Dunning's may not be the best method," _The Stone and the Shell_, 9 novembre 2011. https://tedunderwood.com/2011/11/09/identifying-the-terms-that-characterize-an-author-or-genre-why-dunnings-may-not-be-the-best-method/

- --. "The Historical Significance of Textual Distances", Atelier LaTeCH-CLfL (Version préimpression), COLING, Santa Fe, 2018. https://arxiv.org/abs/1807.00181

- van Rossum, Guido, Barry Warsaw et Nick Coghlan. "PEP 8 -- Style Guide for Python Code." 5 juillet 2001 (mise à jour: juillet 2013). https://www.python.org/dev/peps/pep-0008/

- Whitman, Alden. "Upton Sinclair, Author, Dead; Crusader for Social Justice, 90" _The New York Times_, 26 novembre 1968, 1, 34. https://www.nytimes.com

- "W. E. B. DuBois Dies in Ghana; Negro Leader and Author, 95" _The New York Times_, 28 août 1963, 27. https://www.nytimes.com

- "Willa Cather Dies; Noted Novelist, 70" _The New York Times_, 25 avri 1947, 21. https://www.nytimes.com


## Alternatives à Anaconda

Si vous n'utilisez pas Anaconda, il faudra vous assurer de disposer des outils prérequis suivants:

1. Une installation de Python 3 (préférablement Python 3.6 ou une version plus récente);
2. Idéalement, un environnement virtuel dans lequel installer et exécuter le Python;
3. Le module Scikit-Learn et ses dépendances (voir [http://scikit-learn.org/stable/install.html](http://scikit-learn.org/stable/install.html));
4. Jupyter Notebook et ses dépendances.

# Notes

[^1]: Underwood, Ted. "Identifying diction that characterizes an author or genre: why Dunning's may not be the best method," _The Stone and the Shell_, 9 novembre 2011. <https://tedunderwood.com/2011/11/09/identifying-the-terms-that-characterize-an-author-or-genre-why-dunnings-may-not-be-the-best-method/>

[^2]: Bennett, Jessica et Amisha Padnani. "Overlooked," March 8, 2018. <https://www.nytimes.com/interactive/2018/obituaries/overlooked.html>

[^3]: Ce jeu de données est tiré d'une version du site "On This Day" du _New York Times_ qui n'a pas été mise à jour depuis le 31 janvier 2011 et qui a été remplacée par un nouveau blogue plus élégant situé au [https://learning.blogs.nytimes.com/on-this-day/](https://learning.blogs.nytimes.com/on-this-day/). Ce qui reste sur le site "On This Day" est une page HTML statique pour chaque jour de l'année (0101.html, 0102.html, etc.), y compris une page pour le 29 février (0229.html). Le contenu semble avoir été écrasé à chaque mise à jour; il n'y a donc pas d'archives du contenu publié à chaque année. On peut présumer que les pages associées aux jours de janvier ont été mises à jour pour la dernière fois en 2011, tandis que celles pour les dates entre le 1er février et de 31 décembre ont probablement été mises à jour pour la dernière fois en 2010. La page du 29 février a probablement été changée pour la dernière fois le 29 février 2008.

[^4]: Spärck Jones, Karen. "A Statistical Interpretation of Term Specificity and Its Application in Retrieval." _Journal of Documentation_ vol. 28, no. 1 (1972): 16.

[^5]: "Nellie Bly, Journalist, Dies of Pneumonia" _The New York Times_, 28 janvier 1922, 11. <https://www.nytimes.com>

[^6]: Documentation de TfidfVectorizer. <https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html>

[^7]: Schmidt, Ben. "Do Digital Humanists Need to Understand Algorithms?" _Debates in the Digital Humanities 2016_. Édition en ligne. (Minneapolis: University of Minnesota Press): n.p. <http://dhdebates.gc.cuny.edu/debates/text/99>

[^8]: van Rossum, Guido, Barry Warsaw et Nick Coghlan. "PEP 8 -- Style Guide for Python Code." 5 juillet 2001 (mise à jour en juillet 2013). <https://www.python.org/dev/peps/pep-0008/>

[^9]: "Ida M. Tarbell, 86, Dies in Bridgeport" _The New York Times_, 7 janvier 1944, 17. <https://www.nytimes.com>; "Nellie Bly, Journalist, Dies of Pneumonia" _The New York Times_, 28 janvier 1922, 11. <https://www.nytimes.com>; "W. E. B. DuBois Dies in Ghana; Negro Leader and Author, 95" _The New York Times_, 28 août 1963, 27. <https://www.nytimes.com>; Whitman, Alden. "Upton Sinclair, Author, Dead; Crusader for Social Justice, 90" _The New York Times_, 26 novembre 1968, 1, 34. <https://www.nytimes.com>; "Willa Cather Dies; Noted Novelist, 70" _The New York Times_, 25 avril 1947, 21. <https://www.nytimes.com>

[^10]: Stray, Jonathan et Julian Burgess. "A Full-text Visualization of the Iraq War Logs," 10 décembre 2010 (mise à jour en avril 2012). <http://jonathanstray.com/a-full-text-visualization-of-the-iraq-war-logs>

[^11]: Manning, C.D., P. Raghavan et H. Schütze, _Introduction to Information Retrieval_. (Cambridge: Cambridge University Press, 2008): 118-120.

[^12]: Beckman, Milo. "These Are The Phrases Each GOP Candidate Repeats Most," _FiveThirtyEight_, 10 mars 2016. <https://fivethirtyeight.com/features/these-are-the-phrases-each-gop-candidate-repeats-most/>

[^13]: Bondi, Marina et Mike Scott, eds. _Keyness in Texts_. (Philadelphie: John Benjamins, 2010).

[^14]: Il n'est habituellement pas recommandé d'appliquer __tf-idf__ comme prétraitement avant de produire un modèle thématique. Voir <https://datascience.stackexchange.com/questions/21950/why-we-should-not-feed-lda-with-tfidf>

[^15]: Schmidt, Ben. "Words Alone: Dismantling Topic Models in the Humanities," _Journal of Digital Humanities_. Vol. 2, No. 1 (2012): n.p. <http://journalofdigitalhumanities.org/2-1/words-alone-by-benjamin-m-schmidt/>

[^16]: Mihalcea, Rada et Paul Tarau. "Textrank: Bringing order into text." Dans _Proceedings of the 2004 conference on empirical methods in natural language processing_. 2004.
