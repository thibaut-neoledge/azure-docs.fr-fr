<properties 
	pageTitle="Service de recherche : workflow pour les développeurs" 
	description="Service de recherche : workflow pour les développeurs" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="01/16/2015" 
	ms.author="heidist"/>

# Azure Search : flux de travail de développement

[WACOM.INCLUDE [This article uses the Azure Preview portal](../includes/preview-portal-note.md)]

Cet article propose une feuille de route et quelques pratiques recommandées pour créer et gérer le service de recherche et les index associés. 

Nous partons du principe que vous avez déjà approvisionné le service. Si vous ne l'avez pas encore fait, consultez la section [Prise en main d'Azure Search](search-get-started.md) pour en savoir plus.

+ [Étape 1 : Création de l'index](#sub-1)
+ [Étape 2 : Ajout de documents](#sub-2)
+ [Étape 3 : Interrogation d'un index](#sub-3)
+ [Étape 4 : Mise à jour ou suppression d'index et de documents](#sub-4)
+ [Considérations relatives à la conception du stockage](#sub-5)


<h2 id="sub-1">Étape 1 : Création de l'index</h2>

Les requêtes ciblent un index de recherche qui contient des attributs et des données de recherche. Par conséquent, votre première étape après l'approvisionnement du service est de définir le schéma d'index au format JSON et d'exécuter une requête HTTPS PUT pour créer l'index dans le service. 

Les index sont construits par votre code d'application. Il n'existe aucun outil ou éditeur intégré pour vous aider à définir un index dans une interface utilisateur. Les exemples qui illustrent les divers moyens de construire un index se trouvent dans les sections [Créer votre première solution de recherche à l'aide d'Azure Search](search-create-first-solution.md), dans laquelle le schéma est indiqué dans le fichier Program.cs, et [Prise en main des profils de score dans Azure Search](search-get-started-scoring-profiles.md) qui fournit l'index dans un fichier de schéma autonome au format JSON. Pour plus d'informations sur la création d'index, consultez la section [Création d'un index (API Azure Search)](http://msdn.microsoft.com/library/dn798941.aspx) sur MSDN.

<h2 id="sub-2">Étape 2 : Ajout de documents</h2>

Une fois l'index de recherche créé, vous pouvez y ajouter des documents en les soumettant sous la forme de demandes POST au format JSON. Chaque document doit être associé à une clé unique et à un ensemble de champs contenant des données pouvant faire l'objet d'une recherche et d'autres non. Les données de document sont représentées sous la forme d'un ensemble de paires clé/valeur pour chaque champ.

Il est conseillé d'ajouter des documents par lots afin d'améliorer le débit. Vous pouvez créer des lots contenant jusqu'à 1 000 documents, en supposant que leur taille moyenne est d'environ 1 à 2 Ko.

Il existe un code d'état global pour la demande POST. Les codes d'état sont soit HTTP 200 (Réussite) soit HTTP 207 (Multi-état) s'il y a un mélange de documents ayant abouti et d'autres ayant échoué. Outre le code d'état de la demande POST, Azure Search gère un champ d'état pour chaque document. Dans le cas d'un téléchargement par lots, vous devez disposer d'une méthode permettant d'obtenir un état qui indique, pour chaque document, si l'insertion s'est déroulée correctement ou a échoué. Le champ d'état fournit cette information. La valeur est définie sur false si le chargement du document a échoué.

Dans des conditions de surcharge, les échecs de téléchargement ne sont pas rares. Si cela se produit, le code d'état global est 207, ce qui indique une réussite partielle, et la propriété Status des documents dont l'indexation a échoué est définie sur false.

> [WACOM.NOTE] Lorsque le service reçoit des documents, ceux-ci sont placés en file d'attente en vue de l'indexation. Il se peut qu'ils ne soient pas inclus immédiatement dans les résultats de la recherche. Dans des conditions de charge normales, l'indexation des documents prend généralement quelques secondes.


<h2 id="sub-3">Étape 3 : Interrogation d'un index</h2>

Une fois les documents indexés, vous pouvez exécuter des requêtes de recherche. Vous pouvez interroger un index à la fois, en utilisant soit OData, soit une syntaxe de requête simple :

+	[Syntaxe d'expression OData pour Azure Search](http://msdn.microsoft.com/library/dn798921.aspx)
+	[Syntaxe de requête simple dans Azure Search](http://msdn.microsoft.com/library/dn798920.aspx)

<h2 id="sub-4">Étape 4 : Mise à jour ou suppression d'index et de documents</h2>

Vous pouvez éventuellement apporter des modifications de schéma à l'index de recherche, mettre à jour ou supprimer des documents dans l'index, ou encore supprimer des index.

Lors de la mise à jour d'un index, vous pouvez combiner plusieurs actions (insertion, fusion, suppression) au sein d'un même lot, éliminant ainsi les allers-retours. Pour l'heure, Azure Search ne prend pas en charge les mises à jour partielles (HTTP PATCH). Si vous devez mettre à jour un index, vous devez donc renvoyer sa définition.

<h2 id="sub-5">Considérations relatives à la conception du stockage</h2>

Azure Search utilise le stockage interne pour les index et les documents utilisés dans les opérations de recherche. L'analyse de texte et de l'index dépend de la disponibilité rapide de tous les champs pouvant faire l'objet d'une recherche et des attributs associés.

Tous les champs d'un document ne peuvent pas faire l'objet d'une recherche. Par exemple, si votre application est un catalogue en ligne de musique ou de vidéos, il est conseillé de stocker les fichiers binaires dans Azure BLOB service ou sous une autre forme de stockage. Les fichiers binaires ne pouvant pas faire l'objet d'une recherche, il n'est pas nécessaire de les conserver dans l'espace de stockage Azure Search. Même si vous devez stocker des images, des vidéos et des fichiers audio dans d'autres services ou emplacements, vous devez inclure un champ indiquant l'URL de l'emplacement du fichier. De cette façon, vous pouvez renvoyer les données externes dans le cadre de vos résultats de recherche. 

Pour plus d'informations sur la création d'index ou de documents, consultez la section [API Rest Azure Search](http://msdn.microsoft.com/library/dn798935.aspx).


<!--Anchors-->
[Étape 1 : Création de l'index]: #sub-1
[Étape 2 : Ajout de documents]: #sub-2
[Étape 3 : Interrogation d'un index]: #sub-3
[Étape 4 : Mise à jour ou suppression d'index et de documents]: #sub-4
[Choix d'une banque de documents]: #sub-5


<!--Image references-->

<!--Link references-->
[Prise en main d'Azure Search]: ../search-get-started/
[Gestion de votre service de recherche sur Microsoft Azure]: ../search-manage/
[Création de votre première solution de recherche à l'aide du service Azure Search]: ../search-create-first-solution/


<!--HONumber=46--> 
