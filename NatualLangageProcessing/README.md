## <a href="http://www.insa-toulouse.fr/" ><img src="http://www.math.univ-toulouse.fr/~besse/Wikistat/Images/Logo_INSAvilletoulouse-RVB.png" style="float:left; max-width: 80px; display: inline" alt="INSA"/> |  [*Mathématiques Appliquées*](http://www.math.insa-toulouse.fr/fr/index.html), [`Science des Données`](http://www.math.insa-toulouse.fr/fr/enseignement.html) 

## [Ateliers: Technologies des Données Massives](https://github.com/wikistat/Ateliers-Big-Data) 


<a href="https://www.python.org/"><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Python_logo_and_wordmark.svg/390px-Python_logo_and_wordmark.svg.png" width=250, style="max-width: 110px; display: inline" alt="Python"/></a> <a href="http://spark.apache.org/"> <img src="http://spark.apache.org/images/spark-logo-trademark.png" width=200, style="max-width: 80px; display: inline" alt="Spark"/> </a> <a href="https://www.tensorflow.org/"><img src="https://avatars0.githubusercontent.com/u/15658638?s=200&v=4" width=90 style="max-width: 40px; display: inline" alt="TensorFlow"/></a>  <a href="https://keras.io/"><img src="https://s3.amazonaws.com/keras.io/img/keras-logo-2018-large-1200.png" width=250, style="max-width: 100px; display: inline" alt="Keras"/></a>

L'objectifs de ces ateliers ou tutoriels sous forme de calepins ([*jupyter notebooks*](http://jupyter.org/)) est d'introduire le **passage à l'échelle Volume** des méthodes d'apprentissage; **processus qui transforme un statisticien en *Data Scientist*.** 

# Traitement Naturel du language (*ou NLP*).

# Catégorisation de Produits Cdiscount

**Résumé** Il s'agit d'une version simplifiée du concours proposé par Cdiscount et paru sur le site [datascience.net](https://www.datascience.net/fr/challenge). Les données d'apprentissage sont accessibles sur demande auprès de Cdiscount mais les solutions de l'échantillon test du concours ne sont pas et ne seront pas rendues publiques. Un échantillon test est donc construit pour l'usage de ce tutoriel.  L'objectif est de prévoir la catégorie d'un produit à partir de son descriptif (*text mining*). Seule la catégorie principale (1er niveau, 47 classes) est prédite au lieu des trois niveaux demandés dans le concours. L'objectif est plutôt de comparer les performances des méthodes et technologies en fonction de la taille de la base d'apprentissage ainsi que d'illustrer sur un exemple complexe le prétraitement de données textuelles. 

Le jeux de données complet (15M produits) permet un test en vrai grandeur du **passage à l'échelle volume** des phases de préparation (*munging*), vectorisation (hashage, TF-IDF) et d'apprentissage en fonction de la technologie utilisée.

La synthèse des résultats obtenus est développée par [Besse et al. 2016](https://hal.archives-ouvertes.fr/hal-01350099) (section 5).

## Tutoriels
Plusieurs versions sont disponibles; exécuter les celles principales.

- [Atelier-Python3-Cdiscount-](https://github.com/wikistat/Ateliers-Big-Data/blob/master/Cdiscount/Atelier-Python-3-Cdiscount.ipynb). Version en Python 3 avec [scikit-learn](http://scikit-learn.org/stable/) de préparation (*munging*), vectorisation (hashage, TF-IDF) de données textuelles, modélisation par régression logistique (un modèle par classe).
- [Atelier-pyspark-Cdiscount](https://github.com/wikistat/Ateliers-Big-Data/blob/master/Cdiscount/Atelier-pyspark-Cdiscount.ipynb). Version en PySpark avec [MLlib](http://spark.apache.org/mllib/)) de préparation (*munging*), vectorisation (hashage, TF-IDF) de données textuelles, modélisation par régression logistique (un modèle par classe). Une [autre version](https://github.com/wikistat/Ateliers-Big-Data/blob/master/Cdiscount/Atelier-pyspark_pipeline-Cdiscount.ipynb) construit des *pipelines*.
- R n'est pas adapté au traitement de données aussi volumineuses sur un simple poste de travail. 

## Présentation
### Objectif en fouille de texte
Il s'agit d'un  problème récurrent du commerce en ligne qui se présente sous la forme suivante. Un commerçant partenaire d'un site en ligne souhaite proposer l'ensemble d'un catalogue d'articles à la vente, chacun décrit par un court texte en langage naturel. Pour assurer le maximum de visibilité des produits, ce site doit assurer une catégorisation homogène des produits malgré leurs origines très variées: les commerçants partenaires. A partir de la liste des articles déjà présents sur le site (base d'apprentissage), il s'agit de déterminer la catégorie d'un nouvel article, c'est-à-dire permettre de l'introduire dans l'arborescence des catégories et sous-catégories du site ; c'est donc encore d'un problème de discrimination  ou classification supervisée mais appliquée à de la fouille de textes.

### Etapes
Le traitement se décompose en trois étapes bien distinctes. La première est un pré-traitement ou nettoyage des données. La deuxième est une  *vectorisation* ou quantification des textes, de façon à remplacer les mots par des nombres d'occurrences ou plutôt par les valeurs prises par une liste de variables (*features*) mesurant des fréquences relatives d'une liste déterminée de regroupements de mots. Enfin, une fois construite une matrice, généralement  *creuse*, différentes *méthodes d'apprentissage* sont testées dans la 3ème étape afin de prévoir, au mieux, la catégorie des articles d'un échantillon test.

La préparation des données (*data munging*) est très souvent l'étape la plus délicate et la plus *chronophage* de la *Science des Données* de la vraie vie, par opposition à des données rendues publiques et souvent exploitées pour illustrer ou comparer des méthodes. Ces dernières, comme celles de l'exemple de reconnaissance des caractères, sont très "propres": pas de données manquantes, ou trop atypiques, d'erreur de codage...  Souvent négligé des présentations pédagogiques, le pré-traitement est néanmoins primordial pour assurer la qualité et le pouvoir prédictif des nouvelles variables ainsi construites et qui influent directement sur la pertinence des modèles. D'autre part, ces phases d'extraction, nettoyage, recodage, transformation... des données, conduisent très souvent à une réduction drastique de leur volume. Des données initialement massives, il ressort une matrice souvent adaptée à la mémoire d'un plus ou moins gros ordinateur et ces pré-traitements peuvent rendre inutile une architecture distribuée pour la suite des analyses.

## Préparation des textes
Voici la liste des traitements généralement opérés sur des données textuelles.

- **Nettoyage**, Suppression des caractères mal codés et de ponctuation, transformation des majuscules en minuscules, en remarquant que ces transformations ne seraient pas pertinentes pour un objectif de détection de pourriels.
- **Suppression** des mots inutiles (*stop words*) ou mots de liaison, articles qui n'ont *a priori* pas de pouvoir discriminant.
- **Racinisation** ou *stemming*. Les mots sont réduits à leur seule racine afin de réduire la taille du dictionnaire. 
- **Hashage**. Une fonction de hashage est appliquée pour transformer chaque mot en un index unique en ajoutant une *astuce* ou *hashing trick*. Le nombre de valeurs possibles (modulo une division entière) prises par la fonction de hashage  est un paramètre `n_hash`. Ainsi, les mots sont automatiquement et arbitrairement regroupés pour aboutir à un nombre prédéterminé de codes possibles. Plus précisément, la fonction de hashage *h* est définie sur l'espace des entiers naturels et à valeurs *i=h(j)* dans un ensemble fini *(1,..., `n_hash`)* des variables ou *features*. Ainsi le poids de l'indice *i$*, du nouvel espace, est l'association de tous les poids d'indice *j* tels que *i=h(j)* de l'espace original. Ici, les poids sont associés d'après la méthode décrite par wein et al. (2009}. La fonction *h* n'est pas générée aléatoirement. Ainsi pour un même fichier d'apprentissage (ou de test) et pour un même entier `n_hash`, le résultat de la fonction de hashage est identique. 
- **Xgram**. La fonction de hashage est appliquée aux mots (*unigram*) ou aux couples (*bigram*) de deux mots consécutifs. Ce deuxième choix permet de lever beaucoup d'ambiguïté du langage mais risque de faire exploser le volume du dictionnaire. C'est encore un paramètre à optimiser.
- **TF-IDF** (*Term Frequency-Inverse Document Frequency*) Il  permet de faire ressortir l'importance relative de chaque mot *m* (ou couples de mots consécutifs) dans un texte-produit ou un document *d*, par rapport à la liste entière des documents. La fonction *TF(m,d)* compte le nombre d'occurrences du mot *m* dans le document *d*. La fonction *IDF(m)*  mesure l'importance du terme dans l'ensemble des documents ou descriptifs en donnant plus de poids aux termes les moins fréquents car considérés comme les plus discriminants (motivation analogue à celle de la métrique du *chi2* en analyse des correspondances). *IDF(m)=log((D+1)/(f(m)+1))* (version *smooth* adoptée dans *Scikit-learn* et *MLlib*) où *D* est le nombre de documents, la taille de l'échantillon d'apprentissage, et *f(m)* le nombre de documents ou descriptifs contenant le mot *m*. La nouvelle variable ou *feature* est *V_m(d)=TF(m,d) x IDF(m)*. 

Comme pour les transformations des variables quantitatives (centrage, réduction), les mêmes transformations, c'est-à-dire la même fonction de hashage et le même ensemble de pondérations (*IDF*), sont calculés, appliqués sur l'échantillon d'apprentissage puis appliqués sur celui de test. 

Il s'agit finalement d'évaluer distinctement ces trois étapes au regard des technologies disponibles. De façon schématique, la première (nettoyage) est très facilement parallélisable et peut se décomposer en séquences de traitements ou d'étapes fonctionnelles *Map* tout à fait adaptées à une architecture distribuée et donc des données très massives. En revanche, les étapes suivantes (vectorisation, modélisation) nécessitent des comparaisons plus fouillées.


## Données Cdiscount
Il s'agit d'une version simplifiée du concours proposé par *Cdiscount* et paru sur le site [datascience.net](http://datascience.net). Les données d'apprentissage sont accessibles sur demande auprès de *Cdiscount* dans le forum de ce site et les solutions gagnantes ont été présentées aux journées de Statistique de Montpellier ([Goutorbe et al. 2016](http://papersjds16.sfds.asso.fr/submission_114.pdf)). Comme les solutions de l'échantillon test du concours ne sont pas et ne seront pas rendues publiques, un échantillon test est donc extrait pour l'usage de cet exemple. L'objectif est de prévoir la catégorie d'un produit à partir de son descriptif. Seule la catégorie principale (1er niveau de 47 classes) est modélisée au lieu des trois niveaux demandés dans le concours (5789 classes). L'objectif n'est pas de faire mieux que les solutions gagnantes basées sur des *pyramides* complexes de régressions logistiques programmées en Python mais de comparer les performances des méthodes et technologies en fonction de la taille de la base d'apprentissage ainsi que d'illustrer, sur un exemple réel, le pré-traitement de données textuelles. La stratégie de sous ou sur-échantillonnage des catégories très déséquilibrées qui permet d'améliorer la prévision n'a pas été mise en \oe uvre.

Les données se présentent sous la forme d'un fichier texte de 3.5 Go. Il comporte quinze millions de lignes, un produit par ligne contenant sa catégorie et le descriptif. La nature brute de ces données, leur volume, permet de considérer toute la chaîne de traitement et pas seulement la partie apprentissage.

R, peu adapté à une fouille de textes d'un tel volume, n'a pas été testé, seules sont comparées deux séquences d'analyses identiques réalisées avec Python   (*Scikit-learn*) et Spark (*Mllib*).



