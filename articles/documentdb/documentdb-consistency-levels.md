<properties
	pageTitle="Niveaux de cohérence dans DocumentDB | Microsoft Azure"
	description="DocumentDB offre quatre niveaux de cohérence qui permettent de faire des compromis avisés entre cohérence éventuelle, disponibilité et latence."
	keywords="cohérence éventuelle, documentdb, azure, Microsoft azure"
	services="documentdb"
	authors="mimig1"
	manager="jhubbard"
	editor="cgronlun"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2016"
	ms.author="mimig"/>

# Niveaux de cohérence dans DocumentDB

Azure DocumentDB a été conçu dès le départ pour être distribué à l’échelle mondiale. Il offre des garanties de latence faible prévisible, un SLA de disponibilité à 99,99 % et plusieurs modèles de cohérence souples bien définis. Pour le moment, DocumentDB prend en charge quatre niveaux de cohérence : Fort, Obsolescence limitée, Session et Éventuel. Outre les modèles de cohérence **fort** et **éventuel** souvent offerts par d’autres bases de données NoSQL, DocumentDB propose deux modèles de cohérence soigneusement codifiés et mis en œuvre (**obsolescence limitée** et **session**) dont l’utilité a été validée dans des conditions d’utilisation réelles. Ensemble, ces quatre niveaux de cohérence vous permettent de trouver un bon compromis entre cohérence, disponibilité et latence.

## Portée de la cohérence

La granularité de la cohérence est limitée à la demande d’un utilisateur unique. Une demande d’écriture peut correspondre à une transaction d’insertion, de remplacement, d’upsert ou de suppression (avec ou sans l’exécution d’un déclencheur préalable ou postérieur associé). Ou bien une demande d’écriture peut correspondre à l’exécution transactionnelle d’une procédure stockée JavaScript via plusieurs documents au sein d’une partition. Une transaction de lecture/requête est limitée à une demande d’utilisateur unique, comme c’est le cas des écritures. L’utilisateur peut être amené à paginer un grand jeu de résultats, s’étendant sur plusieurs partitions, mais chaque transaction de lecture se limite à une seule page et est traitée à partir d’une seule partition.

## Niveaux de cohérence

Vous pouvez configurer le niveau de cohérence par défaut sur le compte de base de données qui s'applique à toutes les collections (parmi l'ensemble des bases de données) sous votre compte de base de données. Par défaut, toutes les lectures et requêtes émises vers les ressources définies par l'utilisateur utilisent le niveau de cohérence par défaut spécifié sur le compte de base de données. Vous pouvez néanmoins assouplir le niveau de cohérence d’une demande de lecture/requête donnée en spécifiant l’en-tête de demande [[x-ms-consistency-level]](https://msdn.microsoft.com/library/azure/mt632096.aspx). Quatre types de niveaux de cohérence sont pris en charge par le protocole de réplication de DocumentDB. Ils fournissent un compromis clair entre les garanties de cohérence spécifiques et les performances, comme décrit ci-dessous.

![DocumentDB offre le choix entre plusieurs modèles de cohérence bien définis (souples)][1]

**Remarque** :

- La cohérence forte offre une garantie de [linéarisabilité](https://aphyr.com/posts/313-strong-consistency-models) qui permet de s’assurer que les lectures renvoient la version la plus récente d’un document.
- la cohérence forte garantit qu'une écriture est visible uniquement après sa validation durable par le quorum majoritaire de réplicas. Une écriture est soit validée durablement de manière synchrone par les quorums principal et secondaire, soit abandonnée. Une lecture est toujours reconnue par le quorum de lecture majoritaire : un client ne voit jamais une écriture partielle ou non validée. Il est assuré de lire la toute dernière écriture reconnue.
- Les comptes DocumentDB configurés pour utiliser une cohérence forte ne peuvent pas associer plus d’une région Azure avec leur compte DocumentDB.
- Le coût d’une opération de lecture (en termes [d’unités de requête](documentdb-request-units.md) consommées) avec une cohérence forte est supérieur à celui des niveaux Session et Éventuel, mais équivalent à celui du niveau Obsolescence limitée.
 

**Obsolescence limitée** :

- La cohérence Obsolescence limitée garantit que les lectures sont retardées derrière les écritures par, au plus, des versions ou préfixes *K* d’un document ou un intervalle de temps *t*.
- Par conséquent, lors de la sélection de la cohérence Obsolescence limitée, « l’obsolescence » peut être configurée de deux manières :
    - Nombre de versions *K* du document retardant les lectures derrière les écritures
    - Intervalle de temps *t*
- La cohérence Obsolescence limitée fournit l’ordre global total, en dehors de la « fenêtre d’obsolescence ». Notez que les garanties de lecture unitone existent dans une région à l’intérieur et en dehors de la « fenêtre d’obsolescence ».
- La cohérence Obsolescence limitée fournit une meilleure garantie de cohérence que le niveau Session ou Éventuel. Pour les applications distribuées à l’échelle mondiale, nous recommandons d’utiliser la cohérence Obsolescence limitée pour les scénarios dans lesquels vous voulez obtenir une cohérence forte en plus d’une disponibilité à 99,99 % et d’une latence faible.
- Les comptes DocumentDB configurés avec une cohérence Obsolescence limitée peuvent associer n’importe quel nombre de régions Azure avec leur compte DocumentDB.
- Le coût d’une opération de lecture (en termes d’unités de requête consommées) en fonction de l’obsolescence limitée est supérieur à celui des niveaux de cohérence Session et Éventuel, mais identique au niveau de cohérence forte.

**Session** :

- contrairement aux modèles de cohérence globaux offerts par les niveaux de cohérence Fort et Obsolescence limitée, le niveau Session s’étend à une session client spécifique.
- La cohérence Session est idéale pour tous les scénarios dans lesquels une session utilisateur ou d’appareil est impliquée, car elle garantit des lectures unitones, des écritures unitones et des garanties de lecture de vos propres écritures.
- Ce niveau fournit une cohérence prévisible pour une session, et un débit de lecture maximal en offrant la latence d’écriture et de lecture la plus basse.
- Les comptes DocumentDB configurés avec une cohérence Session peuvent associer n’importe quel nombre de régions Azure avec leur compte DocumentDB.
- Le coût d’une opération de lecture (en termes d’unités de requête consommées) avec un niveau de cohérence Session est inférieur à celui des niveaux Fort et Obsolescence limitée, mais supérieur au niveau Éventuel.
 

**Eventual (Éventuel)** :

- Le niveau de cohérence Éventuel garantit qu’en l’absence d’autres écritures, les réplicas du groupe finiront par converger.
- Il s’agit de la forme de cohérence la plus faible qui permet à un client d’obtenir des valeurs plus anciennes que celles qu’il a pu voir précédemment.
- Le niveau Éventuel fournit la cohérence la plus faible en matière de lecture, tout en offrant la latence la moins élevée pour les lectures et les écritures.
- Les comptes DocumentDB configurés avec un niveau de cohérence Éventuel peuvent associer n’importe quel nombre de régions Azure avec leur compte DocumentDB.
- Le coût d’une opération de lecture (en termes d’unités de requête consommées) avec un niveau de cohérence Éventuel est le plus bas de tous les niveaux de cohérence DocumentDB.


## Garanties de cohérence

Le tableau suivant répertorie les différentes garanties de cohérence correspondant aux quatre niveaux de cohérence.

| Garantie | Strong (Fort) | Bounded Staleness (En fonction de l'obsolescence) | Session | Eventual (Éventuel) |
|----------------------------------------------------------|-------------------------------------------------|------------------------------------------------------------------------------------------------|--------------------------------------------------|--------------------------------------------------|
| **Ordre global total** | Oui | Oui, en dehors de la « durée d’obsolescence » | Non, ordre partiel par « session » | Non |
| **Garantie de préfixe cohérent** | Oui | Oui | Oui | Oui |
| **Lectures unitones** | Oui | Oui, entre les régions en dehors de la durée d’obsolescence et au sein d’une région à tout moment. | Oui, pour la session donnée | Non |
| **Écritures unitones** | Oui | Oui | Oui | Oui |
| **Lire vos écritures** | Oui | Oui | Oui (dans la région d’écriture) | Non |


## Configuration du niveau de cohérence par défaut

1.  Dans la barre de lancement du [portail Azure](https://portal.azure.com/), cliquez sur **Comptes DocumentDB**.

2. Dans le panneau **Comptes DocumentDB**, sélectionnez le compte de base de données à modifier.

3. Dans le panneau du compte, cliquez sur **Cohérence par défaut**.


4. Dans le panneau **Cohérence par défaut**, sélectionnez le nouveau niveau de cohérence et cliquez sur **OK**.

	![Capture d’écran montrant l’icône Paramètres et l’entrée Cohérence par défaut](./media/documentdb-consistency-levels/database-consistency-level-1.png)

## Niveaux de cohérence des requêtes

Par défaut, pour les ressources définies par l’utilisateur, le niveau de cohérence des requêtes est identique à celui des lectures. Par défaut, l'index est mis à jour de manière synchrone lors de chaque insertion, remplacement ou suppression d'un document au niveau de la collection. Cela permet aux requêtes de fournir le même niveau de cohérence que celui des lectures de document. Alors que DocumentDB est optimisé pour les écritures et prend en charge des volumes soutenus d’écritures de documents, la maintenance d’index synchrone et les requêtes cohérentes, vous pouvez configurer certaines collections de manière à ce que la mise à jour de l’index soit effectuée en différé. Ce processus permet d'optimiser encore plus les performances des écritures. Il est idéal pour les scénarios d'ingestion en bloc lorsqu'une charge de travail implique principalement des lectures.

Mode d'indexation|	Lectures|	Requêtes  
-------------|-------|---------
Cohérence (par défaut)|	Choisir parmi Fort, Obsolescence limitée, Session et Éventuel|	Choisir parmi Fort, Obsolescence limitée, Session et Éventuel|
Différé|	Choisir parmi Fort, Obsolescence limitée, Session et Éventuel|	Eventual (Éventuel)  

À l’instar des demandes de lecture, vous pouvez réduire le niveau de cohérence d’une demande de requête donnée en spécifiant l’en-tête de demande [x-ms-consistency-level](https://msdn.microsoft.com/library/azure/mt632096.aspx).

## Étapes suivantes

Si vous souhaitez en lire plus sur les niveaux de cohérence et les différents compromis, nous vous recommandons les ressources suivantes :

-	Doug Terry. La cohérence des données répliquées expliquée par le baseball (vidéo). [https://www.youtube.com/watch?v=gluIh8zd26I](https://www.youtube.com/watch?v=gluIh8zd26I)
-	Doug Terry. La cohérence des données répliquées basée sur l’exemple du baseball. [http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
-	Doug Terry. Le niveau Par session garantit des données répliquées peu cohérentes. [http://dl.acm.org/citation.cfm?id=383631](http://dl.acm.org/citation.cfm?id=383631)
-	Daniel Abadi. Cohérence des compromis en termes de conception de systèmes de base de données distribuée moderne : CAP n’est qu’une partie de l’histoire. [http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html)
-	Peter Bailis, Shivaram Venkataraman, Michael J. Franklin, Joseph M. Hellerstein, Ion Stoica. Probabilités en fonction de l’obsolescence (PBS) pour les quorums partiels pratiques. [http://vldb.org/pvldb/vol5/p776\_peterbailis\_vldb2012.pdf](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
-	Werner Vogels. Niveau de cohérence Éventuel repensé. [http://allthingsdistributed.com/2008/12/eventually\_consistent.html](http://allthingsdistributed.com/2008/12/eventually_consistent.html)


[1]: ./media/documentdb-consistency-levels/consistency-tradeoffs.png

<!---HONumber=AcomDC_0817_2016-->