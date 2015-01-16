<properties title="DocumentDB resource model and concepts" pageTitle="Modèles de ressource et concepts DocumentDB | Azure" description="La base de données de documents NoSQL DocumentDB gère les ressources, identifiées de manière unique par des URI logiques avec lesquelles les développeurs peuvent interagir en utilisant des verbes HTTP, des en-têtes de demande/réponse et des codes d'état." metaKeywords="" services="documentdb" solutions="data-management" documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />

#Modèles de ressource et concepts DocumentDB

Le service de base de données de documents nosql DocumentDB fournit un modèle de programmation RESTful simple et ouvert sur HTTP. Les entités gérées par DocumentDB sont nommées **ressources**. Elles sont identifiées de façon unique par leur URI logique. Les développeurs peuvent interagir avec ces ressources en utilisant des verbes HTTP standard, des en-têtes de demande/réponse et des codes d'état. Comme l'illustre le schéma suivant, le **modèle de ressource** de DocumentDB regroupe des ensembles de ressources sous un compte de base de données, chacun d'eux étant adressable via un URI stable et logique.  

>[AZURE.NOTE] DocumentDB fournit également un protocole TCP très efficace qui est également RESTful dans son modèle de communication, disponible via le Kit de développement logiciel (SDK) .Net client.

![][1]  
**Modèle de ressource hiérarchique sous un compte de base de données**   

Commencez par approvisionner un **compte de base de données** DocumentDB en utilisant votre abonnement Azure. Un compte de base de données se compose d'un ensemble de **bases de données**. Chacune d'elles contient plusieurs **collections** et chaque collection contient des **procédures stockées, des déclencheurs, des fonctions définies par l'utilisateur, des documents** et les **pièces jointes** associées. Une base de données a également des **utilisateurs** associés. Chacun d'eux a un ensemble d'**autorisations** permettant d'accéder aux collections, aux procédures stockées, aux déclencheurs, aux fonctions définies par l'utilisateur, aux documents ou aux pièces jointes. Les bases de données, les utilisateurs, les autorisations et les collections sont des ressources définies par le système avec des schémas connus, tandis que les documents et les pièces jointes contiennent du contenu JSON arbitraire défini par l'utilisateur.  

<table width = "400">
    <tbody>
        <tr>
            <td width="84" valign="top">
                <p>
                    <strong>Ressource </strong>
                </p>
            </td>
            <td width="540" valign="top">
                <p>
                    <strong>Description</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="84" valign="top">
                <p>
                    <strong>Database</strong>
                </p>
                <p>
                    <strong>Compte</strong>
                </p>
            </td>
            <td width="540" valign="top">
                <p>
                    Un compte de base de données est associé à une ou plusieurs unités de capacité représentant le débit et le stockage de documents approvisionnés, un ensemble
                    de bases de données et un stockage d'objets blob. Vous pouvez créer un ou plusieurs comptes de base de données à l'aide de votre abonnement Azure. Chaque compte de base de données a un
                    nom DNS unique.
                </p>
            </td>
        </tr>
        <tr>
            <td width="84" valign="top">
                <p>
                    <strong>Database</strong>
                </p>
            </td>
            <td width="540" valign="top">
                <p>
                    Une base de données est un conteneur logique de stockage de documents partitionné entre des collections. Elle sert également de conteneur pour les utilisateurs.
                </p>
            </td>
        </tr>
        <tr>
            <td width="84" valign="top">
                <p>
                    <strong>Utilisateur</strong>
                </p>
            </td>
            <td width="540" valign="top">
                <p>
                    Espace de noms logique pour les autorisations d'étendue.
                </p>
            </td>
        </tr>
        <tr>
            <td width="84" valign="top">
                <p>
                    <strong>Autorisation</strong>
                </p>
            </td>
            <td width="540" valign="top">
                <p>
                    Jeton d'autorisation associé à un utilisateur pour un accès autorisé à une ressource spécifique.
                </p>
            </td>
        </tr>
        <tr>
            <td width="84" valign="top">
                <p>
                    <strong>Collection</strong>
                </p>
            </td>
            <td width="540" valign="top">
                <p>
                    Une collection est un conteneur de documents JSON. Elle est associée à une logique d'application JavaScript. Les requêtes et les transactions sont étendues vers les
                    collections.
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Ressources système ou ressources définies par l'utilisateur
Les ressources (telles que les comptes de base de données, les bases de données, les collections, les utilisateurs, les autorisations, les procédures stockées, les déclencheurs et les fonctions définies par l'utilisateur) ont toutes un schéma fixe et sont nommées " ressources système ". Par contre, les ressources telles que les documents et les pièces jointes n'ont pas de restriction de schéma et sont des exemples de ressources définies par l'utilisateur. Dans DocumentDB, les ressources système et définies par l'utilisateur sont représentées et gérées en tant qu'éléments JSON standard.  

#Comptes de base de données
![][2]  

Votre abonnement Azure vous permet d'approvisionner un ou plusieurs comptes de base de données DocumentDB. Pour votre compte de base de données, vous pouvez acheter des unités empilables de stockage de documents SSD et de débit en termes d'unités de capacité.  Selon les besoins d'extensibilité et de performances de vos applications, vous pouvez ajouter ou supprimer des unités de capacité de façon incrémentielle. Chaque unité de capacité est fournie avec un ensemble de collections élastiques, ainsi qu'un stockage de documents SSD et un débit approvisionnés. Le stockage de documents approvisionné et la capacité de débit associée à une unité de capacité sont distribués dans des collections DocumentDB que vous créez sous plusieurs bases de données de votre compte de base de données. La capacité approvisionnée sous un compte de base de données est disponible pour toutes les bases de données et collections existantes ou créées dans le compte. En pratique, l'extensibilité de la taille du compte de base de données n'est pas limitée : vous pouvez ajouter n'importe quel nombre d'unités de capacité au fur et à mesure, selon les restrictions de l'offre. Les ressources gérées dans une unité de capacité sont montées en charge via le partitionnement et répliquées pour une haute disponibilité.   

Vous pouvez créer et gérer des comptes de base de données DocumentDB via le portail Azure à l'adresse [http://portal.azure.com/](http://portal.azure.com/). La création et la gestion d'un compte de base de données requièrent un accès administratif et peuvent uniquement être effectuées sous votre abonnement Azure. Dans le cadre de l'approvisionnement et de la gestion d'un compte de base de données, vous pouvez configurer et lire les propriétés suivantes :  

<table border="1" cellspacing="0" cellpadding="0" > 
<tbody>
<tr>
<td valign="top" ><p><b>Nom de la propriété</b></p></td>
<td valign="top" ><p><b>Description</b></p></td>
</tr>

<tr>
<td valign="top" ><p>Stratégie de cohérence</p></td>
<td valign="top" ><p>Définissez cette propriété pour configurer le niveau de cohérence par défaut pour toutes les collections sous votre compte de base de données. Vous pouvez changer le niveau de cohérence en fonction de la demande en utilisant l'en-tête de demande [x-ms-consistency-level]. ﻿À l'avenir, nous prendrons probablement en charge le changement du niveau de cohérence par collection. </p>

<p>Notez que cette propriété ne s'applique qu'aux <i>ressources définies par l'utilisateur</i>. Toutes les ressources définies par le système sont configurées pour prendre en charge les requêtes/lectures avec une cohérence forte.</p></td>
</tr>

<tr>
<td valign="top" ><p>Clé principale et clé secondaire</p></td>
<td valign="top" ><p>Les clés principales et secondaires fournissent un accès administratif à toutes les ressources sous le compte de base de données.</p></td>
</tr>

<tr>
<td valign="top" ><p>MaxMediaStorageUsageInMB (READ)</p></td>
<td valign="top" ><p>Quantité maximale d'espace de stockage multimédia disponible pour le compte de base de données</p></td>
</tr>

<tr>
<td valign="top" ><p>CurrentMediaStorageUsageInMB (READ)</p></td>
<td valign="top" ><p>Utilisation actuelle de l'espace de stockage multimédia disponible pour le compte de base de données</p></td>
</tr>

<tr>
<td valign="top" ><p>CapacityUnitsProvisioned</p></td>
<td valign="top" ><p>Nombre total d'unités de capacité approvisionnées sous le compte de base de données</p></td>
</tr>

<tr>
<td valign="top" ><p>CapacityUnitsConsumed (READ)</p></td>
<td valign="top" ><p>Nombre total d'unités de capacité actuellement consommées sous le compte de base de données</p></td>
</tr>

<tr>
<td valign="top" ><p>ProvisionedDocumentStorageInMB (READ)</p></td>
<td valign="top" ><p>Quantité totale d'espace de stockage de documents (en Mo) approvisionnée entre toutes les unités de capacité sous le compte de base de données</p></td>
</tr>

<tr>
<td valign="top" ><p>ReservedDocumentStorageInMB (READ)</p></td>
<td valign="top" ><p>Quantité totale d'espace de stockage de documents (en Mo) réservée entre toutes les unités de capacité sous le compte de base de données. Le stockage de documents est " réservé " lorsqu'une collection contient des documents. </p></td>
</tr>

<tr>
<td valign="top" ><p>ConsumedDocumentStorageInMB (READ)</p></td>
<td valign="top" ><p>Quantité totale d'espace de stockage de documents (en Mo) réellement consommée entre toutes les unités de capacité sous le compte de base de données. Sont pris en compte la taille de vos documents et de votre index réels combinés dans différentes collections sous un compte de base de données.</p></td>
</tr>

</tbody>
</table>


Notez qu'en plus d'approvisionner, de configurer et de gérer votre compte de base de données à partir du portail Azure, vous pouvez également créer et gérer des comptes de base de données DocumentDB par programme via des [API REST Azure DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402413) ainsi que des Kits de développement logiciel (SDK) clients.  

 
#Bases de données
Dans DocumentDB, une base de données correspond à un conteneur logique d'un(e) ou plusieurs collections et utilisateurs. Vous pouvez créer n'importe quel nombre de bases de données sous un compte de base de données DocumentDB en fonction des limites de l'offre.  
![][3]  
**Une base de données est un conteneur logique d'utilisateurs et de collections**

Une base de données peut contenir un stockage de documents pratiquement illimité, partitionné en collections, qui forment les domaines de transaction pour les documents qu'elles contiennent. Une base de données DocumentDB est flexible par défaut : elle peut aller de quelques Go à plusieurs pétaoctets de stockage de documents SSD et de débit approvisionné. Contrairement à une base de données de SGBDR classique, une base de données DocumentDB n'est pas étendue à un seul ordinateur. Avec DocumentDB, tandis que les besoins d'extensibilité de votre application augmentent, vous pouvez créer plus de collections ou de bases de données, ou les deux. En effet, plusieurs applications de Microsoft utilisent DocumentDB à l'échelle du client en créant des bases de données DocumentDB très volumineuses, contenant chacune des milliers de collections regroupant des téraoctets de stockage de documents. Vous pouvez augmenter ou réduire la taille d'une base de données en ajoutant ou en supprimant des collections pour répondre aux besoins d'extensibilité de vos applications. Vous pouvez créer n'importe quel nombre de collections dans une base de données, selon l'offre et la quantité d'unités de capacité que vous achetez. Le stockage SSD et le débit approvisionnés pour vous via l'achat d'unités de capacité peuvent être répartis entre les collections situées sous les bases de données de votre compte de base de données.   

Une base de données DocumentDB est également un conteneur d'utilisateurs. De même, un utilisateur est un espace de noms logique pour un ensemble d'autorisations qui permet d'obtenir une autorisation ou un accès affinés aux collections, documents et pièces jointes.  
 
Comme pour les autres ressources du modèle de ressource DocumentDB, les bases de données peuvent être facilement créées, remplacées, supprimées, lues ou répertoriées avec des [API REST Azure DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402413) ou l'un des Kits de développement logiciel (SDK) clients. DocumentDB garantit une cohérence forte pour la lecture ou l'interrogation des métadonnées d'une ressource de base de données. La suppression d'une base de données garantit automatiquement que vous ne pouvez pas accéder ni aux collections ni aux utilisateurs qui y sont inclus. Même si DocumentDB réclame le stockage et le débit approvisionnés suite à la suppression de la base de données en arrière-plan, le stockage et le débit approvisionnés de la base de données supprimée sont immédiatement disponibles pour utilisation.   
 

#Collections
Une collection DocumentDB est un conteneur pour vos documents JSON. Une collection est également une unité de mise à l'échelle, de transaction et de requête. Vous pouvez monter en charge une base de données DocumentDB en y ajoutant des collections. Si votre application a besoin d'une mise à l'échelle supplémentaire, vous pouvez approvisionner plus de stockage de documents SSD et de débit et les distribuer aux collections d'une ou plusieurs bases de données sous votre compte de base de données.
 
##Stockage de documents SSD flexible
Une collection est intrinsèquement flexible : elle augmente ou diminue à mesure que vous ajoutez ou supprimez des documents. Bien que l'utilisation de DocumentDB ait été testée avec des milliers de collections (au sein d'une base de données) dont la taille variait pour chacune de quelques gigaoctets à plusieurs téraoctets, l'offre préliminaire Standard de DocumentDB limite actuellement la flexibilité d'une collection donnée à 10 Go. 

##Indexation automatique
DocumentDB est un véritable système de base de données sans schéma. Il ne part pas du principe que vous utilisez des schémas et n'en réclame pas pour les documents JSON. Dès que vous ajoutez des documents à une collection, DocumentDB les indexe automatiquement et vous pouvez les interroger. L'indexation automatique de documents sans schéma ou index secondaire est une fonctionnalité clé de DocumentDB. Elle est rendue possible par des techniques offrant une maintenance d'index structurée par des journaux, sans verrouillage et optimisée pour l'écriture. DocumentDB prend en charge des volumes soutenus d'écritures très rapides, tout en continuant de servir des requêtes cohérentes. Les stockages de documents et d'index permettent de calculer le stockage consommé par chaque collection. Vous pouvez contrôler le stockage et les compromis de performances associés à l'indexation en configurant la stratégie d'indexation d'une collection. 

##Configuration de la stratégie d'indexation d'une collection
La stratégie d'indexation de chaque collection vous permet d'établir des compromis entre les performances et les stockages associés à l'indexation. Les options de configuration d'indexation suivantes sont disponibles :  

-	Choisissez si la collection indexe ou non automatiquement tous les documents. Par défaut, tous les documents sont automatiquement indexés. Vous pouvez choisir de désactiver l'indexation automatique et d'ajouter de façon sélective uniquement certains documents à l'index. ﻿À l'inverse, vous pouvez choisir d'exclure certains documents. Pour ce faire, définissez la propriété automatique sur True ou False dans le paramètre indexingPolicy d'une collection et utilisez l'en-tête de demande [x-ms-indexingdirective] lors de l'insertion, du remplacement ou de la suppression d'un document.  
-	Sélectionnez l'inclusion ou l'exclusion de chemins d'accès ou de modèles spécifiques dans vos documents à partir de l'index. Pour ce faire, définissez les paramètres includedPaths et excludedPaths à partir du paramètre indexingPolicy d'une collection. Vous pouvez également configurer les compromis de performances et de stockage pour les requêtes de plage de données et de hachage pour certains formats de chemin d'accès. 
-	Choisissez entre des mises à jour d'index synchrones (cohérentes) ou asynchrones (différées). Par défaut, l'index est mis à jour de manière synchrone lors de chaque insertion, remplacement ou suppression d'un document au niveau de la collection. Cela permet aux requêtes de fournir le même niveau de cohérence que celui des lectures de document. Alors que DocumentDB est optimisé pour les écritures et prend en charge des volumes soutenus d'écritures de documents, la maintenance d'index synchrone et les requêtes cohérentes, vous pouvez configurer certaines collections de manière à ce que la mise à jour de l'index soit effectuée en différé. L'indexation en différé dynamise les performances en termes d'écriture. Cela est idéal pour les scénarios d'ingestion en bloc pour les collections comportant principalement beaucoup de lectures.

Dans la version préliminaire de DocumentDB, la stratégie d'indexation peut uniquement être configurée durant la création d'une collection. Une fois la collection créée, la stratégie ne peut pas être mise à jour.

##Interrogation d'une collection
Les documents d'une collection peuvent suivre des schémas arbitraires et vous pouvez les interroger sans fournir de schéma ou d'index secondaires à l'avance. Vous pouvez interroger une collection en utilisant le langage de requête SQL de DocumentDB, qui fournit des opérateurs relationnels et hiérarchiques enrichis et une extensibilité via des fonctions JavaScript définies par l'utilisateur. La syntaxe JSON permet la modélisation de documents JSON en tant qu'arborescences avec des étiquettes, comme les nœuds d'arborescence. Cette capacité est exploitée par les techniques d'indexation automatique de DocumentDB et le dialecte de requête SQL de ce dernier. Le langage de requête DocumentDB est caractérisé par trois aspects principaux :   

1.	Un ensemble réduit d'opérations de requête mappant naturellement vers l'arborescence incluant des requêtes hiérarchiques et des projections. 
2.	Un sous-ensemble d'opérations relationnelles incluant la composition, le filtrage, les projections, les agrégations et les jointures réflexives. 
3.	Des fonctions définies par l'utilisateur JavaScript pures, qui se composent en (1) et (2)  

Le modèle de requête DocumentDB tente de garantir un équilibre entre les fonctionnalités, l'efficacité et la simplicité. Le moteur de base de données de DocumentDB compile et exécute de façon native les instructions de requête SQL. Vous pouvez interroger une collection en utilisant les [API REST Azure DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402413) ou l'un des Kits de développement logiciel (SDK) clients. Le Kit de développement logiciel (SDK) .NET comprend un fournisseur LINQ. Dans une prochaine version, des mappages de traits de soulignement natifs seront fournis. Ils pourront être utilisés depuis les Kits de développement logiciel (SDK) JavaScript côté client ainsi que les procédures stockées et déclencheurs côté serveur.   


##Transactions multi-documents
Les transactions de bases de données offrent un modèle de programmation prédictif et sécurisé pour la gestion des modifications simultanées des données. Dans SGBDR, la méthode classique d'écriture de logique métier consiste à écrire des **procédures stockées** et/ou des **déclencheurs** puis à les expédier vers le serveur de base de données pour exécuter la transaction. Dans SGBDR, le programmateur de l'application doit gérer deux langages de programmation différents : (a) le langage de programmation de l'application non transactionnel (comme JavaScript, Python, C#, Java, etc.) et (b) T-SQL, le langage de programmation transactionnel, qui est exécuté de manière native par la base de données. En vertu de son engagement ferme vis-à-vis de JavaScript et JSON directement dans le moteur de base de données, DocumentDB fournit un modèle de programmation intuitif pour l'exécution de logiques d'application JavaScript directement sur les collections en termes de procédures stockées et de déclencheurs. Ceci permet : (a) une implémentation efficace du contrôle concurrentiel, de la récupération et de l'indexation automatique des objets graphiques JSON directement dans le moteur de base de données, ainsi que, (b) un flux de contrôle naturellement expressif, une étendue variable, une affectation et une intégration des primitives de gestion d'exception avec les transactions de bases de données, directement en termes de langage de programmation JavaScript.   

La logique JavaScript enregistrée au niveau d'une collection peut alors émettre des opérations de base de données vers les documents d'une collection donnée. DocumentDB inclut implicitement les procédures stockées et les déclencheurs JavaScript dans des transactions ACID ambiantes avec un isolement de capture instantanée dans les documents d'une collection. Lors de son exécution, si le code JavaScript lève une exception, toute la transaction est abandonnée. Le modèle de programmation qui en résulte est simple mais puissant. Les développeurs JavaScript obtiennent un modèle de programmation " durable " tout en continuant d'utiliser les constructions de langage et les primitives de bibliothèques qui leurs sont familières.   

La possibilité d'exécuter directement JavaScript dans le moteur de base de données au sein du même espace d'adressage que le pool de mémoires tampons permet une exécution performante et transactionnelle des opérations de base de données sur les documents d'une collection. De plus, l'engagement ferme du moteur de base de données DocumentDB envers JSON et JavaScript élimine tout risque d'incohérence d'impédance entre les systèmes de type d'application et la base de données.   

Une fois la collection créée, vous pouvez enregistrer des procédures stockées, des déclencheurs et des fonctions définies par l'utilisateur auprès d'une collection en utilisant des API REST DocumentDB ou l'un des Kits de développement logiciel (SDK) clients. Après l'enregistrement, vous pouvez les référencer et les exécuter. Prenons comme exemple la procédure stockée suivante écrite entièrement en JavaScript, qui prend deux arguments (un titre de livre et un nom d'auteur) et qui crée un document, demande un document puis le met à jour, le tout dans le cadre d'une transaction ACID implicite. Si le JavaScript lève une exception durant son exécution, l'intégralité de la transaction est annulée.

	function businessLogic(name, author) {
	    var context = getContext();
	    var collectionManager = context.getCollection();        
	    var collectionLink = collectionManager.getSelfLink()
	        
	    // create a new document.
	    collectionManager.createDocument(collectionLink,
	        {id: name, author: author},
	        function(err, documentCreated) {
	            if(err) throw new Error(err.message);
	            
	            // filter documents by author
	            var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
	            collectionManager.queryDocuments(collectionLink,
	                filterQuery,
	                function(err, matchingDocuments) {
	                    if(err) throw new Error(err.message);
	                    
	                    context.getResponse().setBody(matchingDocuments.length);
	                   
	                    // Replace the author name for all documents that satisfied the query.
	                    for (var i = 0; i < matchingDocuments.length; i++) {
	                        matchingDocuments[i].author = "George R. R. Martin";
	                        // we don't need to execute a callback because they are in parallel
	                        collectionManager.replaceDocument(matchingDocuments[i]._self,
	                            matchingDocuments[i]);   
	                    }
	                })
	        })
	};

Le client peut " expédier " la logique JavaScript ci-dessus à la base de données pour une exécution transactionnelle via HTTP POST.  

	client.createStoredProcedureAsync(collection._self, {id: "CRUDProc", body: businessLogic})
	   .then(function(createdStoredProcedure) {
	        return client.executeStoredProcedureAsync(createdStoredProcedure.resource._self,
	            "NoSQL Distilled",
	            "Martin Fowler");
	    })
	    .then(function(result) {
	        console.log(result);
	    },
	    function(error) {
	        console.log(error);
	    });


Tenez compte du fait que, comme la base de données comprend de manière native les langages JSON et JavaScript, il n'y a pas de risque d'incohérence de système de type, pas de " mappage OR " ou de génération de code requise.   

Les procédures stockées et les déclencheurs interagissent avec une collection et les documents d'une collection via un modèle d'objet bien défini qui expose le contexte de collection actuel.  

Dans DocumentDB, les collections peuvent être facilement créées, supprimée, lues ou répertoriées avec des [API REST Azure DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402413) ou l'un des Kits de développement logiciel (SDK) clients. DocumentDB fournit toujours une cohérence forte pour la lecture ou l'interrogation des métadonnées d'une collection. La suppression d'une collection garantit automatiquement que vous ne pouvez pas accéder aux documents, pièces jointes, procédures stockées, déclencheurs et fonctions définies par l'utilisateur qu'elle contient. Même si DocumentDB réclame le stockage et le débit approvisionnés suite à la suppression de la collection en arrière-plan, le stockage et le débit approvisionnés de la collection supprimée sont immédiatement disponibles de nouveau pour utilisation.   

 
#Procédures stockées, déclencheurs et fonctions définies par l'utilisateur
Comme décrit dans la section précédente, vous pouvez écrire une logique d'application pour qu'elle s'exécute directement dans une transaction dans le moteur de base de données. La logique d'application peut être entièrement écrite en JavaScript et modélisée en tant que procédure stockée, déclencheur ou fonction, définie par l'utilisateur. Le code JavaScript d'une procédure stockée ou d'un déclencheur peut insérer, remplacer, supprimer, lire ou interroger les documents d'une collection. D'un autre côté, le code JavaScript d'une fonction définie par l'utilisateur peut uniquement effectuer des calculs secondaires libres en énumérant les documents de l'ensemble de résultats de la requête, pour produire un autre ensemble de résultats. DocumentDB applique une gouvernance des ressources basée sur une réservation stricte aux architectures mutualisées. Chaque procédure stockée, déclencheur ou fonction définie par l'utilisateur obtient un quantum fixe de ressources de systèmes d'exploitation pour effectuer ses tâches. De plus, les procédures stockées, déclencheurs ou fonctions définies par l'utilisateur ne peuvent pas créer de liens vers les bibliothèques JavaScript externes et sont placés sur liste noire s'ils dépassent les budgets de ressources qui leurs sont alloués. Vous pouvez enregistrer ou annuler l'enregistrement de procédures stockées, déclencheurs ou fonctions définies par l'utilisateur dans une collection en utilisant des API REST.  Une fois enregistrés, ils sont précompilés et stockés en tant que codes d'octets et sont exécutés ultérieurement. La section suivante illustre l'utilisation du Kit de développement logiciel (SDK) JavaScript de DocumentDB pour enregistrer, exécuter et annuler l'enregistrement d'une procédure stockée, d'un déclencheur et d'une fonction définie par l'utilisateur. Le Kit de développement logiciel (SDK) JavaScript est un wrapper simple des API REST de DocumentDB. 

##Enregistrement d'une procédure stockée
L'enregistrement d'une procédure stockée consiste à créer une ressource de procédure stockée dans une collection via HTTP POST.  

	var storedProc = {
	    id: "validateAndCreate",
	    body: function (documentToCreate) {
	        documentToCreate.id = documentToCreate.id.toUpperCase();
	        
	        var collectionManager = getContext().getCollection();
	        collectionManager.createDocument(collectionManager.getSelfLink(),
	            documentToCreate,
	            function(err, documentCreated) {
	                if(err) throw new Error('Error while creating document: ' + err.message;
	                getContext().getResponse().setBody('success - created ' + 
	                        documentCreated.name);
	            });
	    }
	};
	
	client.createStoredProcedureAsync(collection._self, storedProc)
	    .then(function (createdStoredProcedure) {
	        console.log("Successfully created stored procedure");
	    }, function(error) {
	        console.log("Error");
	    });

##Exécution d'une procédure stockée
L'exécution d'une procédure stockée s'effectue grâce à l'émission d'une instruction HTTP POST sur une ressource de procédure stockée existante en transmettant les paramètres vers la procédure dans le corps de la demande.

	var inputDocument = {id : "document1", author: "G. G. Marquez"};
	client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
	    .then(function(executionResult) {
	        assert.equal(executionResult, "success - created DOCUMENT1");
	    }, function(error) {
	        console.log("Error");
	    });

##Annulation de l'enregistrement d'une procédure stockée
L'annulation de l'enregistrement d'une procédure stockée s'effectue simplement grâce à l'émission d'une instruction HTTP DELETE sur une ressource de procédure stockée existante.   

	client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
	    .then(function (response) {
	        return;
	    }, function(error) {
	        console.log("Error");
	    });


##Enregistrement d'un pré-déclencheur
L'enregistrement d'un déclencheur s'effectue en créant une ressource de déclencheur dans une collection via HTTP POST. Vous pouvez spécifier si le déclencheur est un pré- ou post-déclencheur, ainsi que le type d'opération qui lui est associée (par exemple Create, Replace, Delete ou All).   

	var preTrigger = {
	    id: "upperCaseId",
	    body: function() {
	            var item = getContext().getRequest().getBody();
	            item.id = item.id.toUpperCase();
	            getContext().getRequest().setBody(item);
	    },
	    triggerType: TriggerType.Pre,
	    triggerOperation: TriggerOperation.All
	}
	
	client.createTriggerAsync(collection._self, preTrigger)
	    .then(function (createdPreTrigger) {
	        console.log("Successfully created trigger");
	    }, function(error) {
	        console.log("Error");
	    });

##Exécution d'un pré-déclencheur
L'exécution d'un déclencheur s'effectue en spécifiant le nom d'un déclencheur existant au moment de l'émission de la demande POST/PUT/DELETE d'une ressource de document via l'en-tête de demande.  
 
	client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
	    .then(function(createdDocument) {
	        assert.equal(createdDocument.resource.id, "DOC1");
	    }, function(error) {
	        console.log("Error");
	    });

##Annulation de l'enregistrement d'un pré-déclencheur
L'annulation de l'enregistrement d'un déclencheur s'effectue simplement grâce à l'émission d'une instruction HTTP DELETE sur une ressource de déclencheur existante.  

	client.deleteTriggerAsync(createdPreTrigger._self);
	    .then(function(response) {
	        return;
	    }, function(error) {
	        console.log("Error");
	    });

##Enregistrement d'une fonction définie par l'utilisateur
L'enregistrement d'une fonction définie par l'utilisateur s'effectue en créant une ressource de fonction définie par l'utilisateur dans une collection via HTTP POST.  

	var udf = { 
	    id: "mathSqrt",
	    body: function(number) {
	            return Math.sqrt(number);
	    },
	};
	client.createUserDefinedFunctionAsync(collection._self, udf)
	    .then(function (createdUdf) {
	        console.log("Successfully created stored procedure");
	    }, function(error) {
	        console.log("Error");
	    });



##Exécution d'une fonction définie par l'utilisateur dans le cadre d'une requête
Vous pouvez spécifier une fonction définie par l'utilisateur dans le cadre d'une requête SQL et l'utiliser pour étendre le langage de requête SQL de DocumentDB. Pour plus d'informations sur la composition d'une fonction définie par l'utilisateur dans le langage de requête SQL de DocumentDB, consultez les spécifications relatives à ce langage.  

	var filterQuery = "SELECT mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
	client.queryDocuments(collection._self, filterQuery).toArrayAsync();
	    .then(function(queryResponse) {
	        var queryResponseDocuments = queryResponse.feed;
	    }, function(error) {
	        console.log("Error");
	    });

##Annulation de l'enregistrement d'une fonction définie par l'utilisateur 
L'annulation de l'enregistrement d'une fonction définie par l'utilisateur s'effectue simplement grâce à l'émission d'une instruction HTTP DELETE sur une ressource de fonction définie par l'utilisateur existante.  

	client.deleteUserDefinedFunctionAsync(createdUdf._self)
	    .then(function(response) {
	        return;
	    }, function(error) {
	        console.log("Error");
	    });

Bien que les extraits de code précédents ont montré l'enregistrement (POST), l'annulation de l'enregistrement (PUT), la lecture ou le tri (GET) et l'exécution (POST) via le Kit de développement logiciel (SDK) JavaScript de DocumentDB, vous pouvez également utiliser les API REST ou d'autres Kits clients. 

 
#Documents
Vous pouvez insérer, remplacer, supprimer, lire, énumérer et interroger arbitrairement des documents JSON dans une collection. DocumentDB n'impose aucun schéma et ne requiert pas d'index secondaire pour prendre en charge l'interrogation des documents dans une collection.   

En étant un service de base de données véritablement ouvert, DocumentDB n'invente pas de types de données spécialisés (par exemple, les valeurs de date et heure) ou d'encodage spécifique pour les documents JSON. Notez que DocumentDB ne requiert aucune convention JSON spéciale pour codifier les relations entre les différents documents. Le langage de requête SQL de DocumentDB fournit des opérateurs de requête hiérarchiques et relationnels très puissants, permettant d'interroger et de projeter des documents sans annotation spécifique ou obligation de codifier des relations entre les documents à l'aide de propriétés distinctes.  
 
Comme avec les autres ressources, vous pouvez créer, remplacer, supprimer, lire, énumérer et interroger facilement les documents en utilisant des API REST ou l'un des Kits de développement logiciel (SDK) clients. La suppression d'un document libère instantanément le quota correspondant à toutes les pièces jointes imbriquées. Le niveau de cohérence de lecture des documents respecte la stratégie de cohérence du compte de base de données. Vous pouvez remplacer cette stratégie en fonction de la demande, selon les besoins de cohérence des données de votre application. Lors d'une interrogation de documents, la cohérence de lecture respecte le mode d'indexation défini pour la collection. Par " cohérence ", on entend la stratégie de cohérence du compte. 

#Pièces jointes et éléments multimédias
DocumentDB vous permet de stocker des objets blob ou des éléments multimédias binaires dans son propre magasin ou votre magasin d'éléments multimédias distant. Il vous permet également de représenter les métadonnées d'un élément multimédia dans les termes d'un document spécifique nommé " pièce jointe ". Dans DocumentDB, une pièce jointe correspond à un document (JSON) spécifique qui référence l'objet blob ou l'élément multimédia stocké ailleurs. Une pièce jointe est tout simplement un document spécifique qui capture les métadonnées (comme l'emplacement, l'auteur, etc.) d'un élément multimédia stocké dans un magasin d'éléments multimédias distant. 

Imaginez une application de lecture sociale utilisant DocumentDB pour stocker des annotations manuscrites et des métadonnées incluant des commentaires, des recommandations, des signets, des évaluations, des " j'aime/je n'aime pas ", etc. associés au livre électronique d'un utilisateur donné.   

-	Le contenu du livre est stocké dans le stockage d'éléments multimédias, qui est disponible dans le compte de base de données de DocumentDB ou dans un magasin d'éléments multimédias distant. 
-	Une application peut stocker chaque métadonnée de l'utilisateur dans un document distinct (par exemple, les métadonnées de Jean pour le livre1 sont stockées dans un document référencé par le chemin /colls/jean/docs/livre1). 
-	Les pièces jointes pointant vers les pages du contenu du livre d'un utilisateur sont stockées dans le document correspondant, par exemple /colls/jean/docs/livre1/chapitre1, /colls/jean/docs/livre1/chapitre2 etc. 

Notez que les exemples utilisent des ID conviviaux pour communiquer les ressources de façon hiérarchique. Vous pouvez accéder aux ressources avec des API REST via des ID de ressources uniques. 

Pour les éléments multimédias gérés par DocumentDB, la propriété _media de la pièce jointe va référencer les éléments multimédias en suivant leur URI. DocumentDB va veiller à nettoyer les éléments multimédias lorsque toutes les références en suspens sont supprimées. DocumentDB génère automatiquement les pièces jointes lorsque vous téléchargez les nouveaux éléments multimédias et renseigne la propriété _media pour pointer vers l'élément multimédia récemment ajouté. Si vous choisissez de stocker l'élément multimédia dans un magasin d'objets blob distant que vous gérez (comme OneDrive, Azure Storage, DropBox, etc.), vous pouvez toujours utiliser les pièces jointes pour le référencer. Dans ce cas, vous créez la pièce jointe vous-même et renseignez sa propriété _media manuellement.   

Comme avec les autres ressources, vous pouvez créer, remplacer, supprimer, lire ou énumérer facilement les pièces jointes en utilisant des API REST ou l'un des Kits de développement logiciel (SDK) clients. Comme pour les documents, le niveau de cohérence de lecture des pièces jointes respecte la stratégie de cohérence du compte de base de données. Vous pouvez remplacer cette stratégie en fonction de la demande, selon les besoins de cohérence des données de votre application. Lors d'une interrogation de pièces jointes, la cohérence de lecture respecte le mode d'indexation défini pour la collection. Par " cohérence ", on entend la stratégie de cohérence du compte. 

 
#Utilisateurs
Dans DocumentDB, un utilisateur correspond à un espace de noms logique pour le regroupement des autorisations. Un utilisateur de DocumentDB peut correspondre à un utilisateur dans un système de gestion d'identité ou à un rôle d'application prédéfini. Pour DocumentDB, un utilisateur représente simplement une donnée abstraite pour grouper un ensemble d'autorisations dans une base de données.   

Pour implémenter une architecture mutualisée pour votre application, vous pouvez créer des utilisateurs dans DocumentDB qui correspondent à vos utilisateurs actuels ou aux clients de votre application. Vous pouvez ensuite créer des autorisations pour un utilisateur donné correspondant au contrôle d'accès de divers collections, documents, pièces jointes, etc.   

Lorsque vous devez faire évoluer vos applications en fonction de la croissance de vos utilisateurs, vous pouvez utiliser plusieurs méthodes pour partitionner vos données. Vous pouvez modéliser chacun de vos utilisateurs pour   

-	chaque utilisateur mappe vers une base de données ;
-	chaque utilisateur mappe vers une collection ; 
-	faire correspondre les documents de plusieurs utilisateurs à une collection dédiée ; 
-	modéliser les documents correspondant à plusieurs utilisateurs dans un ensemble de collections.   

Indépendamment de la stratégie de partition que vous choisissez, vous pouvez modéliser vos utilisateurs actuels en tant qu'utilisateurs dans la base de données DocumentDB et associer des autorisations affinées à chaque utilisateur.  

![][4]  
**Stratégies de partitionnement et modélisation des utilisateurs**

Comme avec les autres ressources, vous pouvez créer, remplacer, supprimer, lire ou énumérer facilement les utilisateurs dans DocumentDB en utilisant des API REST ou l'un des Kits de développement logiciel (SDK) clients. DocumentDB fournit toujours une cohérence forte pour la lecture ou l'interrogation des métadonnées d'une ressource d'utilisateur. Il est intéressant de mentionner que la suppression d'un utilisateur garantit automatiquement que vous ne pouvez pas accéder à l'une des autorisations qu'il contient. Même si DocumentDB réclame le quota d'autorisations dans le cadre de la suppression de l'utilisateur en arrière-plan, les autorisations supprimées sont de nouveau disponibles immédiatement pour utilisation.  


#Autorisations
Pour le contrôle d'accès, les ressources comme les comptes de base de données, les bases de données, les utilisateurs et les autorisations sont considérées en tant que ressources d'*administration*, car elles requièrent des autorisations d'administration. D'un autre côté, les ressources incluant les collections, les documents, les pièces jointes, les procédures stockées, les déclencheurs et les fonctions définies par l'utilisateur sont étendues sous une base de données et considérées comme des *ressources d'application*. Le modèle d'autorisation correspondant à ces deux types de ressources et aux rôles qui y accèdent (c'est-à-dire l'administrateur et l'utilisateur) définit deux types de *clés d'accès* : la *clé principale* et la *clé de ressource*. La clé principale appartient au compte de base de données et est fournie au développeur (ou à l'administrateur) qui approvisionne le compte de base de données. Cette clé principale ayant la sémantique d'administration, elle permet d'autoriser l'accès pour les ressources d'administration et d'application. Par contre, une clé de ressource est une clé d'accès granulaire qui permet d'accéder à une ressource d'application *spécifique*. Elle capture donc la relation entre l'utilisateur d'une base de données et les autorisations de l'utilisateur pour une ressource spécifique (comme une collection, un document, une pièce jointe, une procédure stockée, un déclencheur ou une fonction définie par l'utilisateur).   

La seule façon d'obtenir une clé de ressource est de créer une ressource d'autorisation pour un utilisateur donné. Notez que pour créer ou récupérer une autorisation, une clé principale doit être présentée dans l'en-tête d'autorisation. Une ressource d'autorisation lie la ressource, son accès et l'utilisateur. Une fois la ressource d'autorisation créée, l'utilisateur doit simplement présenter la clé de ressource associée pour obtenir l'accès à la ressource correspondante. Ainsi, une clé de ressource peut être vue comme une représentation logique et compacte d'une ressource d'autorisation.  

Comme avec les autres ressources, vous pouvez créer, remplacer, supprimer, lire ou énumérer facilement les autorisations dans DocumentDB en utilisant des API REST ou l'un des Kits de développement logiciel (SDK) clients. DocumentDB fournit toujours une cohérence forte pour la lecture ou l'interrogation des métadonnées d'une autorisation. 




[1]: ./media/documentdb-resources/resources1.png
[2]: ./media/documentdb-resources/resources2.png
[3]: ./media/documentdb-resources/resources3.png
[4]: ./media/documentdb-resources/resources4.png
