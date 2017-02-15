---
title: "Ingénierie et sélection de caractéristiques dans Azure Machine Learning | Microsoft Docs"
description: "Cette rubrique explique les finalités de l’ingénierie de caractéristiques et de la sélection de caractéristiques et fournit des exemples de leur rôle dans le processus d’amélioration des données de l’apprentissage automatique."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 9ceb524d-842e-4f77-9eae-a18e599442d6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: deprecated
ms.date: 01/18/2017
ms.author: zhangya;bradsev
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: machine-learning-data-science-create-features
translationtype: Human Translation
ms.sourcegitcommit: ba61d00f277af579c87a130336ead9879b82a6de
ms.openlocfilehash: c6b88355df430e78594fc1283c9df01ad6e27e20


---
# <a name="feature-engineering-and-selection-in-azure-machine-learning"></a>Ingénierie et sélection de caractéristiques dans Azure Machine Learning
Cette rubrique explique les finalités de l’ingénierie et de la sélection de caractéristiques dans le processus d’amélioration des données de l’apprentissage automatique. Elle présente les étapes de ces processus à l’aide des exemples fournis par Azure Machine Learning Studio.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Les données d'apprentissage utilisées dans l'apprentissage automatique peuvent souvent être améliorées par la sélection ou l'extraction de caractéristiques à partir des données brutes collectées. Un exemple de conception de caractéristique dans le cadre de l’apprentissage de la classification des images de caractères écrits à la main est la carte de densité de bits construite à partir des données brutes de distribution de bits. Cette carte peut aider à localiser les bords des caractères plus efficacement que la distribution brute.

L’ingénierie et la sélection de caractéristiques augmentent l’efficacité du processus d’apprentissage qui tend à extraire les informations essentielles contenues dans les données. Ces processus améliorent également les performances de ces modèles pour classifier les données d'entrée avec précision et prédire les résultats pertinents de façon plus consistante. L'ingénierie et la sélection de caractéristiques peuvent également être combinées afin de rendre l'apprentissage plus souple d'un point de vue informatique. Cela se fait grâce à l'amélioration puis à la réduction du nombre de caractéristiques nécessaires à l'étalonnage ou l'apprentissage d'un modèle. D'un point de vue mathématique, les caractéristiques sélectionnées pour effectuer l'apprentissage du modèle sont un ensemble minimum de variables indépendantes qui expliquent les modèles des données puis prédisent correctement les résultats.

L'ingénierie et la sélection des caractéristiques constituent une partie d'un processus plus vaste, qui se compose généralement de quatre étapes :

* Collecte des données
* Amélioration des données
* Construction de modèles
* Post-traitement

L’ingénierie et la sélection constituent l’étape d’amélioration des données de l’apprentissage automatique. Trois aspects de ce processus peuvent être distingués relativement à nos objectifs :

* **Prétraitement des données** : ce processus tente de s’assurer que les données collectées sont normales et cohérentes. Ce processus inclut des tâches telles que l’intégration de jeux de données multiples, la gestion des données manquantes, la gestion des données inconsistantes et la conversion des types de données.
* **Ingénierie de caractéristiques** : ce processus tente de créer des caractéristiques supplémentaires pertinentes à partir de caractéristiques brutes existantes dans les données et d’augmenter la performance de prédiction de l’algorithme d’apprentissage.
* **Sélection de caractéristiques** : ce processus sélectionne le sous-ensemble clé des caractéristiques de données d’origine pour réduire la dimensionnalité du problème d’apprentissage.

Cette rubrique traite uniquement des aspects de l’ingénierie et de la sélection de caractéristiques du processus d’amélioration des données. Pour plus d’informations sur l’étape de prétraitement des données, consultez la vidéo [Pre-processing data in Azure Machine Learning Studio](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) (Prétraitement des données dans Azure Machine Learning Studio).

## <a name="creating-features-from-your-data--feature-engineering"></a>Création de caractéristiques à partir de vos données : ingénierie de caractéristiques
Les données d'apprentissage se constituent d'une matrice composée d'exemples (enregistrements ou observations stockées dans les lignes), chaque exemple disposant d'un ensemble de caractéristiques (variables ou champs stockés dans des colonnes). Les caractéristiques spécifiées dans la conception expérimentale sont supposées caractériser les modèles dans les données. Bien que plusieurs champs de données brutes puissent être directement inclus dans l’ensemble des caractéristiques sélectionnées utilisées pour l’apprentissage d’un modèle, des caractéristiques conçues supplémentaires doivent souvent être construites à partir des caractéristiques dans les données brutes pour générer un jeu de données de formation amélioré.

Quelles sont les caractéristiques qui doivent être créées pour améliorer le jeu de données durant l’apprentissage d’un modèle ? Les caractéristiques conçues améliorant l'apprentissage offrent des informations qui permettent de mieux différencier les modèles dans les données. Les nouvelles caractéristiques devraient fournir des informations supplémentaires qui ne sont pas clairement capturées ou facilement visibles dans l’ensemble des caractéristiques existantes ou d’origine, mais ce processus est tout un art. Des décisions réfléchies et productives nécessitent souvent une spécialisation dans le domaine.

En débutant avec Azure Machine Learning, il est plus facile de comprendre correctement le processus avec des exemples fournis dans Machine Learning Studio. Deux exemples sont présentés ici :

* Un exemple de régression ([Prédiction du nombre de locations de vélo](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4)) dans une expérimentation supervisée, où les valeurs cibles sont connues
* un exemple de classification d'exploration de texte utilisant le [hachage de caractéristiques][feature-hashing]

### <a name="example-1-adding-temporal-features-for-a-regression-model"></a>Exemple 1 : ajout de caractéristiques temporelles pour un modèle de régression
Nous allons utiliser l’exemple de « prévision de la demande de vélos » dans Azure Machine Learning Studio afin de démontrer comment concevoir des caractéristiques pour une tâche de régression. L’objectif de cette expérimentation est de prédire la demande de vélos, autrement dit le nombre de locations de vélo pour un mois, un jour ou une heure spécifique. Le **jeu de données de location de vélo UCI** est utilisé en tant que données brutes d’entrée.

Le jeu de données se base sur des données réelles de la société Capital Bikeshare qui gère un réseau de location de vélos à Washington DC aux États-Unis. Ce jeu de données représente le nombre de locations de vélo pour une heure spécifique d’un jour, de 2011 à 2012, et contient 17379 lignes et 17 colonnes. L’ensemble des caractéristiques brutes contient des conditions météorologiques (température, humidité, vitesse du vent) et le type de jour (vacances ou jour de semaine). Le champ à prédire est **cnt**, nombre qui représente les locations de vélo pour une heure spécifique et qui est compris entre 1 et 977.

Pour construire des caractéristiques efficaces dans les données d’apprentissage, quatre modèles de régression sont générés à l’aide du même algorithme, mais avec quatre jeux de données d’apprentissage différents. Les quatre jeux de données représentent les mêmes données d’entrée brutes, mais avec un nombre croissant de jeux de caractéristiques. Ces caractéristiques sont regroupées en quatre catégories :

1. A = caractéristiques météo + vacances + jour de semaine + week-end pour le jour prévu
2. B = nombre de vélos loués au cours de chacune des 12 dernières heures
3. C = nombre de vélos loués au cours de chacun des 12 derniers jours à la même heure
4. D = nombre de vélos loués au cours de chacune des 12 dernières semaines le même jour à la même heure

Excepté l’ensemble de caractéristiques A, qui existe déjà dans les données brutes d’origine, les trois autres ensembles de caractéristiques sont créés via le processus de conception des caractéristiques. L’ensemble de caractéristiques B capture les toutes dernières demandes de vélos. L'ensemble de caractéristiques C capture la demande de vélos pour une heure en particulier. L'ensemble de caractéristiques D capture la demande de vélos pour une heure particulière et un jour de la semaine particulier. Chacun des quatre jeux de données d’apprentissage inclut respectivement les ensembles de caractéristiques A, A + B, A + B + C et A + B + C + D.

Dans l’expérimentation d’Azure Machine Learning, ces quatre jeux de données d’apprentissage sont constitués via quatre branches à partir du jeu de données d’entrée traité au préalable. Sauf pour la branche la plus à gauche, chacune de ces branches contient un module [Exécuter le Script R][execute-r-script], dans lequel un ensemble de caractéristiques dérivées (ensembles de caractéristiques B, C et D) est respectivement construit et ajouté au jeu de données importé. La figure suivante montre le script R utilisé pour créer un ensemble de caractéristiques B dans la deuxième branche de gauche.

![Créer un ensemble de caractéristiques](./media/machine-learning-feature-selection-and-engineering/addFeature-Rscripts.png)

La table suivante résume la comparaison des résultats de performance des quatre modèles. Les caractéristiques A + B + C affichent les meilleurs résultats. Notez que le taux d’erreur diminue quand un ensemble de caractéristiques supplémentaires est inclus dans les données d’apprentissage. Cela confirme l’idée présupposée que les ensembles de caractéristiques B et C fournissent des informations supplémentaires pertinentes pour la tâche de régression. L’ajout de l’ensemble de caractéristiques D semble ne pas fournir une réduction du taux d’erreur supplémentaire.

![Comparer les résultats des performances](./media/machine-learning-feature-selection-and-engineering/result1.png)

### <a name="a-nameexample2a-example-2-creating-features-in-text-mining"></a><a name="example2"></a> Exemple 2 : création de caractéristiques dans l’exploration de texte
L'ingénierie de caractéristiques s'applique largement aux tâches liées à l'exploration de texte, telles que la classification de document et l'analyse de sentiments. Par exemple, quand vous souhaitez classer des documents en plusieurs catégories, l’hypothèse typique est que les mots ou expressions inclus dans une catégorie de document sont moins susceptibles de se produire dans une autre catégorie de document. Autrement dit, la fréquence de la distribution de mots ou d’expressions est capable d’identifier les différentes catégories de document. Dans les applications d’exploration de texte, le processus d’ingénierie des caractéristiques est nécessaire pour créer les caractéristiques impliquant des fréquences de mot ou d’expression, car chaque élément des contenus de textes est généralement utilisé en tant que données d’entrée.

Pour effectuer cette tâche, une technique appelée *hachage de caractéristiques* est appliquée pour transformer efficacement les caractéristiques de texte arbitraires en index. Au lieu d’associer chaque caractéristique de texte (mots ou d’expressions) à un index particulier, cette méthode fonctionne en appliquant une caractéristique de hachage aux caractéristiques et en utilisant directement leurs valeurs de hachage en tant qu’index.

Dans Azure Machine Learning, il existe un module de [hachage de caractéristiques][feature-hashing] qui crée ces caractéristiques de mot ou expression. La figure suivante montre un exemple d’utilisation de ce module. Le jeu de données d’entrée contient deux colonnes : l’évaluation du livre allant de 1 à 5 et le contenu même de la critique. L’objectif de ce module de [hachage de caractéristiques][feature-hashing] est de récupérer de nouvelles caractéristiques qui montrent la fréquence d’occurrence des mots ou expressions correspondants dans cette critique de livre en particulier. Pour utiliser ce module, vous devez effectuer les étapes suivantes :

1. Sélectionnez la colonne qui contient le texte d’entrée (**Col2** pour cet exemple).
2. Définissez le *nombre de bits de hachage* sur 8, ce qui signifie que 2 ^ 8 = 256 caractéristiques sont créées. Le mot ou l’expression est alors haché en 256 index dans tout le texte. Le paramètre *hachage du nombre de bits* est compris entre 1 et 31. Si le paramètre est défini sur un nombre plus élevé, les mots ou expressions sont moins susceptibles d’être hachés sur le même index.
3. Définissez le paramètre *N-grammes* sur 2. Celui-ci permet de récupérer la fréquence d’occurrence d’unigrammes (une caractéristique pour chaque mot) et de bigrammes (une caractéristique pour chaque paire de mots juxtaposés) à partir du texte d’entrée. Le paramètre *N-grammes* est compris entre 0 et 10, ce qui indique le nombre maximum de mots séquentiels à inclure dans une caractéristique.  

![Module hachage de caractéristiques](./media/machine-learning-feature-selection-and-engineering/feature-Hashing1.png)

La figure suivante montre à quoi ressemblent ces nouvelles caractéristiques.

![Exemple de hachage de caractéristiques](./media/machine-learning-feature-selection-and-engineering/feature-Hashing2.png)

## <a name="filtering-features-from-your-data--feature-selection"></a>Filtrage des caractéristiques à partir de vos données : sélection de caractéristiques
La *sélection de caractéristiques* est un processus qui s’applique en général à la construction de jeux de données d’apprentissage pour les tâches de modélisation prédictives telles que les tâches de classification ou de régression. L’objectif est de sélectionner un sous-ensemble des caractéristiques du jeu de données d’origine qui réduit ses dimensions à l’aide d’un ensemble minimal de caractéristiques pour représenter l’écart de quantité maximum dans les données. Les caractéristiques de ce sous-ensemble contiennent les seules caractéristiques à inclure à l’apprentissage du modèle. La sélection de caractéristiques a deux principaux objectifs :

* Elle augmente souvent la précision de classification en éliminant les caractéristiques non pertinentes, redondantes ou fortement liées.
* Elle réduit le nombre de caractéristiques qui rendent le processus d’apprentissage du modèle plus efficace. Cela est particulièrement important pour les apprenants dont l'apprentissage est coûteux tels que les machines à vecteurs de support.

Bien que la sélection des caractéristiques ait pour objet de réduire le nombre de caractéristiques dans le jeu de données utilisé pour l’apprentissage du modèle, celle-ci ne correspond généralement pas au terme de *réduction de la dimensionnalité*. Les méthodes de sélection de caractéristiques extraient un sous-ensemble des caractéristiques d'origine dans les données sans les modifier.  Les méthodes de réduction de la dimensionnalité utilisent l'ingénierie des caractéristiques qui peuvent transformer les caractéristiques d'origine et donc les modifier. Parmi les exemples de méthodes de réduction de la dimensionnalité, on peut noter l’analyse du composant principal, l’analyse canonique des corrélations et la décomposition en valeurs uniques.

L’une des méthodes de sélection de caractéristiques de catégorie largement appliquée dans un contexte supervisé est la sélection de caractéristiques par filtrage. En évaluant la corrélation entre chaque caractéristique et l'attribut cible, ces méthodes appliquent une mesure statistique pour attribuer un score à chaque caractéristique. Les caractéristiques sont ensuite classées suivant le score, que vous pouvez utiliser pour définir le seuil de conservation ou d’élimination d’une caractéristique spécifique. Parmi les exemples de mesures statistiques utilisées dans ces méthodes, on peut noter la corrélation de Pearson, des informations mutuelles et le test de la loi du Khi-deux.

Azure Machine Learning Studio fournit des modules pour la sélection des caractéristiques. Comme indiqué dans la figure suivante, ces modules comprennent une [sélection de caractéristiques basée sur les filtres][filter-based-feature-selection] et une [analyse discriminante linéaire de Fisher][fisher-linear-discriminant-analysis].

![Exemple de sélection de caractéristiques](./media/machine-learning-feature-selection-and-engineering/feature-Selection.png)

Par exemple, utilisez le module de [sélection de caractéristiques par filtrage][filter-based-feature-selection] avec l’exemple d’exploration de texte décrit précédemment. Supposons que vous voulez créer un modèle de régression après avoir créé un ensemble de 256 caractéristiques via le module de [hachage de caractéristiques][feature-hashing] et que la variable de réponse est **Col1** et représente une critique de livre notée de 1 à 5. Définissez **Feature scoring method** (Méthode de notation des caractéristiques) sur **Pearson Correlation** (Corrélation de Pearson), **Target column** (Colonne cible) sur **Col1** et **Number of desired features** (Nombre de caractéristiques souhaitées) sur **50**. Le module de [sélection de caractéristiques basée sur les filtres][filter-based-feature-selection] produit ensuite un jeu de données contenant 50 caractéristiques avec l’attribut cible **Col1**. La figure suivante montre le flux de cette expérimentation et les paramètres d’entrée.

![Exemple de sélection de caractéristiques](./media/machine-learning-feature-selection-and-engineering/feature-Selection1.png)

La figure suivante montre les jeux de données qui en résultent. Chaque caractéristique obtient un score basé sur la corrélation de Pearson entre elle et l’attribut cible **Col1**. Les caractéristiques avec les scores les plus élevés sont conservées.

![Jeux de données avec sélection de caractéristiques par filtrage](./media/machine-learning-feature-selection-and-engineering/feature-Selection2.png)

La figure suivante indique les scores correspondants aux caractéristiques sélectionnées.

![Scores des caractéristiques sélectionnées](./media/machine-learning-feature-selection-and-engineering/feature-Selection3.png)

En appliquant ce module de [sélection de caractéristiques basée sur les filtres][filter-based-feature-selection], 50 des 256 caractéristiques sont sélectionnées, car elles présentent les fonctionnalités corrélées les plus importantes avec la variable cible **Col1** selon la méthode de notation **corrélation de Pearson**.

## <a name="conclusion"></a>Conclusion
L’ingénierie de caractéristiques et la sélection de caractéristiques sont deux étapes couramment effectuées pour préparer les données d’apprentissage durant la création d’un modèle d’apprentissage automatique. En général, l’ingénierie de caractéristiques s’applique d’abord à la génération de caractéristiques supplémentaires. L’étape de sélection de caractéristiques est alors effectuée pour éliminer les caractéristiques inutiles, redondantes ou fortement corrélées.

Il n’est pas toujours nécessaire d’effectuer l’ingénierie de caractéristiques ou la sélection des caractéristiques. Les données que vous avez à disposition ou que vous collectez, l’algorithme que vous choisissez et les objectifs de l’expérimentation déterminent si cela est nécessaire.

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/



<!--HONumber=Dec16_HO2-->


