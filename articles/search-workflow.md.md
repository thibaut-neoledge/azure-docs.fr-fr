<properties title="Search Service: workflow for developers" pageTitle="Search Service: workflow for developers" description="Search Service: workflow for developers" metaKeywords="" services="" solutions="" documentationCenter="" authors="heidist" videoId="" scriptId="" />

# Azure Search : workflow de développement

[WACOM.INCLUDE [Cet article utilise la version préliminaire du portail Azure](../includes/preview-portal-note.md)]

Cet article propose une feuille de route et quelques pratiques recommandées pour créer et gérer le service de recherche et les index associés.

Nous partons du principe que vous avez déjà approvisionné le service. Si tel n'est pas le cas, consultez la rubrique [Configuration de Search dans la version préliminaire du portail Azure]() pour commencer.

-   [Étape 1 : Création de l'index](#sub-1)
-   [Étape 2 : Ajout de documents](#sub-2)
-   [Étape 3 : Interrogation d'un index](#sub-3)
-   [Étape 4 : Mise à jour ou suppression d'index et de documents](#sub-4)
-   [Considérations relatives à la conception du stockage](#sub-5)

<h2 id="sub-1">Étape 1 : Création de l'index</h2>

Les requêtes (du moins les requêtes autres que celles du système) ciblent un index de recherche contenant des attributs et des données de recherche. Au cours de cette étape, vous allez définir le schéma d'index au format JSON et exécuter une demande HTTPS PUT pour que cet index soit créé dans le service.

Les index sont généralement codés dans votre environnement de développement local. Il n'existe aucun éditeur ou outil intégré pour la définition d'index. Pour plus d'informations sur la création d'index, consultez la page [Création d'un index (API Azure Search)](http://msdn.microsoft.com/en-us/library/dn798941.aspx) sur MSDN.

<h2 id="sub-2">Étape 2 : Ajout de documents</h2>

Une fois l'index de recherche créé, vous pouvez y ajouter des documents en les soumettant sous la forme de demandes POST au format JSON. Chaque document doit être associé à une clé unique et à un ensemble de champs contenant des données pouvant faire l'objet d'une recherche et d'autres non. Les données de document sont représentées sous la forme d'un ensemble de paires clé/valeur.

Il est conseillé d'ajouter des documents par lots afin d'améliorer le débit. Vous pouvez créer des lots contenant jusqu'à 10 000 documents, en supposant une taille de document moyenne d'environ 1 à 2 Ko.

Il existe un code d'état global pour la demande POST. Les codes d'état sont soit HTTP 200 (Réussite) soit HTTP 207 (Multi-état) s'il y a un mélange de documents ayant abouti et d'autres ayant échoué. Outre le code d'état de la demande POST, Azure Search gère un champ d'état pour chaque document. Dans le cas d'un téléchargement par lots, vous devez disposer d'une méthode permettant d'obtenir un état qui indique, pour chaque document, si l'insertion s'est déroulée correctement ou a échoué. Le champ d'état fournit cette information. La valeur est définie sur false si le chargement du document a échoué.

Dans des conditions de surcharge, les échecs de téléchargement ne sont pas rares. Si cela se produit, le code d'état global est 207, ce qui indique une réussite partielle, et la propriété Status des documents dont l'indexation a échoué est définie sur false.

> [WACOM.NOTE] Lorsque le service reçoit des documents, ceux-ci sont placés en file d'attente en vue de l'indexation. Il se peut qu'ils ne soient pas inclus immédiatement dans les résultats de la recherche. Dans des conditions de charge normales, l'indexation des documents prend généralement quelques secondes.

<h2 id="sub-3">Étape 3 : Interrogation d'un index</h2>

Une fois les documents indexés, vous pouvez exécuter des requêtes de recherche. Vous pouvez interroger un index à la fois, en utilisant soit OData, soit une syntaxe de requête simple :

-   [Syntaxe d'expression OData pour Azure Search](http://msdn.microsoft.com/en-us/library/dn798921.aspx)
-   [Syntaxe de requête simple dans Azure Search](http://msdn.microsoft.com/en-us/library/dn798920.aspx)

<h2 id="sub-4">Étape 4 : Mise à jour ou suppression d'index et de documents</h2>

Vous pouvez éventuellement apporter des modifications de schéma à l'index de recherche, mettre à jour ou supprimer des documents dans l'index, ou encore supprimer des index.

Lors de la mise à jour d'un index, vous pouvez combiner plusieurs actions (insertion, fusion, suppression) au sein d'un même lot, éliminant ainsi les allers-retours. Pour l'heure, Azure Search ne prend pas en charge les mises à jour partielles (HTTP PATCH). Si vous devez mettre à jour un index, vous devez donc renvoyer sa définition.

<h2 id="sub-5">Considérations relatives à la conception du stockage</h2>

De nombreuses applications de recherche utilisent plusieurs formats de stockage pour répondre à divers besoins en termes d'application. L'espace de stockage interne proposé avec Azure Search doit être utilisé pour le stockage d'index et de documents. L'analyse de texte dépend de la disponibilité rapide de tous les champs pouvant faire l'objet d'une recherche et des attributs associés.

Tous les champs d'un document ne peuvent pas faire l'objet d'une recherche. Par exemple, si votre application est un catalogue en ligne de musique ou de vidéos, il est conseillé de stocker des fichiers binaires dans des objets blob ou une autre forme de stockage. Les fichiers binaires ne pouvant pas faire l'objet d'une recherche, il n'est pas nécessaire de les conserver dans l'espace de stockage Azure Search. Vous devez stocker les images, vidéos et fichiers audio dans d'autres services ou emplacements, avec un champ dans le document affecté au stockage de l'URL vers l'emplacement du fichier.

Pour plus d'informations sur la création d'index ou de documents, consultez la page [API Rest Azure Search](http://msdn.microsoft.com/en-us/library/dn798935.aspx).

<!--Anchors-->
[Step 1: Create the index]: #sub-1
[Step 2: Add documents]: #sub-2
[Step 3: Query an index]: #sub-3
[Step 4: Update or delete indexes and documents]: #sub-4
[Choosing a document store]: #sub-5


<!--Image references-->

<!--Link references-->
[Configure search in the Azure Preview portal]: ../search-configure/
[Manage your search service on Microsoft Azure]: ../search-manage/
[Create your first azure search solution]: ../search-create-first-solution/
