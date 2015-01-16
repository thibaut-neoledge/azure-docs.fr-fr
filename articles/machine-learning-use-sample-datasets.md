<properties title="Use the sample datasets in Azure Machine Learning Studio" pageTitle="Utilisation des exemples de jeux de données dans Machine Learning Studio | Azure" description="Utilisation des exemples de jeux de données dans Azure Machine Learning Studio" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="garye" />


# Utilisation des exemples de jeux de données dans Azure Machine Learning Studio

ML Studio inclut des exemples de jeux de données à votre disposition. Il s'agit de jeux de données standard d'apprentissage automatique provenant du domaine public. 

Vous pouvez les utiliser dans vos expériences pour explorer différents types de modèles d'analyses d'apprentissage automatique dans ML Studio. 

- Pour afficher la liste des jeux de données standard dans ML Studio, cliquez sur l'onglet **JEUX DE DONNÉES**. Pour chaque jeu de données, vous pouvez obtenir des informations telles que le nom du jeu de données, la personne par laquelle il a été soumis et une rapide description.
 
    Pour trier les jeux de données, cliquez sur un en-tête de colonne. Par exemple, cliquez sur **SOUMIS PAR** pour regrouper tous les exemples de jeux de données fournis par Microsoft Corporation. C'est également un moyen rapide de voir les jeux de données que vous avez importés avec d'autres utilisateurs de votre espace de travail. 

- Pour utiliser un jeu de données dans une expérience, développez la section **Jeux de données enregistrés** de la palette de modules à gauche du canevas d'expérience, ou recherchez un jeu de données spécifique en tapant son nom dans la zone de recherche au-dessus de la palette. Faites glisser le jeu de données sur le canevas pour l'ajouter à votre expérience. 

##Exemples de jeux de données

Le tableau suivant fournit des informations supplémentaires sur les jeux de données, qui peuvent vous servir pour explorer les fonctionnalités d'apprentissage automatique de ML Studio. Pour chaque jeu de données, le tableau indique :
 
- le nom et la source du jeu de données ;
- les utilisations possibles du jeu de données et les recherches d'apprentissage automatique qui ont utilisé le jeu de données ;
- une synthèse des colonnes importantes comprises dans le jeu de données, ainsi que d'autres attributs utiles.

Certaines descriptions sont adaptées de la documentation fournie par la source (en général [UC Irvine Machine Learning Repository](http://archive.ics.uci.edu/ml/ "UC Irvine Machine Learning Repository")) et d'autres sont basées sur une recherche supplémentaire ou reflètent les changements apportés dans ces exemples.

<table>
<tr valign=top>
<th>Nom du jeu de données</th>
<th>Description du jeu de données</th>
<th>Recherche associée</th>
</tr>
<tr valign=top>
<td>Jeu de données Adult Census Income Binary Classification</td>
<td>
<p>Sous-ensemble de la base de données Census de 1994, qui recense les adultes de plus de 16 ans en activité avec un index des revenus ajustés supérieur à 100.</p>
<p><strong>Usage                 :</strong> Effectuer un classement des personnes en utilisant des données démographiques pour prédire si une personne gagne plus de 50 000 par an.</p>
</td>
<td>
<p>Kohavi, R., Becker, B., (1996). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA : Université de Californie, School of Information and Computer Science</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Données sur le prix des véhicules automobiles (brutes)</p>
</td>
<td>
<p>Informations sur les véhicules automobiles par marque et modèle, incluant le prix, des caractéristiques telles que le nombre de cylindres et de litres au 100, et une note de risque d'assurance.</p>
<p>La note de risque est initialement associée au prix du véhicule, puis ajustée pour le risque réel au cours d'une procédure communément appelée <legacyItalic>symbolisation</legacyItalic> par les actuaires. La valeur +3 indique que le véhicule est à risque, et la valeur -3 qu'il est plutôt sûr.</p>
<p><strong>Usage                 :</strong> Prédire la note de risque en fonction des caractéristiques, en utilisant une classification de régression ou multicritère.</p>
</td>
<td>
<p>
Schlimmer, J.C. (1987). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA : Université de Californie, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Données sur le don de sang</p>
</td>
<td>
<p>Sous-ensemble de la base de données des donneurs de sang du centre de transfusion sanguine de Hsin-Chu City à Taiwan.</p>
  <p>Les données associées aux donneurs incluent le nombre de mois depuis le dernier don et la fréquence, ou le nombre total de dons, la durée écoulée depuis le dernier don et la quantité de sang donnée.</p>
  <p><strong>Usage                 :</strong> L'objectif est de prédire par la classification si le donneur a donné du sang en mars 2007, 1 indiquant un donneur pendant la période ciblée et 0 pas de donneur.</p>
</td>
<td>
<list class="bullet">
<listItem>
<p>
Yeh, I.C., (2008). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA : Université de Californie, School of Information and Computer Science
</p>
</listItem>
<listItem>
<p>
Yeh, I-Cheng, Yang, King-Jang et Ting, Tao-Ming, " Knowledge discovery on RFM model using Bernoulli sequence ", Expert Systems with Applications, 2008, [<a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>]
</p>
</listItem>
</list>
</td>
</tr>

<tr valign=top>
<td>
<p>Données sur le cancer du sein</p>
</td>
<td>
<p>Un des trois jeux de données sur le cancer fournis par l'Oncology Institute, fréquemment cité dans la littérature sur l'apprentissage automatique. Combine des informations de diagnostic et des caractéristiques d'analyse de laboratoire sur environ 300 échantillons de tissu.</p>
<p><strong>Usage                 :</strong> Classifier le type de cancer en se basant sur 9 attributs, certains étant linéaires, d'autres de catégorie.</p>
</td>
<td>
<p>
Wohlberg, W.H., Street, W.N., &amp; Mangasarian, O.L. (1995). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA : Université de Californie, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Données sur la régression de l'efficacité énergétique</p>
</td>
<td>
<p>Collection de profils d'énergie simulés, basée sur 12 formes différentes de bâtiments. Les bâtiments diffèrent sur 8 caractéristiques, comme la zone de vitrage, la distribution des zones de vitrage et l'orientation.</p>
<p><strong>Usage                 :</strong> Utiliser la régression ou la classification pour prédire la notation de l'efficacité énergétique sous la forme de l'une des deux réponses en valeur réelle. Pour la classification multiclasse, arrondit la variable de la réponse à l'entier le plus proche.</p>
</td>
<td>
<p>
Xifara, A. &amp; Tsanas, A. (2012). UCI Machine Learning Repository   [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA : Université de Californie, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Performance concernant les vols à l'heure (brutes)</p>
</td>
<td>
<p>Enregistrements des arrivées et départs de vols aux États-Unis à compter d'octobre 2011.</p>
<p><strong>Usage                 :</strong> Prédire les retards des vols.</p>
</td>
<td>
<p>
Département des transports des États-Unis [<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&amp;DB_Short_Name=On-Time</a>]
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Données sur les feux de forêt</p>
</td>
<td>
<p>Contient des données sur la météo, comme les indices de température et d'humidité et la vitesse du vent, pour une zone du nord-est du Portugal, combinées aux enregistrements des feux de forêt.</p>
<p><strong>Usage                 :</strong> Il s'agit d'une tâche difficile de régression dont l'objectif est de prédire la zone brûlée par des feux de forêt.</p>
</td>
<td>
<list class="bullet">
<listItem>
<p>
Cortez, P., &amp; Morais, A. (2008). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA : Université de Californie, School of Information and Computer Science
</p>
</listItem>
<listItem>
<p>
[Cortez et Morais, 2007] P. Cortez et A. Morais. 
A Data Mining Approach to Predict Forest Fires using Meteorological Data. 
In J. Neves, M. F. Santos et J. Machado Eds., New Trends in Artificial Intelligence, 
Actes de procédures de la 13e conférence portugaise en intelligence artificelle (EPIA 2007), 
December, Guimar&#227;es, Portugal, pp. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Disponible à l'adresse :
[<a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>]
</p>
</listItem>
</list>
</td>
</tr>

<tr valign=top>
<td>
<p>Données sur deux classes d'iris</p>
</td>
<td>
<p>Il s'agit sans doute de l'une des bases de données les plus réputées dans la littérature de la reconnaissance des formes. Le jeu de données est relativement petit, avec 50 exemples de mesures de pétales provenant de trois variétés d'iris.</p>
<p><strong>Usage                 :</strong> Prédire le type d'iris à partir des mesures. 
<!-- I believe the following doesn't apply anymore, but I'm not sure so I'll remove it for now.
One class is linearly separable from the other two; but the latter are not linearly separable from each other.
-->
</p>
</td>
<td>
<p>
Fisher, R.A. (1988). UCI Machine Learning Repository   [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA : Université de Californie, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Données sur la quantité de litres au 100 pour différents véhicules automobiles</p>
</td>
<td>
<p>Ce jeu de données diffère légèrement de la version fournie par la bibliothèque StatLib de l'université de Carnegie Mellon. Le jeu de données a été utilisé lors de l'exposition de l'American Statistical Association en 1983.</p>
<p>Les données répertorient la consommation d'essence de différents véhicules automobiles en miles par gallon, et des informations comme le nombre de cylindres, la cylindrée du moteur, le cheval-puissance, le poids total et l'accélération.</p>
<p><strong>Usage                 :</strong> Prédire l'économie d'essence en se basant sur 3 attributs discrets multivalents et 5 attributs continus.</p>
</td>
<td>
<p>
StatLib, Carnegie Mellon University, (1993). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA : Université de Californie, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Jeu de données sur la classification binaire du diabète chez les indiens Pima</p>
</td>
<td>
<p>Sous-ensemble de la base de données du National Institute of Diabetes and Digestive and Kidney Diseases. Le jeu de données a été filtré pour s'intéresser aux patientes féminines des descendants des indiens Pima. Les données incluent des données médicales comme les taux de glucose et d'insuline, et des facteurs liés au style de vie.</p>
<p><strong>Usage                 :</strong> Prédire si le sujet a du diabète (classification binaire).</p>
</td>
<td>
<p>
Sigillito, V. (1990). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA : Université de Californie, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Données sur les clients de restaurant</p>
</td>
<td>
<p>Jeu de données sur les clients, comprenant des données démographiques et des préférences.</p>
<p><strong>Usage                 :</strong> Utilisez ce jeu de données en combinaison avec les deux autres jeux de données sur les restaurants, pour former et tester un système de recommandation.</p>
</td>
<td>
<p>
Bache, K. et Lichman, M. (2013). UCI Machine Learning Repository 
[<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>].
Irvine, CA : Université de Californie, School of Information and Computer Science.
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Données sur les caractéristiques de restaurants</p>
</td>
<td>
<p>Jeu de métadonnées sur des restaurants et leurs caractéristiques, comme le type de gastronomie, le style de lieu et l'emplacement.</p>
<p><strong>Usage                 :</strong> Utilisez ce jeu de données en combinaison avec les deux autres jeux de données sur les restaurants, pour former et tester un système de recommandation.</p>
</td>
<td>
<p>
Bache, K. et Lichman, M. (2013). UCI Machine Learning Repository 
[<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>].
Irvine, CA : Université de Californie, School of Information and Computer Science.
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Notations de restaurants</p>
</td>
<td>
<p>Contient les notes attribuées par les consommateurs à des restaurants sur une échelle de 0 à 2.</p>
<p><strong>Usage                 :</strong> Utilisez ce jeu de données en combinaison avec les deux autres jeux de données sur les restaurants, pour former et tester un système de recommandation.</p>
</td>
<td>
<p>
Bache, K. et Lichman, M. (2013). UCI Machine Learning Repository 
[<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>].
Irvine, CA : Université de Californie, School of Information and Computer Science.
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Jeu de données multiclasse de recuit d'acier</p>
</td>
<td>
<p>Ce jeu de données contient un ensemble d'enregistrements d'essais de recuits d'acier avec les attributs physiques (largeur, épaisseur, type - rouleau, feuille, etc.) des résultats des types d'acier.</p>
<p><strong>Usage                 :</strong> Prédire un attribut de classe numérique sur deux, robustesse ou force. Vous pouvez également analyser les corrélations entre attributs.</p>
<p>Les qualités d'acier répondent à un standard défini par la SAE et d'autres organisations. Vous recherchez une qualité d'acier spécifique (variable de classe) et vous cherchez à comprendre les valeurs nécessaires.</p>
</td>
<td>
<p>
Sterling, D. &amp; Buntine, W., (NA). UCI Machine Learning Repository   [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA : Université de Californie, School of Information and Computer Science
</p>
<p>Guide pratique sur les qualités d'acier :</p>
<p>[<a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf>]
  </p>
</td>
</tr>

<tr valign=top>
<td>
<p>Données sur les télescopes</p>
</td>
<td>
<p>Enregistrements sur les sursauts de particules gamma de haute énergie et le bruit de fond, à partir de simulations Monte Carlo.</p>
<p>L'objectif de la simulation était d'améliorer la précision des télescopes gamma à effet Cherenkov atmosphérique basés sur sol en utilisant des méthodes statistiques pour discriminer le signal souhaité (douches de radiation Cherenkov) du bruit de fond (douches hadroniques dues à des rayons cosmiques dans la haute atmosphère).</p>
<p>Les données ont été prétraitées pour créer un cluster allongé avec l'axe long orienté vers le centre de la caméra. Les caractéristiques de cette ellipse (souvent appelée paramètres de Hillas) font partie des paramètres d'image qui peuvent servir pour la discrimination.</p>
<p><strong>Usage                 :</strong> Prédire si l'image d'une douche représente le signal ou le bruit de fond.</p>
<p><strong>Remarques :</strong> La simple précision de la classification n'est pas significative pour ces données, car classifier un événement de bruit de fond comme événement de signal est pire que classifier un événement de signal comme événement de bruit de fond. Pour comparer différents classifieurs, il faut utiliser le graphe ROC. La probabilité d'accepter un événement de bruit de fond comme signal doit être sous l'un des seuils suivants : 0,01, 0,02, 0,05, 0,1 ou 0,2.</p>
<p>Il faut également noter que le nombre d'événements de bruits de fond (" h " pour douches hadroniques) est sous-estimé, car dans les mesures réelles la classe h ou de bruits représente la majorité des événements.</p>
</td>
<td>
<p>
Bock, R.K. (1995). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA : Université de Californie, School of Information and Computer Science
</p>
</td>
</tr>

</table>

Les types de jeux de données possibles sont répertoriés ci-dessous.

<table>
<tr>
<th>Format de données</th>
<th>Description</th>
</tr>
<tr>
<td>
Valeurs séparées par une virgule (CSV)
</td>
<td>
Format d'échange connu pour toutes les plateformes de données. ML Studio charge ces données et intègre un devin de type colonne qui applique des métadonnées basées sur des échantillons de données de chaque colonne.
</td>
</tr>
<tr>
<td>
Format ARFF (Attribute Relationship File Format)
</td>
<td>
Format de données Machine Learning défini par WEKA. Contient des métadonnées (pour les types nominal, numérique et de chaîne) converties directement en tables de données ML Studio.
</td>
</tr>
<tr>
<td>
Texte brut
</td>
<td>
Le texte brut peut être lu, puis divisé en colonnes avec l'aide de modules de prétraitement en aval. Le format de texte brut généralement pris en charge est JSON.
</td>
</tr>
<tr>
<td>
DotNetTable
</td>
<td>
Version sérialisée du premier conteneur passé entre les modules dans ML Studio (le <i>jeu de données</i>).
</td>
</tr>
</table>
