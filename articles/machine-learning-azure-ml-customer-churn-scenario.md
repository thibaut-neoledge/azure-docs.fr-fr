<properties title="Analyzing Customer Churn using Microsoft Azure Machine Learning" pageTitle="Analyzing Customer Churn using Microsoft Machine Learning | Azure" description="Case study of developing an integrated model for analyzing and scoring customer churn" metaKeywords="" services="" solutions="" documentationCenter="" authors="barga" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="barga" />

# Analyse de l'attrition des clients à l'aide de Microsoft Azure Machine Learning

Ce document présente une implémentation de référence d'un projet d'analyse de l'attrition des clients, établi à l'aide d'Azure Machine Learning Studio. Ce document évoque également des modèles génériques associés afin de résoudre globalement le problème du taux d'attrition des clients. Nous mesurons également la précision des modèles établis à l'aide d'Azure Machine Learning et évaluons les pistes à suivre pour un développement ultérieur.

## Le problème de l'attrition des clients

Les entreprises du secteur de la consommation et de tous les secteurs en général sont confrontées au problème de l'attrition. Parfois, ce dernier est excessif et influence les décisions relatives aux politiques de l'entreprise. La solution classique consiste à prédire les clients présentant une forte probabilité d'attrition et à répondre à leurs besoins par le biais d'un service de conciergerie ou de campagnes marketing, ou encore en appliquant des dérogations. Ces approches peuvent varier d'un secteur à l'autre, voire même d'un groupe de clients à un autre au sein d'un même secteur (ex. : télécommunications). Le facteur commun est le suivant : les entreprises doivent réduire au minimum ces efforts particuliers de rétention des clients. Ainsi, la méthodologie logique consisterait à évaluer chaque client en fonction de sa probabilité d'attrition et à s'occuper des N clients les plus susceptibles de franchir le pas. Ces derniers sont peut-être les clients les plus rentables. Dans un scénario plus complexe, une fonction de rentabilité est utilisée lors de la sélection des candidats susceptibles de bénéficier d'une dérogation. Cependant, ces considérations ne représentent qu'une partie de la stratégie globale visant à résoudre le problème de l'attrition. Les entreprises doivent également prendre en compte les risques (et la tolérance à l'égard des risques associée), le niveau et le coût de l'intervention, ainsi que la segmentation possible de la clientèle.

## Perspectives et approches du secteur

La gestion sophistiquée de l'attrition témoigne de la maturité d'un secteur. Le secteur des télécommunications en est un exemple type. En effet, les abonnés sont connus pour changer fréquemment d'opérateur. Cette attrition volontaire est l'un des principaux problèmes du secteur. De plus, les opérateurs ont accumulé de nombreuses connaissances sur les *causes d'attrition*. Il s'agit des facteurs qui incitent les clients à changer d'opérateur. Le choix du téléphone, par exemple, est une cause d'attrition bien connue dans le secteur de la téléphonie. De ce fait, une pratique courante consiste à réduire le prix d'un téléphone pour les nouveaux abonnés, tout en faisant payer le prix fort aux abonnés existants qui veulent changer d'appareil. Traditionnellement, cette règle incite les clients à passer d'un opérateur à un autre pour obtenir une nouvelle remise. En conséquence, les opérateurs ont été amenés à revoir leurs stratégies. La volatilité élevée des offres de téléphone est un facteur qui invalide très rapidement les modèles d'attrition basés sur les modèles de téléphone actuels. D'autre part, les téléphones mobiles ne sont pas uniquement des outils de communication. En effet, ce sont également des accessoires de mode (iPhone, par exemple). Ces facteurs de prévision sociaux ne sont pas pris en compte par les jeux de données standard relatifs aux télécommunications. Résultat : vous ne pouvez pas établir de politique sûre en éliminant simplement les motifs connus d'attrition. En fait, une stratégie de modélisation continue, comprenant des modèles classiques quantifiant des variables par catégorie (ex. : arbres de décision), est **obligatoire**.

En utilisant des jeux de données volumineuses sur leurs clients, les entreprises exécutent une analyse des données volumineuses, en particulier une détection de l'attrition en fonction de ces données, afin de proposer une solution efficace à ce problème. Vous pouvez en savoir plus sur la résolution du problème de l'attrition à l'aide des données volumineuses dans la section Recommandations concernant l'ETL.

## Méthodologie de modélisation de l'attrition

Une méthode standard de résolution de l'attrition est décrite dans les schémas 1 à 3 :

1.  Le modèle de risque vous permet d'identifier la façon dont les actions affectent la probabilité et le risque.
2.  Le modèle d'intervention vous permet d'envisager la façon dont le niveau d'intervention pourrait influer sur la probabilité d'attrition et la quantité de valeur vie client.
3.  Cela permet de bénéficier d'une analyse qualitative transformée en campagne marketing proactive ciblant des segments de clients afin de proposer la meilleure offre.

![][0]

Cette approche prévisionnaire est la meilleure façon de régler le problème de l'attrition, mais cela n'est pas si simple : nous devons développer un archétype multi-modèles et identifier des dépendances entre ces modèles. L'interaction entre les modèles peut être encapsulée, comme illustré dans le schéma suivant :

![][1]

*Figure 4 : archétype multi-modèles unifié*

Les interactions entre les différents modèles sont primordiales si nous voulons proposer une méthode globale de rétention de la clientèle. Chaque modèle se dégrade forcément au fil du temps. De ce fait, l'architecture est une boucle implicite (similaire à l'archétype défini par le standard d'exploration de données CRISP-DM, [***3***]).

Le cycle global risque-décision-segmentation marketing/décomposition reste une structure généralisée, applicable à de nombreux problèmes d'entreprise. L'analyse de l'attrition n'est qu'une représentation forte de ce groupe de problèmes, car elle affiche toutes les caractéristiques d'un problème commercial complexe qui ne permet pas l'utilisation d'une solution prédictive simplifiée. Les aspects sociaux de l'approche moderne quant à l'attrition ne sont pas particulièrement mis en lumière dans cette méthode, mais encapsulés dans l'archétype de modélisation, comme dans n'importe quel autre modèle.

L'analyse des données volumineuses est un ajout intéressant. Les entreprises de télécommunication et de vente au détail récupèrent des données exhaustives sur leurs clients. De ce fait, nous pouvons prédire que le besoin d'une connectivité multi-modèles deviendra une tendance. En effet, les tendances émergentes, telles que l'Internet des objets et les appareils polyvalents, permettent aux entreprises d'utiliser des solutions intelligentes à plusieurs niveaux.

 

# Implémentation de l'archétype de modélisation dans Microsoft Azure ML Studio

Étant donné le problème que nous venons de décrire, comment implémenter une méthode de modélisation et d'évaluation intégrée ? Dans cette section, nous expliquerons comment nous avons réussi ceci en utilisant Microsoft Azure Cloud ML Studio.

L'approche multi-modèles est incontournable pour concevoir un archétype global d'attrition. Même la partie évaluative (prédictive) de la méthode doit être multi-modèles.

Le schéma suivant montre le prototype que nous avons créé. Il emploie quatre algorithmes d'évaluation différents dans Cloud ML Studio afin de prévoir l'attrition. L'utilisation d'une méthode multi-modèles n'est pas seulement utile pour créer un classifieur d'ensemble afin d'améliorer l'exactitude, mais également pour se protéger du surajustement et pour améliorer la sélection des caractéristiques prédictives.

![][2]

*Figure 5 : prototype d'une méthode de modélisation de l'attrition*

Les sections suivantes fournissent des informations supplémentaires sur le prototype de modèle d'évaluation que nous avons implémenté à l'aide de Cloud ML Studio.

## Sélection et préparation des données

Les données utilisées pour établir les modèles et évaluer les clients ont été obtenues à partir d'une solution verticale de CRM. Elles ont été masquées pour protéger la confidentialité des clients. Les données elles-mêmes contiennent des informations sur 8 000 abonnements aux États-Unis et combinent trois sources : les données d'approvisionnement (métadonnées d'abonnement), les données d'activité (utilisation du système) et les données du service client. Elles n'incluent pas d'informations sur les clients relatives à l'entreprise. Par exemple, elles ne contiennent pas de métadonnées sur la fidélité ni de score de crédit.

Pour simplifier, ETL et les processus de nettoyage des données ne sont pas utilisés, car nous estimons que la préparation des données a déjà été faite autre part.

La sélection des caractéristiques pour la modélisation se base sur une évaluation de précision préliminaire de l'ensemble de facteurs de prévision, incluse dans le processus à l'aide du modèle de forêt aléatoire. Pour l'implémentation dans Cloud ML Studio, nous avons calculé les valeurs moyennes et médianes, ainsi que les fourchettes des caractéristiques représentatives. Nous avons notamment ajouté des agrégats pour les données qualitatives, telles que des valeurs minimum et maximum pour l'activité de l'utilisateur.

Nous avons également collecté des informations temporelles pour les 6 derniers mois. Nous n'avons utilisé que les six derniers mois, car nous avons analysé les données durant un an et établi que même s'il s'agissait de tendances significatives d'un point de vue statistique, leur effet sur l'attrition est sensiblement réduit passé ce délai.

Le point le plus important est que le processus entier, y compris ETL, la sélection de caractéristiques et la modélisation, a été implémenté dans Cloud ML Studio en utilisant des sources de données dans Microsoft Azure.

Les schémas suivants illustrent les données utilisées :

![][3]

*Figure 6 : extrait d'une source de données (masquée)*

![][4]

*Figure 7 : caractéristiques extraites de la source de données*
 

## Algorithmes utilisés dans le prototype

Nous avons utilisé les quatre algorithmes d'apprentissage automatique suivants pour établir le prototype (sans personnalisation) :

1.  Régression logique (Logistic Regression, LR)
2.  Arbre de décision optimisé (Boosted Decision Tree, BT)
3.  Perceptron moyenné (Averaged Perceptron, AP)
4.  Machines à vecteurs de support (Support Vector Machine, SVM)

Le schéma suivant illustre une partie de la surface de conception de l'expérience, qui indique l'ordre dans lequel les modèles ont été créés :

![][5]

*Figure 8 : création de modèles ML dans Cloud ML Studio*

## Méthodes d'évaluation

Nous avons évalué les quatre modèles à l'aide d'un jeu de données d'apprentissage étiqueté.

Nous avons également soumis le jeu de données d'évaluation à un modèle comparable élaboré à l'aide de l'édition Desktop de SAS Enterprise Miner 12, et mesuré l'exactitude du modèle SAP, ainsi que les quatre modèles Azure Cloud ML.

# Résultats

Dans cette section, nous présentons nos découvertes sur l'exactitude des modèles en fonction du jeu de données d'évaluation.

## Calcul des incertitudes de l'évaluation

En règle générale, l'implémentation dans Azure Cloud ML est derrière SAP en termes d'exactitude, d'environ 10 à 15 % (ASC).

Cependant, la mesure la plus importante pour l'attrition est le taux d'erreur de classification : parmi les N principaux clients les plus susceptibles d'attrition selon le classifieur, lesquels **n'ont pas** quitté l'entreprise et ont pourtant reçu un traitement spécial ? Le schéma suivant compare ce taux d'erreur de classification pour tous les modèles :

![][6]

*Figure 9 : aire sous la courbe du prototype Passau*

### Utilisation de l'aire sous la courbe (ASC) pour comparer les résultats

L'aire sous la courbe (ASC) est une mesure qui représente la mesure globale de *séparabilité* entre les distributions d'évaluations pour des populations positives et négatives. Elle est similaire à la courbe ROC (Receiver Operating Characteristic, caractéristique de fonctionnement du récepteur), mais contrairement à cette dernière, l'ASC ne requiert pas que vous choisissiez une valeur seuil. Au lieu de cela, elle résume les résultats pour **tous** les choix possibles. En comparaison, la courbe ROC traditionnelle indique le taux de positifs sur l'axe vertical et le taux de faux positifs sur l'axe horizontal. De plus, son seuil de classification varie.

L'ASC est généralement utilisée pour mesurer la valeur de différents algorithmes (ou systèmes), car elle permet de comparer des modèles à l'aide de leurs valeurs ASC. Cette méthode est très répandue dans le domaine de la météorologie, des sciences biologiques et dans de nombreux secteurs. Ainsi, l'ASC est un outil répandu pour évaluer les performances d'un classifieur.

### Comparaison des taux d'erreur de classification

Nous avons comparé les taux d'erreur de classification du jeu de données à l'aide des données CRM d'environ 8 000 abonnements.

-   Le taux d'erreur de classification de SAP se situait entre 10 et 15 %.
-   Le taux d'erreur de classification d'Azure ML était de 15 à 20 % pour les 200 à 300 principaux clients susceptibles d'opter pour l'attrition.

Dans le secteur des télécommunications, il est essentiel de s'adresser uniquement aux clients les plus susceptibles d'attrition en leur offrant un service personnalisé ou tout autre traitement spécial. Sur ce plan, l'implémentation d'Azure Cloud ML obtient des résultats identiques au modèle SAP.

De même, l'exactitude est plus importante que la précision, car nous tenons avant tout à identifier correctement les personnes susceptibles de résilier leur abonnement.

Le schéma suivant, provenant de Wikipedia, décrit cette relation sous forme de graphique dynamique facile à comprendre :

![][7]

*Figure 10 : équilibre entre exactitude et précision*

### Résultats du calcul d'incertitude pour le modèle d'arbre optimisé

Le graphique suivant présente les résultats bruts d'une évaluation réalisée à l'aide du prototype Azure Cloud ML pour le modèle d'arbre de décision optimisé à deux classes. Il s'agit du modèle ayant la plus grande exactitude parmi les quatre proposés :

![][8]

*Figure 11 : caractéristiques de l'arbre optimisé*

## Comparaison des performances

Nous avons comparé la vitesse à laquelle les données étaient évaluées à l'aide des modèles Azure Cloud ML et d'un modèle comparable créé à l'aide de l'édition Desktop de SAS Enterprise Miner 12.1.

Le tableau suivant récapitule les performances des algorithmes :

*Tableau 1. Performances générales (exactitude) des algorithmes*

| **LR**       | **BT**          | **AP**                 | **SVM**      |
|--------------|-----------------|------------------------|--------------|
| Modèle moyen | Meilleur modèle | Mauvaises performances | Modèle moyen |

Les modèles hébergés dans Azure Cloud ML ont supplanté le modèle SAP dans 15 à 25 % des cas grâce à sa vitesse d'exécution, mais leur exactitude était largement équivalente.

# Considérations et recommandations

Dans le secteur des télécommunications, plusieurs pratiques ont émergé afin d'analyser l'attrition :

-   Mesures dérivées pour quatre catégories principales :

    -   **Entité (ex. : abonnement)**. Informations de base sur l'abonnement ou le client qui risque de se désabonner.
    -   **Activité**. Permet d'obtenir toutes les informations d'utilisation possibles liées à l'entité. Ex. : nombre de connexions, etc.
    -   **Service client**. Récupère des informations dans les journaux du service client pour indiquer si l'abonné a rencontré des problèmes ou est entré en contact avec le service client.
    -   **Données d'entreprise et concurrentielles**. Permet d'obtenir toutes les informations possibles sur le client (elles peuvent être indisponibles ou difficiles à récupérer).
-   Utilisez l'importance pour la sélection des caractéristiques. Cela implique qu'un arbre optimisé/aléatoire est toujours une approche prometteuse.

L'utilisation des quatre catégories ci-dessus donne l'impression qu'une simple approche *déterministe*, basée sur des index formés sur des facteurs raisonnables par catégorie, doit suffire à identifier les clients risquant de se désabonner. Malheureusement, même si cette notion reste plausible, c'est une fausse idée. En effet, l'attrition est un effet temporel et les facteurs y contribuant sont habituellement dans des états temporaires. Ce qui incite un client à se désabonner aujourd'hui peut être différent demain, et le sera certainement dans six mois. De ce fait, un modèle *probabiliste* est nécessaire.

Cette observation importante est souvent ignorée dans les entreprises, qui préfèrent généralement une approche d'informatique décisionnelle, surtout car elle est plus vendeuse et permet une automatisation directe.

Cependant, la promesse d'une analyse autonome à l'aide d'Azure Cloud ML permet d'envisager que les quatre catégories d'informations, évaluées par division ou service, deviendront des sources utiles pour l'apprentissage automatique relatif à l'attrition.

Une autre fonction intéressante est fournie dans Azure Cloud ML. En effet, vous pouvez ajouter un module personnalisé au référentiel de modules prédéfinis déjà disponibles. Cette capacité offre surtout la possibilité de sélectionner des bibliothèques et de créer des modèles pour les marchés verticaux. C'est un facteur de différenciation primordial d'Azure Cloud ML sur le marché.

Nous espérons pouvoir à nouveau évoquer ce sujet, notamment en ce qui concerne l'analyse de données volumineuses.
  

# Conclusion

Ce document décrit une approche rationnelle pour gérer un problème commun, l'attrition, à l'aide d'une structure générique. Nous avons étudié un prototype de modèles d'évaluation et l'avons implémenté à l'aide de Microsoft Azure Cloud ML. Enfin, nous avons évalué l'exactitude et les performances de la solution prototype par rapport aux algorithmes comparables dans SAP.

**Pour plus d'informations :**

Ce document vous a-t-il été utile ? Donnez-nous votre avis. Dites-nous, sur une échelle de 1 (mauvais) à 5 (excellent), comment vous évalueriez ce document et pourquoi vous lui donneriez cette note ? Par exemple :

-   Le notez-vous bien du fait des bons exemples, des excellentes captures d'écran, de l'écriture claire ou pour toute autre raison ?
-   Le notez-vous mal du fait de mauvais exemples, de captures d'écran floues ou d'une écriture peu claire ?

Ces commentaires nous aideront à améliorer la qualité des livres blancs que nous publions.

[Envoyer mon avis][Envoyer mon avis].
 

# Références

[1][1] Predictive analytics: beyond the predictions, W. McKnight, Information Management, juillet/août 2011, p.18-20.

[2][1] <http://en.wikipedia.org/wiki/Accuracy_and_precision>

[3][2] <http://www.the-modeling-agency.com/crisp-dm.pdf>

[4][3] Big Data Marketing

[5][4] <http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn>
 

# Annexe

![][9]

*Figure 12 : capture d'écran d'une présentation d'un prototype pour l'attrition*

  [0]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-1.png
  [1]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-2.png
  [2]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-3.png
  [3]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-4.png
  [4]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-5.png
  [5]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-6.png
  [6]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-7.png
  [7]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-8.png
  [8]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-9.png
  [Envoyer mon avis]: mailto:sqlfback@microsoft.com
  [9]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-10.png
