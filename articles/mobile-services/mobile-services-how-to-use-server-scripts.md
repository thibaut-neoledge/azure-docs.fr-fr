<properties 
	pageTitle="Utilisation d'un service mobile de backend Javascript" 
	description="Fournit des exemples sur la façon de définir, enregistrer et utiliser des scripts serveurs dans Azure Mobile Services." 
	services="mobile-services" 
	documentationCenter="" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="javascript" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/23/2016" 
	ms.author="ricksal"/>


# Utilisation d'un service mobile de backend Javascript

Cet article fournit des informations détaillées et des exemples sur l'utilisation d'un backend JavaScript dans Azure Mobile Services.

##<a name="intro"></a>Introduction

Dans un service mobile de backend JavaScript, vous pouvez définir une logique métier personnalisée en tant que code JavaScript stocké et exécuté sur le serveur. Ce code de script serveur est attribué à l'une des fonctionnalités de serveur suivantes :

+ [Opérations d'insertion, de lecture, de mise à jour ou de suppression sur une table donnée][Table operations].
+ [Travaux planifiés][Job Scheduler].
+ [Méthodes HTTP définies dans une API personnalisée][Custom API anchor]. 

La signature de la fonction principale dans le script serveur dépend du contexte de l'emplacement d'utilisation du script. Vous pouvez également définir un code de script commun comme les modules nodes.js qui sont partagés entre des scripts. Pour plus d'informations, consultez la section [Contrôle du code source et code partagé][Source control, shared code, and helper functions].

Pour obtenir une description des objets et des fonctions des scripts serveur individuels, consultez la page [Référence de script serveur Mobile Services].


##<a name="table-scripts"></a>Opérations de table

Un script d'opération de table est un script serveur enregistré pour une opération sur une table : insertion, lecture, mise à jour ou suppression (*del*). Cette section décrit comment utiliser des opérations de table dans un backend JavaScript et comprend les sections suivantes :

+ [Vue d'ensemble des opérations de table][Basic table operations]
+ [Procédure : enregistrement pour les opérations de table]
+ [Procédure : remplacement de la réponse par défaut]
+ [Procédure : remplacement du succès d'execute]
+ [Procédure : remplacement de la gestion des erreurs par défaut]
+ [Procédure : génération de valeurs d'ID uniques](#generate-guids)
+ [Procédure : ajout de paramètres personnalisés]
+ [Procédure : fonctionnement des utilisateurs de table][How to: Work with users]

###<a name="basic-table-ops"></a>Vue d'ensemble des opérations de table

Le nom du script doit correspondre au type d'opération pour lequel il est enregistré. Un seul script peut être enregistré pour une opération de table donnée. Le script est exécuté chaque fois qu'une opération donnée est appelée par une demande REST, par exemple, quand une demande POST est reçue pour insérer un élément dans la table. Mobile Services ne conserve pas l'état entre les exécutions du script. Comme un nouveau contexte global est créé à chaque fois qu'un script est exécuté, l'état des variables définies dans le script est réinitialisé. Si vous souhaitez stocker l'état d'une requête à une autre, créez une table dans votre service mobile, puis lisez et écrivez l'état dans la table. Pour plus d'informations, consultez la page [Procédure : accès aux tables partir des scripts].

Vous écrivez des scripts d'opération de table si vous devez appliquer une logique métier personnalisée lors de l'exécution de l'opération. Par exemple, le script suivant rejette les opérations d'insertion quand la longueur de la chaîne du champ `text` est supérieure à dix caractères :

	function insert(item, user, request) {
	    if (item.text.length > 10) {
	        request.respond(statusCodes.BAD_REQUEST, 
				'Text length must be less than 10 characters');
	    } else {
	        request.execute();
	    }
	}

Une fonction de script de table prend toujours trois arguments.

- Le premier argument varie selon l'opération de table. 

	- Pour les insertions et les mises à jour, il s'agit d'un objet **item**, qui est une représentation JSON de la ligne concernée par l'opération. Cela vous permet d'accéder aux valeurs des colonnes par nom, par exemple, *item.Owner*, où *Owner* est un des noms dans la représentation JSON.
	- Pour une suppression, il s'agit de l'ID de l'enregistrement à supprimer. 
	- Et pour une lecture, il s'agit d'un [objet query] spécifiant l'ensemble de lignes à renvoyer.

- Le deuxième argument est toujours un [objet user][User object] représentant l'utilisateur ayant envoyé la requête.

- Le troisième argument est toujours un [objet request][request object], qui vous permet de contrôler l'exécution de l'opération demandée et la réponse envoyée au client.

Voici les signatures des principales fonctions canoniques pour les opérations de table :

+ [Insertion][insert function] : `function insert (item, user, request) { ... }`
+ [Mise à jour][update function] : `function update (item, user, request) { ... }`
+ [Suppression][delete function] : `function del (id, user, request) { ... }`
+ [Lecture][read function] : `function read (query, user, request) { ... }`

>[AZURE.NOTE]Une fonction enregistrée pour une opération de suppression doit être nommée _del_, car delete est un mot clé réservé en JavaScript.

Chaque script serveur dispose d'une fonction principale et peut avoir des fonctions d'assistance facultatives. Même si un script serveur peut avoir été créé pour une table spécifique, il peut également faire référence à d’autres tables dans la même base de données. Vous pouvez également définir des fonctions communes comme les modules qui sont partagés entre des scripts. Pour plus d'informations, consultez la section [Contrôle du code source et code partagé][Source control, shared code, and helper functions].

###<a name="register-table-scripts"></a>Procédure : inscription de scripts de table

Vous pouvez définir des scripts serveur qui sont enregistrés pour une opération de table d'une des façons suivantes :

+ Dans le [portail Azure Classic]. Les scripts pour les opérations de table sont accessibles sous l'onglet **Scripts** d'une table donnée. Ce qui suit présente le code par défaut enregistré pour le script insert de la table `TodoItem`. Vous pouvez remplacer ce code par votre propre logique métier personnalisée.

	![1][1]
	
	Pour plus d'informations, consultez la page [Validation et modification des données dans Mobile Services à l'aide des scripts serveur].

+ À l'aide du contrôle de code source Quand le contrôle de code source est activé, créez simplement un fichier nommé <em>`<table>`</em>.<em>`<operation>`</em>.js dans le sous-dossier .\\service\\table de votre référentiel git, où <em>`<table>`</em> est le nom de la table et où <em>`<operation>`</em> est l'opération de table enregistrée. Pour plus d'informations, consultez la section [Contrôle du code source et code partagé][Source control, shared code, and helper functions].

+ À partir de l'invite de commandes de l'outil en ligne de commande Azure. Pour plus d'informations, consultez la section [Utilisation de l'outil en ligne de commande].


Un script d'opération de table doit appeler au moins une des fonctions suivantes de l'[objet request] pour que le client reçoive une réponse.
 
+ **Fonction execute**: l'opération est effectuée comme demandé et la réponse standard est renvoyée.
 
+ **Fonction respond**: une réponse personnalisée est renvoyée.

> [AZURE.IMPORTANT] Lorsqu'un script possède un chemin de code dans lequel ni **execute** ni **respond** ne sont appelées, il se peut que l'opération soit sans réponse.

Le script suivant appelle la fonction **execute** pour effectuer l'opération de table demandée par le client :

	function insert(item, user, request) { 
	    request.execute(); 
	}

Dans cet exemple, l'élément est inséré dans la base de données et le code d'état approprié est renvoyé à l'utilisateur.

Quand la fonction **execute** est appelée, l'élément `item`, la [requête][query object] ou la valeur `id` qui a été transmis comme premier argument à la fonction du script est utilisé pour effectuer l'opération. Pour une opération d'insertion, de mise à jour ou de requête, vous pouvez modifier l'élément ou la requête avant d'appeler **execute**:

	function insert(item, user, request) { 
	    item.scriptComment =
			'this was added by a script and will be saved to the database'; 
	    request.execute(); 
	} 
 
	function update(item, user, request) { 
	    item.scriptComment = 
			'this was added by a script and will be saved to the database'; 
	    request.execute(); 
	} 

	function read(query, user, request) { 
		// Only return records for the current user 	    
		query.where({ userid: user.userId}); 
	    request.execute(); 
	}
 
>[AZURE.NOTE]Dans un script de suppression, la modification de valeur de la variable userId fournie n'affecte pas le choix de l'enregistrement à supprimer.

Pour plus d'exemples, consultez les pages [Lire et écrire des données], [Modifier la requête] et [Valider des données].


###<a name="override-response"></a>Procédure : remplacement de la réponse par défaut

Vous pouvez aussi utiliser un script pour implémenter la logique de validation pouvant remplacer le comportement de la réponse par défaut. Si la validation échoue, appelez simplement la fonction **respond** plutôt que la fonction **execute** et écrivez la réponse au client :

	function insert(item, user, request) {
	    if (item.userId !== user.userId) {
	        request.respond(statusCodes.FORBIDDEN, 
	        'You may only insert records with your userId.');
	    } else {
	        request.execute();
	    }
	}

Dans cet exemple, la requête est rejetée quand l'élément inséré n'a pas une propriété `userId` correspondant au `userId` de l'[objet user] qui est fourni pour le client authentifié. Dans ce cas, une opération de base de données (*insert*) ne se produit pas ; une réponse avec un code d'état HTTP 403 et un message d'erreur personnalisé est renvoyée au client. Pour plus d'exemples, consultez la page [Modifier la réponse].

###<a name="override-success"></a>Procédure : remplacement du succès d'execute

Par défaut, dans une opération de table, la fonction **execute** écrit les réponses automatiquement. Toutefois, vous pouvez transmettre deux paramètres facultatifs à la fonction execute pour remplacer le comportement en cas de succès et/ou d'erreur.

En passant un gestionnaire **success** lorsque vous appelez execute, vous pouvez modifier les résultats d'une requête avant de les écrire dans la réponse. L'exemple suivant appelle `execute({ success: function(results) { ... })` pour effectuer des tâches supplémentaires une fois que les données sont lues à partir de la base de données, mais avant que la réponse soit écrite :

	function read(query, user, request) {
	    request.execute({
	        success: function(results) {
	            results.forEach(function(r) {
	                r.scriptComment = 
	                'this was added by a script after querying the database';
	            });
	            request.respond();
	        }
	    });
	}

Lorsque vous fournissez un gestionnaire **success** à la fonction **execute**, vous devez aussi appeler la fonction **respond** dans le gestionnaire **success** pour que le runtime sache que le script est terminé et qu'une réponse peut être écrite. Lorsque vous appelez **respond** sans passer d'argument, Mobile Services génère la réponse par défaut.

>[AZURE.NOTE]Vous pouvez appeler **respond** sans argument pour appeler la réponse par défaut uniquement après avoir appelé la fonction **execute**.
 
###<a name="override-error"></a>Procédure : remplacement de la gestion des erreurs par défaut

La fonction **execute** peut échouer en cas de perte de la connectivité à la base de données, d'un objet non valide ou d'une requête incorrecte. Par défaut, lorsqu'une erreur se produit, les scripts serveur consignent l'erreur et écrivent un résultat d'erreur dans la réponse. Comme Mobile Services fournit une gestion des erreurs par défaut, vous n'avez pas à gérer les erreurs qui peuvent se produire dans le service.

Vous pouvez remplacer la gestion des erreurs par défaut en implémentant une gestion des erreurs explicite si vous souhaitez une action de compensation particulière ou lorsque vous souhaitez utiliser l'objet console global pour écrire des informations détaillées dans le journal. Pour ce faire, fournissez un gestionnaire **error** dans la fonction **execute**:

	function update(item, user, request) { 
	  request.execute({ 
	    error: function(err) { 
	      // Do some custom logging, then call respond. 
	      request.respond(); 
	    } 
	  }); 
	}
 

Lorsque vous fournissez un gestionnaire des erreurs, Mobile Services renvoie un résultat d'erreur au client lorsque la fonction **respond** est appelée.

Vous pouvez aussi fournir un gestionnaire **success** et un gestionnaire **error** si vous le souhaitez.

###<a name="generate-guids"></a>Procédure : génération de valeurs d'ID uniques

Mobile Services prend en charge les valeurs de chaîne personnalisée uniques pour la colonne **id** de la table. Cela permet aux applications d’utiliser des valeurs personnalisées telles que les adresses de messagerie ou des noms d’utilisateur pour l’ID.

Les ID de chaîne fournissent les avantages suivants :

+ Les ID sont générés sans effectuer d’aller-retour vers la base de données.
+ Il est plus facile de fusionner des enregistrements de plusieurs tables ou bases de données.
+ Les valeurs d’ID peuvent mieux s’intégrer à la logique d’une application.

Lorsqu’une valeur d’ID de chaîne n’est pas définie sur un enregistrement inséré, Mobile Services génère une valeur unique pour l’ID. Vous pouvez générer vos propres valeurs d’ID uniques dans les scripts serveur. L’exemple de script ci-dessous génère un GUID personnalisé et lui attribue un nouvel ID d’enregistrement. Il est similaire à la valeur d’ID que génère Mobiles Services si vous n’avez pas transmis de valeur pour un ID d’enregistrement.

	// Example of generating an id. This is not required since Mobile Services
	// will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


Si une application fournit la valeur d’un ID, Mobile Services la stocke en l’état. Les espaces de début et de fin sont également inclus. Ils ne sont pas supprimés de la valeur.

La valeur pour le `id` doit être unique et ne contenir aucun caractère présent dans les ensembles suivants :

+ Caractères de contrôle : [0x0000-0x001F] et [0x007F-0x009F]. Pour plus d'informations, consultez la page [Codes de contrôle ASCII C0 et C1](http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set).
+  Caractères imprimables : **"**(0x0022), **+** (0x002B), **/** (0x002F), **?** (0x003F), **\** (0x005C), **`** (0x0060)
+  Les ID « . » et « .. »

Vous pouvez également utiliser des ID d’entier pour vos tables. Pour pouvoir utiliser un ID d'entier, vous devez créer votre table avec la commande `mobile table create` et l'option `--integerId`. Cette commande s'utilise avec l'interface de ligne de commande (CLI) pour Azure. Pour plus d'informations sur l'utilisation de l'interface de ligne de commande, consultez la page [Interface de ligne de commande pour la gestion des tables Mobile Services](../virtual-machines-command-line-tools.md#Mobile_Tables).


###<a name="access-headers"></a>Procédure : accès aux paramètres personnalisés

Lorsque vous envoyez une requête à votre service mobile, vous pouvez inclure des paramètres personnalisés dans l'URI de la requête pour indiquer à vos scripts d'opération de table comment traiter une requête donnée. Vous modifiez alors votre script pour examiner le paramètre afin de déterminer le chemin du traitement.

Par exemple, l'URI suivant pour une requête POST indique au service de ne pas autoriser l'insertion d'un nouveau *TodoItem* possédant la même valeur de texte :

		https://todolist.azure-mobile.net/tables/TodoItem?duplicateText=false

Ces paramètres de requête personnalisés sont accessibles comme des valeurs JSON à partir de la propriété **parameters** de l'[objet request]. L'objet **request** est fourni par Mobile Services aux fonctions enregistrées avec une opération de table. Le script serveur suivant pour l'opération insert vérifie la valeur du paramètre `duplicateText` avant l'exécution de l'opération insert :

		function insert(item, user, request) {
		    var todoItemTable = tables.getTable('TodoItem');
		    // Check the supplied custom parameter to see if
		    // we should allow duplicate text items to be inserted.		   
		    if (request.parameters.duplicateText === 'false') {
		        // Find all existing items with the same text
		        // and that are not marked 'complete'. 
		        todoItemTable.where({
		            text: item.text,
		            complete: false
		        }).read({
		            success: insertItemIfNotComplete
		        });
		    } else {
		        request.execute();
		    }

		    function insertItemIfNotComplete(existingItems) {
		        if (existingItems.length > 0) {
		            request.respond(statusCodes.CONFLICT, 
                        "Duplicate items are not allowed.");
		        } else {
		            // Insert the item as normal. 
		            request.execute();
		        }
		    }
		}

Notez que dans **insertItemIfNotComplete**, la fonction **execute** de l'[objet request] est appelée pour insérer l'élément s'il n'y a pas de texte dupliqué ; sinon, la fonction **respond** est appelée pour avertir le client du doublon.

Notez la syntaxe de l'appel à la fonction **success** dans le code ci-dessus :

 		        }).read({
		            success: insertItemIfNotComplete
		        });

En JavaScript, c'est une version courte équivalente à la version longue :

		success: function(results) 
		{ 
			insertItemIfNotComplete(results); 
		}


###<a name="work-with-users"></a>Procédure : fonctionnement des utilisateurs

Dans Azure Mobile Services, vous pouvez utiliser un fournisseur d'identité pour authentifier les utilisateurs. Pour plus d'informations, consultez la page [Prise en main de l'authentification]. Lorsqu'un utilisateur authentifié appelle une opération de table, Mobile Services utilise l'[objet user] pour fournir les informations sur l'utilisateur à la fonction de script enregistrée. La propriété **userId** peut être utilisée pour stocker et récupérer des informations spécifiques de l'utilisateur. L’exemple suivant définit la propriété owner d’un élément en fonction de l’élément **userId** d’un utilisateur authentifié :

	function insert(item, user, request) {
	    item.owner = user.userId;
	    request.execute();
	}

L'exemple suivant ajoute un filtre supplémentaire à la requête basée sur l'élément **userId** d'un utilisateur authentifié. Ce filtre restreint les résultats aux éléments appartenant à l'utilisateur actuel :

	function read(query, user, request) {
	    query.where({
	        owner: user.userId
	    });
	    request.execute();
	}

##<a name="custom-api"></a>API personnalisées

Cette section décrit comment créer et utiliser les points de terminaison d’API personnalisées et inclut les sections suivantes :
	
+ [Vue d'ensemble des API personnalisées](#custom-api-overview)
+ [Procédure : définition d'une API personnalisée]
+ [Procédure : implémentation des méthodes HTTP]
+ [Procédure : envoi et réception des données au format XML]
+ [Procédure : fonctionnement des utilisateurs et des en-têtes dans une API personnalisée]
+ [Procédure : définition de plusieurs itinéraires dans une API personnalisée]

###<a name="custom-api-overview"></a>Vue d'ensemble des API personnalisées

Une API personnalisée est un point de terminaison dans votre service mobile qui est accessible par une ou plusieurs méthodes HTTP standard : GET, POST, PUT, PATCH, DELETE. Une exportation de fonction distincte peut être définie pour chaque méthode HTTP prise en charge par l'API personnalisée, le tout dans un seul fichier script. Le script enregistré est appelé lorsqu'une requête pour l'API personnalisée est reçue à l'aide d'une méthode donnée. Pour plus d'informations, consultez la page [API personnalisée].

Lorsque les fonctions de l'API personnalisée sont appelées par le runtime Mobile Services, un objet [request][request object] et un objet [response][response object] sont fournis. Ces objets exposent la fonctionnalité de la [bibliothèque express.js], qui peut être utilisée par vos scripts. L'API personnalisée suivante nommée **hello** est un exemple très simple qui renvoie _Hello, world!_ en réponse à une requête POST :

		exports.post = function(request, response) {
		    response.send(200, "{ message: 'Hello, world!' }");
		} 

La fonction **send** de l'[objet response] renvoie la réponse voulue au client. Ce code est appelé en envoyant une requête POST à l'URL suivante :

		https://todolist.azure-mobile.net/api/hello  

L'état global est conservé entre les exécutions.

###<a name="define-custom-api"></a>Procédure : définition d'une API personnalisée

Vous pouvez définir des scripts serveur qui sont enregistrés pour des méthodes HTTP dans un point de terminaison d'API personnalisée de l'une des façons suivantes :

+ Dans le [portail Azure Classic]. Les scripts de l'API personnalisée sont créés et modifiés dans l'onglet **API**. Le code du script serveur se trouve dans l'onglet **Scripts** d'une API personnalisée donnée. Ce qui suit affiche le script appelé par une requête POST au point de terminaison de l'API personnalisée `CompleteAll`. 

	![2][2]
	
	Les autorisations d'accès aux méthodes de l'API personnalisée sont attribuées dans l'onglet Autorisations. Pour plus d'informations sur la création de cette API personnalisée, consultez la page [Appel d'une API personnalisée à partir du client].

+ À l'aide du contrôle de code source Quand le contrôle de code source est activé, créez simplement un fichier nommé <em>`<custom_api>`</em>.js dans le sous-dossier .\\service\\api de votre référentiel git, où <em>`<custom_api>`</em> est le nom de l'API personnalisée qui est enregistrée. Ce fichier de script contient une fonction _exportée_ pour chaque méthode HTTP exposée par l'API personnalisée. Les autorisations sont définies dans un fichier complément .json. Pour plus d'informations, consultez la section [Contrôle du code source et code partagé][Source control, shared code, and helper functions].

+ À partir de l'invite de commandes de l'outil en ligne de commande Azure. Pour plus d'informations, consultez la section [Utilisation de l'outil en ligne de commande].

###<a name="handle-methods"></a>Procédure : implémentation des méthodes HTTP

Une API personnalisée peut gérer une ou plusieurs des méthodes HTTP (GET, POST, PUT, PATCH et DELETE). Une fonction exportée est définie pour chaque méthode HTTP gérée par l'API personnalisée. Un seul fichier de code d'API personnalisée peut exporter une ou toutes les fonctions suivantes :

		exports.get = function(request, response) { ... };
		exports.post = function(request, response) { ... };
		exports.patch = function(request, response) { ... };
		exports.put = function(request, response) { ... };
		exports.delete = function(request, response) { ... };

Le point de terminaison de l'API personnalisée ne peut pas être appelé à l'aide d'une méthode HTTP qui n'est pas implémentée dans le script serveur, et une réponse indiquant une erreur 405 (Méthode non autorisée) est renvoyée. Des niveaux d'autorisation distincts peuvent être attribués à chaque méthode HTTP de prise en charge.

###<a name="api-return-xml"></a>Procédure : envoi et réception des données au format XML

Lorsque les clients stockent et récupèrent les données, Mobile Services utilise JavaScript Object Notation (JSON) pour représenter les données dans le corps du message. Toutefois, il existe des scénarios dans lesquels vous souhaitez utiliser une charge utile XML. Par exemple, les applications Windows Store intègrent une fonctionnalité de notifications périodiques qui nécessite que le service émette du code XML. Pour plus d'informations, consultez la page [Définition d'une API personnalisée prenant en charge les notifications périodiques].

La fonction de l'API personnalisée **OrderPizza** renvoie un document XML simple comme charge utile de réponse :

		exports.get = function(request, response) {
		  response.set('content-type', 'application/xml');
		  var xml = '<?xml version="1.0"?><PizzaOrderForm><PizzaOrderForm/>';
		  response.send(200, xml);
		};

Cette fonction de l'API personnalisée est appelée par une requête HTTP GET au point de terminaison suivant :

		https://todolist.azure-mobile.net/api/orderpizza

###<a name="get-api-user"></a>Procédure : fonctionnement des utilisateurs et des en-têtes dans une API personnalisée

Dans Azure Mobile Services, vous pouvez utiliser un fournisseur d'identité pour authentifier les utilisateurs. Pour plus d'informations, consultez la page [Prise en main de l'authentification]. Lorsqu'un utilisateur authentifié appelle une API personnalisée, Mobile Services utilise l'[objet user] pour fournir les informations sur l'utilisateur permettant de personnaliser le code de l'API. L'[objet user] est accessible depuis la propriété user de l'[objet request]. La propriété **userId** peut être utilisée pour stocker et récupérer des informations spécifiques de l'utilisateur.

La fonction de l’API personnalisée **OrderPizza** définit la propriété owner d’un élément en fonction de l’élément **userId** d’un utilisateur authentifié :

		exports.post = function(request, response) {
			var userTable = request.service.tables.getTable('user');
			userTable.lookup(request.user.userId, {
				success: function(userRecord) {
					callPizzaAPI(userRecord, request.body, function(orderResult) {
						response.send(201, orderResult);
					});
				}
			});
		
		};

Cette fonction de l'API personnalisée est appelée par une requête HTTP POST au point de terminaison suivant :

		https://<service>.azure-mobile.net/api/orderpizza

Vous pouvez aussi accéder à un en-tête HTTP spécifique à partir de l'[objet request], comme dans le code suivant :

		exports.get = function(request, response) {    
    		var header = request.header('my-custom-header');
    		response.send(200, "You sent: " + header);
		};

Cet exemple simple lit un en-tête personnalisé nommé `my-custom-header`, puis renvoie la valeur dans la réponse.

###<a name="api-routes"></a>Procédure : définition de plusieurs itinéraires dans une API personnalisée

Mobile Services vous permet de définir plusieurs chemins ou itinéraires dans une API personnalisée. Par exemple, des requêtes HTTP GET vers les URL suivantes dans une API personnalisée **calculator** appellent une fonction **add** ou **subtract**, respectivement :

+ `https://<service>.azure-mobile.net/api/calculator/add`
+ `https://<service>.azure-mobile.net/api/calculator/sub`

Plusieurs itinéraires sont définis en exportant une fonction **register**, à qui est transmis un objet **api** (semblable à l'[objet express dans express.js]) qui est utilisé pour enregistrer les itinéraires sous le point de terminaison de l'API personnalisée. L'exemple suivant implémente les méthodes **add** et **sub** dans l'API personnalisée **calculator**:

		exports.register = function (api) {
		    api.get('add', add);
		    api.get('sub', subtract);
		}
		
		function add(req, res) {
		    var result = parseInt(req.query.a) + parseInt(req.query.b);
		    res.send(200, { result: result });
		}
		
		function subtract(req, res) {
		    var result = parseInt(req.query.a) - parseInt(req.query.b);
		    res.send(200, { result: result });
		}

L'objet **api** transmis à la fonction **register** expose une fonction pour chaque méthode HTTP (**get**, **post**, **put**, **patch** et **delete**). Ces fonctions enregistrent un itinéraire sur une fonction définie pour une méthode HTTP spécifique. Chaque fonction prend deux paramètres, le premier étant le nom de l'itinéraire, et le deuxième la fonction enregistrée pour l'itinéraire.

Les deux itinéraires de l'exemple d'API personnalisée ci-dessus peuvent être appelés par des requêtes HTTP GET comme suit (présentés avec la réponse) :

+ `https://<service>.azure-mobile.net/api/calculator/add?a=1&b=2`

		{"result":3}

+ `https://<service>.azure-mobile.net/api/calculator/sub?a=3&b=5`

		{"result":-2}

##<a name="scheduler-scripts"></a>Planificateur de travaux

Mobile Services vous permet de définir des scripts serveur qui sont exécutés comme des travaux selon une planification fixe ou à la demande à partir du portail Azure Classic. Les travaux planifiés sont utiles pour effectuer des tâches périodiques comme le nettoyage des données de table et le traitement par lots. Pour plus d'informations, consultez la page [Planifier les travaux].

Les scripts qui sont enregistrés pour des travaux planifiés ont une fonction principale avec le même nom que le travail planifié. Étant donné qu'un script planifié n'est pas appelé par une requête HTTP, aucun contexte ne peut être transmis au runtime du serveur et la fonction ne prend pas de paramètre. Comme d'autres types de scripts, vous pouvez avoir des fonctions de sous-routine et des modules partagés. Pour plus d'informations, consultez la section [Contrôle du code source, code partagé et fonctions d'assistance].

###<a name="scheduler-scripts"></a>Procédure : définition de scripts de travail planifié

Un script serveur peut être attribué à un travail qui est défini dans le planificateur Mobile Services. Ces scripts appartiennent au travail et sont exécutés selon la planification du travail. (Vous pouvez aussi utiliser le [portail Azure Classic] pour exécuter des tâches à la demande.) Un script définissant un travail planifié n'a pas de paramètre, car Mobile Services ne lui transmet aucune donnée ; il est exécuté comme une fonction JavaScript régulière et n'interagit pas directement avec Mobile Services.

Vous définissez des travaux planifiés de l'une des façons suivantes :

+ Dans le [portail Azure Classic], sous l’onglet **Script** dans le planificateur :

	![3][3]

	Pour plus d'informations, consultez la page [Planification des tâches du backend dans Mobile Services].

+ À partir de l'invite de commandes de l'outil en ligne de commande Azure. Pour plus d'informations, consultez la section [Utilisation de l'outil en ligne de commande].

>[AZURE.NOTE]Lorsque le contrôle du code source est activé, vous pouvez modifier les fichiers de script du travail planifié directement dans le sous-dossier .\\service\\scheduler de votre référentiel git. Pour plus d'informations, consultez [Procédure : partage de code à l'aide du contrôle du code source].

##<a name="shared-code"></a>Contrôle du code source, code partagé et fonctions d'assistance

Cette section vous montre comment tirer parti du contrôle de code source pour ajouter vos propres modules node.js personnalisés, votre code partagé et toute autre stratégie de réutilisation de code, y compris les sections suivantes :

+ [Vue d'ensemble de l'utilisation de code partagé](#leverage-source-control)
+ [Procédure : chargement des modules Node.js]
+ [Procédure : utilisation des fonctions d'assistance]
+ [Procédure : partage de code à l'aide du contrôle du code source]
+ [Procédure : utilisation des paramètres d'application] 

###<a name="leverage-source-control"></a>Vue d'ensemble de l'utilisation de code partagé

Comme Mobile Services utilise Node.js sur le serveur, vos scripts ont déjà accès aux modules Node.js intégrés. Vous pouvez aussi utiliser le contrôle du code source pour définir vos propres modules ou ajouter d'autres modules Node.js à votre service.

Voici une partie des modules les plus utiles pouvant être utilisés dans vos scripts à l'aide de la fonction globale **require**:

+ **azure**: expose la fonctionnalité du Kit de développement logiciel (SDK) Azure pour Node.js. Pour plus d'informations, consultez la page [Kit de développement logiciel (SDK) Azure pour Node.js]. 
+ **crypto**: fournit la fonctionnalité crypto d'OpenSSL. Pour plus d'informations, consultez la [documentation Node.js][crypto API].
+ **path**: contient des utilitaires pour utiliser les chemins de fichier. Pour plus d'informations, consultez la [documentation Node.js][path API].
+ **querystring**: contient des utilitaires pour utiliser les chaînes de requête. Pour plus d'informations, consultez la [documentation Node.js][querystring API].
+ **request**: envoie des requêtes HTTP aux services REST externes, comme Twitter et Facebook. Pour plus d'informations, consultez la page [Envoyer une requête HTTP].
+ **sendgrid**: envoie le courrier électronique à l'aide du service de messagerie Sendgrid dans Azure. Pour plus d'informations, consultez la page [Envoi de courrier électronique à partir de Mobile Services avec SendGrid].
+ **url**: contient des utilitaires pour analyser et résoudre les URL. Pour plus d'informations, consultez la [documentation Node.js][url API].
+ **util**: contient divers utilitaires, comme le formatage de chaînes et la vérification du type d'objet. Pour plus d'informations, consultez la [documentation Node.js][util API]. 
+ **zlib**: expose la fonctionnalité de compression, comme gzip et deflate. Pour plus d'informations, consultez la [documentation Node.js][zlib API]. 

###<a name="modules-helper-functions"></a>Procédure : utilisation des modules

Mobile Services expose un ensemble de modules que les scripts peuvent charger à l'aide de la fonction globale **require**. Par exemple, un script peut demander **request** pour effectuer des requêtes HTTP :

	function update(item, user, request) { 
	    var httpRequest = require('request'); 
	    httpRequest('http://www.google.com', function(err, response, body) { 
	    	... 
	    }); 
	} 


###<a name="shared-code-source-control"></a>Procédure : partage de code à l'aide du contrôle du code source

Vous pouvez utiliser le contrôle du code source avec le gestionnaire de package Node.js (npm) pour contrôler la disponibilité des modules pour votre service mobile. Il existe deux façons d'effectuer cette opération :

+ Pour les modules qui sont publiés et installés par npm, utilisez le fichier package.json pour déclarer quels packages vous souhaitez installer pour votre service mobile. Ainsi, votre service a toujours accès à la dernière version des packages requis. Le fichier package.json se trouve dans le répertoire `.\service`. Pour plus d'informations, consultez la page [Prise en charge de package.json dans Azure Mobile Services].

+ Pour les modules privés ou personnalisés, vous pouvez utiliser npm pour installer manuellement le module dans le répertoire `.\service\node_modules` de votre contrôle du code source. Pour un exemple de chargement manuel d'un module, consultez la page [Utilisation du code partagé et des modules Node.js dans vos scripts serveur].

	>[AZURE.NOTE]Quand `node_modules` existe déjà dans la hiérarchie de répertoires, NPM crée le sous-répertoire `\node-uuid` plutôt que de créer un `node_modules` dans le référentiel. Dans ce cas, supprimez simplement le répertoire `node_modules` existant.

Après avoir validé le fichier package.json ou les modules personnalisés sur le référentiel pour votre service mobile, utilisez **require** pour référencer les modules par nom.

>[AZURE.NOTE] Les modules que vous spécifiez dans package.json ou que vous chargez sur votre service mobile sont uniquement utilisés dans le code de votre script serveur. Ils ne sont pas utilisés par le runtime Mobile Services.

###<a name="helper-functions"></a>Procédure : utilisation des fonctions d'assistance

En plus de nécessiter des modules, chaque script serveur peut inclure des fonctions d'assistance. Ce sont des fonctions qui sont séparées de la fonction principale et peuvent être utilisées pour factoriser le code du script.

Dans l'exemple suivant, un script de table est enregistré pour une opération insert, qui inclut la fonction d'assistance **handleUnapprovedItem**:


	function insert(item, user, request) {
	    if (!item.approved) {
	        handleUnapprovedItem(item, user, request);
	    } else {
	        request.execute();
	    }
	}
	
	function handleUnapprovedItem(item, user, request) {
	    // Do something with the supplied item, user, or request objects.
	}
 
Dans un script, les fonctions d'assistance doivent être déclarées après la fonction principale. Vous devez déclarer toutes les variables dans votre script. Les variables non déclarées provoquent une erreur.

Les fonctions d'assistance peuvent aussi être définies une fois et partagées entre les scripts serveur. Pour partager une fonction entre des scripts, les fonctions doivent être exportées et le fichier de script doit exister dans le répertoire `.\service\shared`. Ce qui suit est un modèle d'exportation d'une fonction partagée dans un fichier `.\services\shared\helpers.js` :

		exports.handleUnapprovedItem = function (tables, user, callback) {
		    
		    // Do something with the supplied tables or user objects and 
			// return a value to the callback function.
		};
 
Vous pouvez alors utiliser une fonction semblable dans un script d'opération de table :

		function insert(item, user, request) {
		    var helper = require('../shared/helper');
		    helper.handleUnapprovedItem(tables, user, function(result) {
		        	
					// Do something based on the result.
		            request.execute();
		        }
		    }
		}

Dans cet exemple, vous devez passer un [objet tables] et un [objet user] à la fonction partagée. Ceci est dû au fait que les scripts partagés ne peuvent pas accéder à l'[objet tables] global et que l'[objet user] existe uniquement dans le contexte d'une requête.

Les fichiers de script sont chargés sur le répertoire partagé à l'aide du [contrôle du code source][How to: Share code by using source control] ou de l'[outil en ligne de commande][Using the command line tool].

###<a name="app-settings"></a>Procédure : utilisation des paramètres d'application

Mobile Services vous permet de stocker en toute sécurité les valeurs en tant que paramètres d'application qui seront accessibles par vos scripts serveur au moment de l'exécution. Lorsque vous ajoutez des données aux paramètres d'application de votre service mobile, les paires nom/valeur sont stockées de manière chiffrée et vous pouvez y accéder dans vos scripts serveur sans les coder en dur dans votre fichier de script. Pour plus d’informations, consultez la page [Paramètres de l’application].

L'exemple d'API personnalisée suivant utilise l'[objet service] fourni pour récupérer la valeur du paramètre d'application.

		exports.get = function(request, response) {
		
			// Get the MY_CUSTOM_SETTING value from app settings.
		    var customSetting = 
		        request.service.config.appSettings.my_custom_setting;
				
			// Do something and then send a response.

		}

Le code suivant utilise le module de configuration pour récupérer les valeurs de jeton d'accès Twitter stockées dans les paramètres d'application et qui sont utilisées dans un script de travail planifié :

		// Get the service configuration module.
		var config = require('mobileservice-config');

		// Get the stored Twitter consumer key and secret. 
		var consumerKey = config.twitterConsumerKey,
		    consumerSecret = config.twitterConsumerSecret
		// Get the Twitter access token from app settings.    
		var accessToken= config.appSettings.TWITTER_ACCESS_TOKEN,
		    accessTokenSecret = config.appSettings.TWITTER_ACCESS_TOKEN_SECRET;

Notez que ce code récupère également les valeurs de la clé Twitter stockée sous l'onglet **Identité** du portail. Comme un **objet config** n'est pas disponible dans les scripts de l'opération de la table et du travail planifié, le module de configuration est requis pour accéder aux paramètres d'application. Pour obtenir un exemple complet, consultez la page [Planification des tâches du backend dans Mobile Services].

<h2><a name="command-prompt"></a>Utilisation de l'outil en ligne de commande</h2>

Dans Mobile Services, vous pouvez créer, modifier et supprimer des scripts serveur en utilisant l'outil en ligne de commande Azure. Avant de charger vos scripts, assurez-vous que vous utilisez la structure de répertoires suivante :

![4][4]

Notez que cette structure de répertoires est identique au référentiel git lors de l'utilisation du contrôle du code source.

Pendant le chargement de fichiers de script à partir de l'outil en ligne de commande, vous devez d'abord accéder au répertoire `.\services`. La commande suivante charge un script nommé `todoitem.insert.js` à partir du sous-répertoire `table` :

		~$azure mobile script upload todolist table/todoitem.insert.js
		info:    Executing command mobile script upload
		info:    mobile script upload command OK

La commande suivante renvoie des informations sur chaque fichier de script conservé dans votre service mobile :

		~$ azure mobile script list todolist
		info:    Executing command mobile script list
		+ Retrieving script information
		info:    Table scripts
		data:    Name                       Size
		data:    -------------------------  ----
		data:    table/channels.insert      1980
		data:    table/TodoItem.insert      5504
		data:    table/TodoItem.read        64
		info:    Shared scripts
		data:    Name              Size
		data:    ----------------  ----
		data:    shared/helper.js  62
		data:    shared/uuid.js    7452
		info:    Scheduled job scripts
		data:    Job name    Script name           Status    Interval     Last run  Next run
		data:    ----------  --------------------  --------  -----------  --------  --------
		data:    getUpdates  scheduler/getUpdates  disabled  15 [minute]  N/A       N/A
		info:    Custom API scripts
		data:    Name                    Get          Put          Post         Patch        Delete
		data:    ----------------------  -----------  -----------  -----------  -----------  -----------
		data:    completeall             application  application  application  application  application
		data:    register_notifications  application  application  user         application  application
		info:    mobile script list command OK

Pour plus d'informations, consultez la page [Commandes pour gérer Azure Mobile Services].

##<a name="working-with-tables"></a>Utilisation des tables

Cette section détaille les stratégies permettant de travailler directement avec les données de table de base de données SQL, y compris les sections suivantes :

+ [Vue d'ensemble de l'utilisation de tables](#overview-tables)
+ [Procédure : accès aux tables à partir des scripts]
+ [Procédure : insertions en bloc]
+ [Procédure : mappage des types JSON vers des types de base de données]
+ [Accès aux tables à l'aide de Transact-SQL]

###<a name="overview-tables"></a>Vue d'ensemble de l'utilisation de tables

De nombreux scénarios dans Mobile Services nécessitent des scripts serveur pour accéder aux tables dans la base de données. Par exemple, comme Mobile Services ne conserve pas l'état entre les exécutions de script, les données devant être persistantes entre les exécutions de script doivent être stockées dans des tables. Vous pouvez examiner les entrées dans une table d'autorisations ou stocker les données d'audit au lieu d'écrire simplement sur le journal dans lequel les données ont une durée limitée et ne sont pas accessibles par programme.

Il existe deux moyens d'accéder aux tables dans Mobile Services, soit en utilisant un proxy d'[objet table], soit en composant des requêtes Transact-SQL à l'aide de l'[objet mssql]. L'[objet table] facilite l'accès aux données des tables à partir de votre code de script serveur, mais l'[objet mssql] prend en charge les opérations de données plus complexes et offre le plus de flexibilité.

###<a name="access-tables"></a>Procédure : accès aux tables à partir des scripts

Le moyen le plus simple d'accéder aux tables à partir de votre script consiste à utiliser l'[objet tables]. La fonction **getTable** renvoie une instance d'[objet table] qui est un proxy pour accéder à la table demandée. Vous pouvez ensuite appeler des fonctions sur le proxy pour accéder aux données et les modifier.

Les scripts enregistrés sur les opérations de table et les travaux planifiés peuvent accéder à l'[objet tables] en tant qu'objet global. Cette ligne de code permet d'obtenir un proxy pour la table *TodoItems* à partir de l'[objet tables] global :

		var todoItemsTable = tables.getTable('TodoItems');

Les scripts d'API personnalisée peuvent accéder à l'[objet tables] à partir de la propriété <strong>service</strong> de l'[objet request] fourni. Cette ligne de code permet d'obtenir un [objet tables] à partir de la requête :

		var todoItemsTable = request.service.tables.getTable('TodoItem');

> [AZURE.NOTE] Les fonctions partagées ne peuvent pas accéder à l'objet **tables** directement. Dans une fonction partagée, vous devez passer l'objet tables à la fonction.

Une fois que vous avez un [objet table], vous pouvez appeler une ou plusieurs fonctions d'opération de table : insert, update, delete ou read. Dans cet exemple, les autorisations de l'utilisateur sont lues à partir de la table permissions :

	function insert(item, user, request) {
		var permissionsTable = tables.getTable('permissions');
	
		permissionsTable
			.where({ userId: user.userId, permission: 'submit order'})
			.read({ success: checkPermissions });
			
		function checkPermissions(results) {
			if(results.length > 0) {
				// Permission record was found. Continue normal execution.
				request.execute();
			} else {
				console.log('User %s attempted to submit an order without permissions.', user.userId);
				request.respond(statusCodes.FORBIDDEN, 'You do not have permission to submit orders.');
			}
		}
	}

L'exemple suivant écrit des informations d'audit dans une table **audit**:

	function update(item, user, request) {
		request.execute({ success: insertAuditEntry });
		
		function insertAuditEntry() {
			var auditTable = tables.getTable('audit');
			var audit = {
				record: 'checkins',
				recordId: item.id,
				timestamp: new Date(),
				values: JSON.stringify(item)
			};
			auditTable.insert(audit, {
				success: function() {
					// Write to the response now that all data operations are complete
					request.respond();
				}
			});
		}
	}

Cet exemple de code constitue un autre exemple : [Procédure : accès aux paramètres personnalisés][How to: Add custom parameters].

###<a name="bulk-inserts"></a>Procédure : insertions en bloc

Si vous utilisez une boucle **for** ou **while** pour insérer directement un grand nombre d'éléments (1 000, par exemple) dans une table, il se peut que vous rencontriez une limite de connexion SQL déclenchant l'échec de certaines insertions. Votre requête peut ne jamais se terminer ou elle peut renvoyer une erreur interne du serveur HTTP 500. Pour éviter ce problème, vous pouvez insérer les éléments par lot de 10 par exemple. Après l'insertion du premier lot, soumettez le batch suivant, et ainsi de suite.

Le script suivant vous permet de définir la taille d'un lot d'enregistrements à insérer en parallèle. Il est recommandé de conserver un petit nombre d'enregistrements. La fonction **insertItems** s'appelle de manière récursive lorsqu'une insertion de lot asynchrone a été effectuée. À la fin, la boucle for insère un enregistrement à la fois et appelle **insertComplete** en cas de succès et **errorHandler** en cas d'erreur. **insertComplete** contrôle si **insertItems** est appelé de manière récursive pour le lot suivant ou si le travail est effectué, auquel cas le script doit s'arrêter.

		var todoTable = tables.getTable('TodoItem');
		var recordsToInsert = 1000;
		var batchSize = 10; 
		var totalCount = 0;
		var errorCount = 0; 
		
		function insertItems() {        
		    var batchCompletedCount = 0;  
		
		    var insertComplete = function() { 
		        batchCompletedCount++; 
		        totalCount++; 
		        if(batchCompletedCount === batchSize || totalCount === recordsToInsert) {                        
		            if(totalCount < recordsToInsert) {
		                // kick off the next batch 
		                insertItems(); 
		            } else { 
		                // or we are done, report the status of the job 
		                // to the log and don't do any more processing 
		                console.log("Insert complete. %d Records processed. There were %d errors.", totalCount, errorCount); 
		            } 
		        } 
		    }; 
		
		    var errorHandler = function(err) { 
		        errorCount++; 
		        console.warn("Ignoring insert failure as part of batch.", err); 
		        insertComplete(); 
		    };
		
		    for(var i = 0; i < batchSize; i++) { 
		        var item = { text: "This is item number: " + totalCount + i }; 
		        todoTable.insert(item, { 
		            success: insertComplete, 
		            error: errorHandler 
		        }); 
		    } 
		} 
		
		insertItems(); 


Vous trouverez l'intégralité de cet exemple de code accompagné de sa discussion dans ce [billet de blog](http://blogs.msdn.com/b/jpsanders/archive/2013/03/20/server-script-to-insert-table-items-in-windows-azure-mobile-services.aspx). Si vous utilisez ce code, vous pouvez l'adapter à votre situation particulière et le tester de manière approfondie.

###<a name="JSON-types"></a>Procédure : mappage des types JSON vers des types de base de données

Les collections de types de données sont différentes sur le client et dans une table de base de données Mobile Services. À certains moments, elles sont mises facilement en correspondance l'une avec l'autre, et à d'autres moments non. Mobile Services effectue de nombreuses transformations de types dans le mappage :

- Les types propres au langage du client sont sérialisés en JSON.
- La représentation JSON est traduite en JavaScript avant d'apparaître dans les scripts serveur.
- Les types de données JavaScript sont convertis en types de base de données SQL lors de l'enregistrement à l'aide de l'[objet tables].

La transformation à partir du schéma client en JSON varie selon les plateformes. JSON.NET est utilisé dans les clients Windows Phone et Windows Store. Le client Android utilise la bibliothèque gson. Le client iOS utilise la classe NSJSONSerialization. Le comportement de sérialisation par défaut de chacune de ces bibliothèques est utilisé, sauf que les objets date sont convertis en chaînes JSON contenant la date encodée à l'aide de la norme ISO 8601.

Lorsque vous écrivez des scripts serveur utilisant les fonctions [insert], [update], [read] ou [delete], vous pouvez accéder à la représentation JavaScript de vos données. Mobile Services utilise la fonction de désérialisation de Node.js ([JSON.parse](http://es5.github.io/#x15.12)) pour transformer JSON simultanément en objets JavaScript. Toutefois, Mobile Services effectue une transformation pour extraire les objets **Date** à partir des chaînes ISO 8601.

Lorsque vous utilisez l'[objet tables] ou l'[objet mssql] ou pour simplement laisser vos scripts de table s'exécuter, les objets JavaScript désérialisés sont insérés dans une base de données SQL. Dans ce processus, les propriétés des objets sont mappées aux types T-SQL :

Propriété JavaScript|Type T-SQL
---|---
Number|Float(53)
Boolean|Bit
Date|DateTimeOffset(3)|
String|Nvarchar(max)
Buffer|Non pris en charge
Object|Non pris en charge
Array|Non pris en charge
Stream|Non pris en charge

###<a name="TSQL"></a>Accès aux tables à l'aide de Transact-SQL

Le moyen le plus simple d'utiliser les données de table à partir de scripts serveur consiste à utiliser un proxy d'[objet table]. Toutefois, il existe des scénarios plus avancés qui ne sont pas pris en charge par l'[objet table], comme les requêtes de jonction et d'autres requêtes complexes ainsi que l'appel de procédures stockées. Dans ces cas-là, vous devez exécuter des instructions Transact-SQL directement sur la table relationnelle à l'aide de l'[objet mssql]. Cet objet fournit les fonctions suivantes :

- **query**: exécute une requête, spécifiée par une chaîne TSQL ; les résultats sont renvoyés au rappel **success** sur l'objet **options**. La requête peut inclure des paramètres si le paramètre *params* est présent.
- **queryRaw**: identique à *query*, à l'exception que le jeu de résultats renvoyé à partir de la requête est dans un format « brut » (voir l'exemple ci-dessous).
- **open**: utilisé pour obtenir une connexion à partir de votre base de données Mobile Services. Vous pouvez ensuite utiliser l'objet de connexion pour appeler les opérations de base de données comme les transactions.

Ces méthodes vous donnent progressivement un meilleur contrôle de bas niveau sur le traitement de la requête.

+ [Procédure : exécution d'une requête statique]
+ [Procédure : exécution d'une requête dynamique]
+ [Procédure : jonction des tables relationnelles]
+ [Procédure : exécution d'une requête renvoyant des résultats *bruts*]
+ [Procédure : obtention de l'accès à une connexion de base de données]	

####<a name="static-query"></a>Procédure : exécution d'une requête statique

La requête suivante n'a pas de paramètre et renvoie trois enregistrements à partir de la table `statusupdate`. L'ensemble de lignes est au format JSON standard.

		mssql.query('select top 3 * from statusupdates', {
		    success: function(results) {
		        console.log(results);
		    },
            error: function(err) {
                console.log("error is: " + err);
			}
		});


####<a name="dynamic-query"></a>Procédure : exécution d'une requête dynamique paramétrable

L'exemple suivant implémente l'autorisation personnalisée par des autorisations de lecture pour chaque utilisateur à partir de la table permissions. L'espace réservé (?) est remplacé par le paramètre fourni lors de l'exécution de la requête.

		    var sql = "SELECT _id FROM permissions WHERE userId = ? AND permission = 'submit order'";
		    mssql.query(sql, [user.userId], {
		        success: function(results) {
		            if (results.length > 0) {
		                // Permission record was found. Continue normal execution. 
		                request.execute();
		            } else {
		                console.log('User %s attempted to submit an order without permissions.', user.userId);
		                request.respond(statusCodes.FORBIDDEN, 'You do not have permission to submit orders.');
		            }
		        },
            	error: function(err) {
                	console.log("error is: " + err);
				}	
		    });


####<a name="joins"></a>Procédure : jonction des tables relationnelles

Vous pouvez joindre deux tables en utilisant la méthode **query** de l'[objet mssql] à passer dans le code TSQL qui implémente la jonction. Supposons qu'il y ait quelques éléments dans la table **ToDoItem** et que chacun d'eux ait une propriété **priority** qui corresponde à une colonne dans la table. Un élément peut ressembler à ceci :

		{ text: 'Take out the trash', complete: false, priority: 1}

Supposons également qu'il y ait une table supplémentaire appelée **Priority** avec des lignes contenant un **number** (ordre) de priorité et une **description** sous forme de texte. Par exemple, l'ordre de priorité 1 peut avoir « Critique » pour description et l'objet ressemble à ceci :

		{ number: 1, description: 'Critical'}

Nous pouvons maintenant remplacer l'ordre de **priority** dans notre élément par la description texte de l'ordre de priorité. Nous faisons cela avec une jonction relationnelle des deux tables.

		mssql.query('SELECT t.text, t.complete, p.description FROM ToDoItem as t INNER JOIN Priority as p ON t.priority = p.number', {
			success: function(results) {
				console.log(results);
			},
            error: function(err) {
                console.log("error is: " + err);
		});
	
Le script joint les deux tables et écrit les résultats dans le journal. Les objets en résultant peuvent ressembler à ceci :

		{ text: 'Take out the trash', complete: false, description: 'Critical'}


####<a name="raw"></a>Procédure : exécution d'une requête renvoyant des résultats *bruts*

Cet exemple exécute la requête comme avant, mais renvoie le jeu de résultats au format « brut » qui requiert de l'analyser, ligne par ligne et colonne par colonne. Un exemple de scénario possible serait que vous ayez besoin d'accéder à des types de données non pris en charge par Mobile Services. Ce code écrit simplement la sortie dans le journal de la console pour que vous puissiez inspecter le format brut.

		mssql.queryRaw('SELECT * FROM ToDoItem', {
		    success: function(results) {
		        console.log(results);
		    },
            error: function(err) {
                console.log("error is: " + err);
			}
		});

Voici la sortie apparaissant suite à l'exécution de cette requête. Elle contient des métadonnées sur chaque colonne dans la table, suivies d'une représentation des lignes et des colonnes.

		{ meta: 
		   [ { name: 'id',
		       size: 19,
		       nullable: false,
		       type: 'number',
		       sqlType: 'bigint identity' },
		     { name: 'text',
		       size: 0,
		       nullable: true,
		       type: 'text',
		       sqlType: 'nvarchar' },
		     { name: 'complete',
		       size: 1,
		       nullable: true,
		       type: 'boolean',
		       sqlType: 'bit' },
		     { name: 'priority',
		       size: 53,
		       nullable: true,
		       type: 'number',
		       sqlType: 'float' } ],
		  rows: 
		   [ [ 1, 'good idea for the future', null, 3 ],
		     [ 2, 'this is important but not so much', null, 2 ],
		     [ 3, 'fix this bug now', null, 0 ],
		     [ 4, 'we need to fix this one real soon now', null, 1 ],
		   ] }

####<a name="connection"></a>Procédure : obtention de l'accès à une connexion de base de données

Vous pouvez utiliser la méthode **open** pour accéder à la connexion de base de données. Une raison de le faire serait que vous deviez utiliser des transactions de base de données.

La bonne exécution de la méthode **open** entraîne la transmission de la connexion de base de données en paramètre de la fonction **success**. Vous pouvez appeler l'une des fonctions suivantes sur l'objet **connection**: *close*, *queryRaw*, *query*, *beginTransaction*, *commit* et *rollback*.

		    mssql.open({
		        success: function(connection) {
		            connection.query(//query to execute);
		        },
	            error: function(err) {
	                console.log("error is: " + err);
				}
		    });

##<a name="debugging"></a>Débogage et résolution des problèmes

Écrire dans le journal de service est le moyen principal de déboguer et de résoudre les problèmes des scripts serveur. Par défaut, Mobile Services écrit des erreurs qui se produisent pendant l'exécution de script de service dans les journaux de service. Vos scripts peuvent également écrire dans les journaux. L'écriture dans les journaux est un excellent moyen de déboguer vos scripts et de valider le comportement souhaité.

###<a name="write-to-logs"></a>Procédure : écriture de la sortie dans les journaux de service mobile

Pour écrire dans les journaux, utilisez l'[objet console] global. Utilisez la fonction **log** ou **info** pour journaliser les avertissements de niveau des informations. Les fonctions **warning** et **error** journalisent leurs niveaux respectifs qui sont appelés dans les journaux.

> [AZURE.NOTE] Pour afficher les journaux de votre service mobile, connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/), sélectionnez votre service mobile, puis choisissez l’onglet **Journaux**.

Vous pouvez aussi utiliser les fonctions de journalisation de l'[objet console] pour formater vos messages à l'aide de paramètres. L'exemple suivant fournit un objet JSON en tant que paramètre à la chaîne du message :

	function insert(item, user, request) {
	    console.log("Inserting item '%j' for user '%j'.", item, user);  
	    request.execute();
	}

Remarquez que la chaîne `%j` est utilisée en tant qu'espace réservé pour un objet JSON et que les paramètres sont fournis dans l'ordre séquentiel.

Pour éviter de surcharger votre journal, il est conseillé de supprimer ou de désactiver les appels à console.log() qui ne sont pas nécessaires pour l'utilisation en production.

<!-- Anchors. -->
[Introduction]: #intro
[Table operations]: #table-scripts
[Basic table operations]: #basic-table-ops
[Procédure : enregistrement pour les opérations de table]: #register-table-scripts
[How to: Define table scripts]: #execute-operation
[Procédure : remplacement de la réponse par défaut]: #override-response
[How to: Modify an operation]: #modify-operation
[How to: Override success and error]: #override-success-error
[Procédure : remplacement du succès d'execute]: #override-success
[Procédure : remplacement de la gestion des erreurs par défaut]: #override-error
[Procédure : accès aux tables partir des scripts]: #access-tables
[Procédure : accès aux tables à partir des scripts]: #access-tables
[How to: Add custom parameters]: #access-headers
[Procédure : ajout de paramètres personnalisés]: #access-headers
[How to: Work with users]: #work-with-users
[How to: Define scheduled job scripts]: #scheduler-scripts
[How to: Refine access to tables]: #authorize-tables
[Accès aux tables à l'aide de Transact-SQL]: #TSQL
[Procédure : exécution d'une requête statique]: #static-query
[Procédure : exécution d'une requête dynamique]: #dynamic-query
[Procédure : exécution d'une requête renvoyant des résultats *bruts*]: #raw
[Procédure : obtention de l'accès à une connexion de base de données]: #connection
[Procédure : jonction des tables relationnelles]: #joins
[Procédure : insertions en bloc]: #bulk-inserts
[Procédure : mappage des types JSON vers des types de base de données]: #JSON-types
[Procédure : chargement des modules Node.js]: #modules-helper-functions
[How to: Write output to the mobile service logs]: #write-to-logs
[Source control, shared code, and helper functions]: #shared-code
[Contrôle du code source, code partagé et fonctions d'assistance]: #shared-code
[Using the command line tool]: #command-prompt
[Utilisation de l'outil en ligne de commande]: #command-prompt
[Working with tables]: #working-with-tables
[Custom API anchor]: #custom-api
[Procédure : définition d'une API personnalisée]: #define-custom-api
[How to: Share code by using source control]: #shared-code-source-control
[Procédure : partage de code à l'aide du contrôle du code source]: #shared-code-source-control
[Procédure : utilisation des fonctions d'assistance]: #helper-functions
[Debugging and troubleshooting]: #debugging
[Procédure : implémentation des méthodes HTTP]: #handle-methods
[Procédure : fonctionnement des utilisateurs et des en-têtes dans une API personnalisée]: #get-api-user
[How to: Access custom API request headers]: #get-api-headers
[Job Scheduler]: #scheduler-scripts
[Procédure : définition de plusieurs itinéraires dans une API personnalisée]: #api-routes
[Procédure : envoi et réception des données au format XML]: #api-return-xml
[Procédure : utilisation des paramètres d'application]: #app-settings

[1]: ./media/mobile-services-how-to-use-server-scripts/1-mobile-insert-script-users.png
[2]: ./media/mobile-services-how-to-use-server-scripts/2-mobile-custom-api-script.png
[3]: ./media/mobile-services-how-to-use-server-scripts/3-mobile-schedule-job-script.png
[4]: ./media/mobile-services-how-to-use-server-scripts/4-mobile-source-local-cli.png

<!-- URLs. -->
[Référence de script serveur Mobile Services]: http://msdn.microsoft.com/library/windowsazure/jj554226.aspx
[Planification des tâches du backend dans Mobile Services]: /develop/mobile/tutorials/schedule-backend-tasks/
[request object]: http://msdn.microsoft.com/library/windowsazure/jj554218.aspx
[objet request]: http://msdn.microsoft.com/library/windowsazure/jj554218.aspx
[response object]: http://msdn.microsoft.com/library/windowsazure/dn303373.aspx
[objet response]: http://msdn.microsoft.com/library/windowsazure/dn303373.aspx
[User object]: http://msdn.microsoft.com/library/windowsazure/jj554220.aspx
[objet user]: http://msdn.microsoft.com/library/windowsazure/jj554220.aspx
[push object]: http://msdn.microsoft.com/library/windowsazure/jj554217.aspx
[insert function]: http://msdn.microsoft.com/library/windowsazure/jj554229.aspx
[insert]: http://msdn.microsoft.com/library/windowsazure/jj554229.aspx
[update function]: http://msdn.microsoft.com/library/windowsazure/jj554214.aspx
[delete function]: http://msdn.microsoft.com/library/windowsazure/jj554215.aspx
[read function]: http://msdn.microsoft.com/library/windowsazure/jj554224.aspx
[update]: http://msdn.microsoft.com/library/windowsazure/jj554214.aspx
[delete]: http://msdn.microsoft.com/library/windowsazure/jj554215.aspx
[read]: http://msdn.microsoft.com/library/windowsazure/jj554224.aspx
[query object]: http://msdn.microsoft.com/library/windowsazure/jj613353.aspx
[objet query]: http://msdn.microsoft.com/library/windowsazure/jj613353.aspx
[apns object]: http://msdn.microsoft.com/library/windowsazure/jj839711.aspx
[mpns object]: http://msdn.microsoft.com/library/windowsazure/jj871025.aspx
[wns object]: http://msdn.microsoft.com/library/windowsazure/jj860484.aspx
[objet table]: http://msdn.microsoft.com/library/windowsazure/jj554210.aspx
[objet tables]: http://msdn.microsoft.com/library/windowsazure/jj614364.aspx
[objet mssql]: http://msdn.microsoft.com/library/windowsazure/jj554212.aspx
[objet console]: http://msdn.microsoft.com/library/windowsazure/jj554209.aspx
[Lire et écrire des données]: http://msdn.microsoft.com/library/windowsazure/jj631640.aspx
[Valider des données]: http://msdn.microsoft.com/library/windowsazure/jj631638.aspx
[Modifier la requête]: http://msdn.microsoft.com/library/windowsazure/jj631635.aspx
[Modifier la réponse]: http://msdn.microsoft.com/library/windowsazure/jj631631.aspx
[portail Azure Classic]: https://manage.windowsazure.com/
[Planifier les travaux]: http://msdn.microsoft.com/library/windowsazure/jj860528.aspx
[Validation et modification des données dans Mobile Services à l'aide des scripts serveur]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/
[Commandes pour gérer Azure Mobile Services]: ../virtual-machines-command-line-tools.md#Mobile_Scripts
[Windows Store Push]: /develop/mobile/tutorials/get-started-with-push-dotnet/
[Windows Phone Push]: /develop/mobile/tutorials/get-started-with-push-wp8/
[iOS Push]: /develop/mobile/tutorials/get-started-with-push-ios/
[Android Push]: /develop/mobile/tutorials/get-started-with-push-android/
[Kit de développement logiciel (SDK) Azure pour Node.js]: http://go.microsoft.com/fwlink/p/?LinkId=275539
[Envoyer une requête HTTP]: http://msdn.microsoft.com/library/windowsazure/jj631641.aspx
[Envoi de courrier électronique à partir de Mobile Services avec SendGrid]: /develop/mobile/tutorials/send-email-with-sendgrid/
[Prise en main de l'authentification]: http://go.microsoft.com/fwlink/p/?LinkId=287177
[crypto API]: http://go.microsoft.com/fwlink/p/?LinkId=288802
[path API]: http://go.microsoft.com/fwlink/p/?LinkId=288803
[querystring API]: http://go.microsoft.com/fwlink/p/?LinkId=288804
[url API]: http://go.microsoft.com/fwlink/p/?LinkId=288805
[util API]: http://go.microsoft.com/fwlink/p/?LinkId=288806
[zlib API]: http://go.microsoft.com/fwlink/p/?LinkId=288807
[API personnalisée]: http://msdn.microsoft.com/library/windowsazure/dn280974.aspx
[Appel d'une API personnalisée à partir du client]: /develop/mobile/tutorials/call-custom-api-dotnet/#define-custom-api
[bibliothèque express.js]: http://go.microsoft.com/fwlink/p/?LinkId=309046
[Définition d'une API personnalisée prenant en charge les notifications périodiques]: /develop/mobile/tutorials/create-pull-notifications-dotnet/
[objet express dans express.js]: http://expressjs.com/api.html#express
[Store server scripts in source control]: /develop/mobile/tutorials/store-scripts-in-source-control/
[Utilisation du code partagé et des modules Node.js dans vos scripts serveur]: /develop/mobile/tutorials/store-scripts-in-source-control/#use-npm
[objet service]: http://msdn.microsoft.com/library/windowsazure/dn303371.aspx
[Paramètres de l’application]: http://msdn.microsoft.com/library/dn529070.aspx
[config module]: http://msdn.microsoft.com/library/dn508125.aspx
[Prise en charge de package.json dans Azure Mobile Services]: http://go.microsoft.com/fwlink/p/?LinkId=391036
 

<!---HONumber=AcomDC_0224_2016-->