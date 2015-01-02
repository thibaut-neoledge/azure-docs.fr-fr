<properties urlDisplayName="HTML Client" pageTitle="Utilisation d'un client HTML - Azure Mobile Services" metaKeywords="Azure Mobile Services, Mobile Service HTML client, HTML client" description="Learn how to use an HTML client for Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="How to use an HTML/JavaScript client for Azure Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />


# Utilisation d'un client HTML/JavaScript pour Azure Mobile Services

<div class="dev-center-tutorial-selector sublanding">
  <a href="/fr-fr/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/fr-fr/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript" class="current">HTML/JavaScript</a><a href="/fr-fr/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/fr-fr/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/fr-fr/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>


Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation d'un client HTML/JavaScript pour Azure Mobile Services. Les scénarios traités incluent l'interrogation des données, l'insertion, la mise à jour et la suppression des données, l'authentification des utilisateurs et la gestion des erreurs. Si vous débutez avec Mobile Services, suivez le didacticiel [Démarrage rapide de Windows Store JavaScript] ou [Démarrage rapide HTML] consacré à Mobile Services. Ces didacticiels de démarrage rapide vous aideront à configurer et à créer votre premier service mobile.


## Sommaire

- [Présentation de Mobile Services]
- [Concepts]
- [  Création du client Mobile Services]
- [  Interrogation des données à partir d'un service mobile]
	- [Filtrage des données renvoyées]
    - [Tri des données renvoyées]
	- [Renvoi de données dans les pages]
	- [Sélection de colonnes spécifiques]
	- [Recherche des données par ID]
	- [Exécution d'une opération de requête OData]
- [  Insertion de données dans un service mobile]
- [  Modification des données d'un service mobile]
- [  Suppression des données d'un service mobile]
- [  Affichage des données dans l'interface utilisateur]
- [  Authentification des utilisateurs]
- [  Gestion des erreurs]
- [  Utilisation des promesses]
- [  Personnalisation des en-têtes de requête]
- [  Utilisation de CORS (Cross-Origin Resource Sharing, partage des ressources cross-origin)]
- [Étapes suivantes]

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

##<a name="create-client"></a>Création du client Mobile Services



Dans un éditeur web, ouvrez le fichier HTML et ajoutez ce qui suit aux références de script de la page :

    <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

>[WACOM.NOTE]Pour une application Windows Store écrite en JavaScript/HTML, vous devez simplement ajouter le package NuGet **WindowsAzure.MobileServices.WinJS** à votre projet.

Dans l'éditeur, ouvrez ou créez un fichier JavaScript, ajoutez le code suivant qui définit la variable MobileServiceClient et fournissez l'URL et la clé d'application du service mobile dans le constructeur MobileServiceClient, dans cet ordre.

	var MobileServiceClient = WindowsAzure.MobileServiceClient;
    var client = new MobileServiceClient('AppUrl', 'AppKey');

Vous devez remplacer l'espace réservé AppUrl par l'URL d'application de votre service mobile et AppKey par la clé d'application. Pour savoir comment obtenir l'URL et la clé d'application du service mobile, consultez le didacticiel [Prise en main des données dans Windows Store JavaScript] ou [Prise en main des données dans HTML/JavaScript].

##<a name="querying"></a>Interrogation des données à partir d'un service mobile

L'ensemble du code qui permet d'accéder aux données de la table Base de données SQL ou de les modifier appelle des fonctions sur l'objet MobileServiceTable. Pour obtenir une référence à la table, appelez la fonction GetTable sur une instance du MobileServiceClient.

    var todoItemTable = client.getTable('todoitem');


### <a name="filtering"></a>Filtrage des données renvoyées

Le code suivant montre comment filtrer des données en incluant une clause Where dans une requête. Il renvoie tous les éléments de todoItemTable dont le champ Complete est défini sur false. todoItemTable est la référence à la table des services mobiles que vous venez de créer. La fonction where applique un prédicat de filtrage de ligne à la requête au niveau de la table. Elle accepte comme argument une fonction ou un objet JSON qui définit le filtre de ligne et renvoie une requête qui peut être composée de manière plus approfondie.

	var query = todoItemTable.where({
	    complete: false
	}).read().done(function (results) {
	    alert(JSON.stringify(results));
	}, function (err) {
	    alert("Error: " + err);
	});

En ajoutant l'appel à where sur l'objet Requête et en transmettant un objet comme paramètre, vous indiquez à Mobile Services de renvoyer uniquement les lignes dont la colonne complete contient la valeur false. Étudiez également l'URI de requête ci-dessous. Vous remarquerez que nous modifions la chaîne de requête elle-même :

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

Vous pouvez afficher l'URI de la requête envoyée au service mobile en utilisant un logiciel d'inspection des messages, tel que les outils destinés aux développeurs de navigateurs ou Fiddler.

Cette requête serait normalement convertie approximativement sous forme de requête SQL côté serveur, comme suit :

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

L'objet qui est transmis à la méthode where peut avoir un nombre arbitraire de paramètres qui seront interprétés en tant que clauses AND dans la requête. Par exemple, la ligne ci-dessous :

	query.where({
	   complete: false,
	   assignee: "david",
	   difficulty: "medium"
	}).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Serait approximativement convertie (pour la même requête indiquée plus haut) comme suit

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND assignee = 'david'
	      AND difficulty = 'medium'

L'instruction where et la requête SQL ci-dessus recherchent les éléments incomplets attribués à " david " avec une difficulté " medium ".

Il existe néanmoins une autre manière d'écrire la même requête. Comme un appel .where sur l'objet Requête ajoute une expression AND à la clause WHERE, nous pourrions écrire ceci en trois lignes :

	query.where({
	   complete: false
	});
	query.where({
	   assignee: "david"
	});
	query.where({
	   difficulty: "medium"
	});

Ou à l'aide de l'API Fluent :

	query.where({
	   complete: false
	})
	   .where({
	   assignee: "david"
	})
	   .where({
	   difficulty: "medium"
	});

Les deux méthodes sont équivalentes et peuvent être utilisées de manière interchangeable. Tous les appels where évoqués jusqu'à maintenant prennent un objet avec quelques paramètres et leur égalité est comparée par rapport aux données de la base de données. Il y a toutefois une autre surcharge pour la méthode de requête, qui prend une fonction au lieu de l'objet. Dans cette fonction, vous pouvez écrire des expressions plus complexes, en utilisant des opérateurs tels que l'inégalité et d'autres opérations relationnelles. Dans ces fonctions, le mot clé this est lié à l'objet serveur.

Le corps de la fonction est converti en expression booléenne OData (Open Data Protocol) qui est transmise à un paramètre de chaîne de requête. Il est possible de transmettre une fonction qui ne prend aucun paramètre, comme suit :

    query.where(function () {
       return this.assignee == "david" && (this.difficulty == "medium" || this.difficulty == "low");
    }).read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });


En cas de transmission d'une fonction sans paramètre, les arguments situés après la clause where sont liés aux paramètres de fonction dans l'ordre. Les objets extérieurs à la portée de la fonction DOIVENT être transmis en tant que paramètres. La fonction ne peut pas capturer de variables externes. Dans les deux exemples suivants, l'argument " david " est lié au paramètre name, et dans le premier exemple, l'argument " medium " est également lié au paramètre level. En outre, la fonction doit se composer d'une instruction return simple avec une expression prise en charge, comme suit :

	 query.where(function (name, level) {
	    return this.assignee == name && this.difficulty == level;
	 }, "david", "medium").read().done(function (results) {
	    alert(JSON.stringify(results));
	 }, function (err) {
	    alert("Error: " + err);
	 });

Aussi, tant que vous suivez les règles, vous pouvez ajouter des filtres plus complexes à vos requêtes de base de données, comme suit :

    query.where(function (name) {
       return this.assignee == name &&
          (this.difficulty == "medium" || this.difficulty == "low");
    }, "david").read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });

Il est possible de combiner where avec orderBy, take et skip. Pour plus d'informations, consultez la section suivante.

### <a name="sorting"></a>Utilisation Tri des données renvoyées

Le code suivant montre comment trier des données en incluant une fonction orderBy ou orderByDescending dans la requête. Il renvoie des éléments de todoItemTable, triés dans l'ordre croissant par le champ text. Par défaut, le serveur renvoie uniquement les 50 premiers éléments.

<div class="dev-callout"><strong>Remarque</strong> <p>Une taille de page pilotée par un serveur est utilisée par défaut pour empêcher le renvoi de tous les éléments. Cela permet d'éviter que les requêtes par défaut associées à des jeux de données volumineux aient un impact négatif sur le service. </p> </div>
>
Vous pouvez augmenter le nombre d'éléments à renvoyer en appelant Take, comme décrit dans la section qui suit. todoItemTable est la référence à la table des services mobiles que vous venez de créer.

	var ascendingSortedTable = todoItemTable.orderBy("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

	var descendingSortedTable = todoItemTable.orderByDescending("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

	var descendingSortedTable = todoItemTable.orderBy("text").orderByDescending("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

### <a name="paging"></a>Utilisation Renvoi de données dans les pages

Le code suivant montre comment implémenter la pagination des données renvoyées à l'aide des clauses take et skip dans la requête.  Lorsqu'elle est exécutée, la requête suivante renvoie les trois premiers éléments de la table.

	var query = todoItemTable.take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Notez que la méthode `Take(3)` a été convertie en option de requête `$top=3` dans l'URI de requête.

La requête révisée ci-dessous ignore les trois premiers résultats et renvoie les trois résultats suivants. Il s'agit en fait de la deuxième " page " de données, dont la taille est de trois éléments.

	var query = todoItemTable.skip(3).take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Là aussi, vous pouvez afficher l'URI de la requête envoyée au service mobile. La méthode `skip(3)` a été convertie en option de requête `$skip=3` dans l'URI de requête.

Il s'agit d'un scénario simplifié dans lequel les valeurs de pagination codées en dur sont transmises aux fonctions take et skip. Dans une application réelle, vous pouvez utiliser des requêtes semblables à celles indiquées plus haut avec un contrôle pager ou une interface utilisateur comparable pour permettre aux utilisateurs d'accéder aux pages précédentes et suivantes.

### <a name="selecting"></a>Utilisation Sélection de colonnes spécifiques

Vous pouvez indiquer le jeu de propriétés à inclure dans les résultats en ajoutant une clause Select à la requête. Par exemple, le code suivant renvoie les propriétés id, complete et text de chaque ligne dans la table todoItemTable :

	var query = todoItemTable.select("id", "complete", "text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	})

Ici, les paramètres associés à la fonction select sont les noms des colonnes de table à renvoyer.


Toutes les fonctions décrites jusqu'ici étant cumulatives, nous pouvons continuer de les appeler, ce qui aura à chaque fois des répercussions sur la requête. Un exemple supplémentaire :

    query.where({
       complete: false
    })
       .select('id', 'assignee')
       .orderBy('assignee')
       .take(10)
       .read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);

### <a name="lookingup"></a>Utilisation Recherche des données par ID

La fonction `lookup` prend uniquement la valeur `id` et renvoie l'objet de la base de données avec cet ID. Les tables de base de données sont créées avec une colonne `id` sous forme d'entier ou de chaîne. Par défaut, la colonne id est affichée sous forme de chaîne.

	todoItemTable.lookup("37BBF396-11F0-4B39-85C8-B319C729AF6D").done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	})

##<a name="odata-query"></a>Exécution d'une opération de requête OData

Mobile Services utilise les conventions URI de requête OData pour composer et exécuter des requêtes REST.  Toutes les requêtes OData ne peuvent pas être composées à l'aide des fonctions de requête intégrées, en particulier les opérations complexes portant sur les filtres, par exemple la recherche d'une sous-chaîne dans une propriété. Pour ces requêtes complexes, vous pouvez transmettre n'importe quelle chaîne option valide de requête OData à la fonction read, comme ceci :

	function refreshTodoItems() {
	    todoItemTable.read("$filter=substringof('search_text',text)").then(function(items) {
	        var itemElements = $.map(items, createUiForTodoItem);
	        $("#todo-items").empty().append(itemElements);
	        $("#no-items").toggle(items.length === 0);
	    }, handleError);
	}

>[WACOM.NOTE]Si vous fournissez une chaîne option brute de requête OData dans la fonction read, vous ne pouvez pas utiliser également les méthodes du générateur de requêtes dans la même requête. Dans ce cas, vous devez composer toute la requête sous forme de chaîne option OData. Pour plus d'informations sur les options de requête système OData, consultez la page [Référence des options de requête système OData].

<h2><a name="inserting"></a>Utilisation Insertion de données dans un service mobile</h2>

Le code suivant montre comment insérer de nouvelles lignes dans une table. Le client demande à ce qu'une ligne de données soit insérée en envoyant une requête POST au service mobile. Le corps de la requête contient les données à insérer, sous forme d'objet JSON.

	todoItemTable.insert({
	   text: "New Item",
	   complete: false
	})

Cela permet d'insérer des données de l'objet JSON fourni dans la table. Vous pouvez également spécifier une fonction de rappel à appeler à la fin de l'insertion :

	todoItemTable.insert({
	   text: "New Item",
	   complete: false
	}).done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	});


Mobile Services prend en charge les valeurs de chaîne personnalisées uniques pour l'ID de table. Les applications peuvent ainsi utiliser des valeurs personnalisées, telles que des adresses de messagerie ou des noms d'utilisateur, pour la colonne d'ID d'une table Mobile Services. Par exemple, si vous voulez identifier chaque enregistrement par une adresse électronique, vous pouvez utiliser l'objet JSON suivant.

			todoItemTable.insert({
			   id: "myemail@domain.com",
			   text: "New Item",
			   complete: false
			})

Si aucune valeur d'ID de chaîne n'est fournie lors de l'insertion de nouveaux enregistrements dans une table, Mobile Services génère une valeur d'ID unique.

La prise en charge des ID de chaîne fournit les avantages suivants aux développeurs :

+ Les ID peuvent être générés sans effectuer d'aller-retour vers la base de données.
+ Il est plus facile de fusionner des enregistrements de plusieurs tables ou bases de données.
+ Les valeurs d'ID peuvent mieux s'intégrer à la logique d'une application.

Vous pouvez également utiliser des scripts serveur pour définir des valeurs d'ID. L'exemple de script ci-dessous génère un GUID personnalisé et l'attribue à l'ID d'un nouvel enregistrement. Il est semblable à la valeur d'ID qui serait générée par Mobile Services si vous ne transmettiez pas de valeur pour l'ID d'un enregistrement.

	//Example of generating an id. This is not required since Mobile Services
	//will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


Si une application fournit la valeur d'un ID, Mobile Services la stocke en l'état. Les espaces de début et de fin sont également inclus. Ils ne sont pas supprimés de la valeur.

La valeur d'id doit être unique et ne contenir aucun caractère présent dans les ensembles suivants :

+ Caractères de contrôle : [0x0000-0x001F] et [0x007F-0x009F]. Pour plus d'informations, consultez la page [Codes de contrôle ASCII C0 et C1].
+  Caractères imprimables : **"**(0x0022), **\+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **`** (0x0060)
+  Les ID " . " et " .. "

Vous pouvez également utiliser des ID d'entier pour vos tables. Pour utiliser un ID d'entier, vous devez créer votre table à l'aide de la commande mobile table create en utilisant l'option --integerId. Cette commande s'utilise avec l'interface de ligne de commande (CLI) pour Azure. Pour plus d'informations sur l'utilisation de l'interface de ligne de commande, consultez la page [Interface de ligne de commande pour la gestion des tables Mobile Services].


<h2><a name="modifying"></a>Utilisation Modification des données d'un service mobile</h2>

Le code suivant montre comment mettre à jour les données d'une table. Le client demande à ce qu'une ligne de données soit mise à jour en envoyant une requête PATCH au service mobile. Le corps de la requête contient les champs spécifiques à mettre à jour, sous forme d'objet JSON. Il met à jour un élément existant dans la table todoItemTable.

			todoItemTable.update({
			   id: idToUpdate,
			   text: newText
			})

Le premier paramètre spécifie l'instance à mettre à jour dans la table, comme spécifié par son ID.

Vous pouvez également spécifier une fonction de rappel à appeler à la fin de la mise à jour :

			todoItemTable.update({
			   id: idToUpdate,
			   text: newText
			}).done(function (result) {
			   alert(JSON.stringify(result));
			}, function (err) {
			   alert("Error: " + err);
			});

<h2><a name="deleting"></a>Utilisation Suppression des données d'un service mobile</h2>

Le code suivant montre comment supprimer les données d'une table. Le client demande à ce qu'une ligne de données soit supprimée en envoyant une requête DELETE au service mobile. Il supprime un élément existant de la table todoItemTable.

			todoItemTable.del({
			   id: idToDelete
			})

Le premier paramètre spécifie l'instance à supprimer dans la table, comme spécifié par son ID.

Vous pouvez également spécifier une fonction de rappel à appeler à la fin de la suppression :

			todoItemTable.del({
			   id: idToDelete
			}).done(function () {
			   /* Do something */
			}, function (err) {
			   alert("Error: " + err);
			});

<h2><a name="binding"></a>Utilisation Affichage des données dans l'interface utilisateur</h2>

Cette section montre comment afficher des objets de données renvoyés à l'aide d'éléments d'interface utilisateur. Pour interroger les éléments de la table todoItemTable et afficher celle-ci sous forme de liste toute simple, vous pouvez exécuter l'exemple de code suivant. Aucune sélection, ni aucun filtre ou tri n'est effectué.

			var query = todoItemTable;

			query.read().then(function (todoItems) {
			   // The space specified by 'placeToInsert' is an unordered list element <ul> ... </ul>
			   var listOfItems = document.getElementById('placeToInsert');
			   for (var i = 0; i < todoItems.length; i++) {
			      var li = document.createElement('li');
			      var div = document.createElement('div');
			      div.innerText = todoItems[i].text;
			      li.appendChild(div);
			      listOfItems.appendChild(li);
			   }
			}).read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

Dans une application Windows Store, les résultats d'une requête peuvent servir à créer un objet [WinJS.Binding.List], qui peut être lié comme source de données d'un objet [ListView]. Pour plus d'informations, consultez la page [Liaison de données (applications du Windows Store en JavaScript et HTML)].

<h2><a name="caching"></a>Utilisation Authentification des utilisateurs</h2>

Mobile Services permet aux utilisateurs d'applications de s'authentifier par l'intermédiaire de divers fournisseurs d'identité externes : Facebook, Google, compte Microsoft et Twitter. Vous pouvez définir des autorisations sur les tables pour limiter l'accès à certaines opérations aux seuls utilisateurs authentifiés. Vous pouvez également utiliser l'identité des utilisateurs authentifiés pour implémenter des règles d'autorisation dans les scripts serveur. Pour plus d'informations, consultez le didacticiel [Prise en main de l'authentification].

Deux flux d'authentification sont pris en charge : un _flux serveur_ et un _flux client_. Le flux serveur fournit l'authentification la plus simple, car il repose sur l'interface d'authentification Web du fournisseur. Le flux client permet une intégration approfondie avec les fonctionnalités propres aux appareils, telles que l'authentification unique, car il repose sur des Kits de développement logiciel (SDK) propres aux appareils et aux fournisseurs.

<h3>Flux serveur</h3>
Pour que Mobile Services gère le processus d'authentification dans votre application Windows Store ou HTML5, vous devez inscrire votre application auprès de votre fournisseur d'identité. Ensuite, dans votre service mobile, vous devez configurer l'ID d'application et le secret fournis par votre fournisseur. Pour plus d'informations, consultez le didacticiel " Prise en main de l'authentification " ([Windows Store][Get started with authentication Windows Store]/[HTML][Get started with authentication]).

Une fois que vous avez inscrit votre fournisseur d'identité, appelez simplement la [méthode LoginAsync] avec la valeur [MobileServiceAuthenticationProvider] de votre fournisseur. Par exemple, pour vous connecter avec Facebook, utilisez le code suivant.

		client.login("facebook").done(function (results) {
		     alert("You are now logged in as: " + results.userId);
		}, function (err) {
		     alert("Error: " + err);
		});

Si vous utilisez un fournisseur d'identité différent de Facebook, remplacez la valeur transmise à la méthode login ci-dessus par l'une des valeurs suivantes : `microsoftaccount`, `facebook`, `twitter`, `google` ou `windowsazureactivedirectory`.

Dans ce cas, Mobile Services gère le flux d'authentification OAuth 2.0 en affichant la page de connexion du fournisseur sélectionné et en générant un jeton d'authentification Mobile Services après avoir établi une connexion avec le fournisseur d'identité. La fonction [login], lorsqu'elle est utilisée, renvoie un objet JSON (**user**) qui expose l'ID utilisateur et le jeton d'authentification Mobile Services dans les champs **userId** et **authenticationToken**, respectivement. Ce jeton peut être mis en cache et réutilisé jusqu'à ce qu'il arrive à expiration. Pour plus d'informations, consultez la page [Mise en cache du jeton d'authentification].

<div class="dev-callout"><b>Application Windows Store</b>
<p>Lorsque vous utilisez le fournisseur de connexion du compte Microsoft pour authentifier les utilisateurs de votre application Windows Store, vous devez également inscrire le package de l'application auprès de Mobile Services. Lorsque vous inscrivez les informations du package de votre application Windows Store auprès de Mobile Services, le client peut réutiliser les informations d'identification du compte Microsoft pour fournir une authentification unique. Si vous ne le faites pas, vos utilisateurs se connectant via le compte Microsoft seront invités à se connecter à chaque appel de la méthode de connexion. Pour apprendre à inscrire votre package d'application Windows Store, consultez la page <a href="/fr-fr/develop/mobile/how-to-guides/register-windows-store-app-package/" target="_blank">Inscription du package de votre application Windows Store pour l'authentification Microsoft</a>. Après avoir inscrit les informations du package de votre application Windows Store auprès de Mobile Services, appelez la méthode <a href="http://go.microsoft.com/fwlink/p/?LinkId=322050" target="_blank">login</a> en fournissant la valeur <strong>true</strong> pour le paramètre <em>useSingleSignOn</em> pour réutiliser les informations d'identification.</p>
</div>

<h3>Flux client</h3>
Votre application peut également contacter le fournisseur d'identité de manière indépendante, puis fournir le jeton renvoyé à Mobile Services à des fins d'authentification. Le flux client permet de proposer l'authentification unique aux utilisateurs ou de récupérer d'autres données utilisateur auprès du fournisseur d'identité.

L'exemple suivant utilise le Kit de développement logiciel (SDK) Live, qui prend en charge l'authentification unique pour les applications Windows Store à l'aide d'un compte Microsoft :

		WL.login({ scope: "wl.basic"}).then(function (result) {
		      client.login(
		            "microsoftaccount",
		            {"authenticationToken": result.session.authentication_token})
		      .done(function(results){
		            alert("You are now logged in as: " + results.userId);
		      },
		      function(error){
		            alert("Error: " + err);
		      });
		});

Cet exemple simplifié récupère un jeton de Live Connect, qui est fourni à Mobile Services en appelant la fonction [login]. Pour accéder à un exemple plus complet concernant l'utilisation d'un compte Microsoft pour fournir l'authentification unique, consultez la rubrique [Authentification de votre application avec l'authentification unique].

Lorsque vous utilisez les API Facebook ou Google pour l'authentification client, l'exemple diffère légèrement.

		client.login(
		     "facebook",
		     {"access_token": token})
		.done(function (results) {
		     alert("You are now logged in as: " + results.userId);
		}, function (err) {
		     alert("Error: " + err);
		});

Cet exemple part du principe que le jeton fourni par le Kit de développement logiciel (SDK) propre au fournisseur est stocké dans une variable token.
Vous ne pouvez pas utiliser Twitter pour l'authentification client pour le moment.

<h3>Mise en cache du jeton d'authentification</h3>
Dans certains cas, l'appel à la méthode de connexion peut être évité après la première authentification de l'utilisateur. Vous pouvez utiliser [sessionStorage] ou [localStorage] pour mettre en cache l'identité de l'utilisateur actif lors de sa première connexion et, par la suite, à chaque fois que vous vérifiez si son identité est présente dans le cache. Si le cache est vide ou échoue (c'est-à-dire que la session de connexion en cours a expiré), l'utilisateur doit toujours effectuer le processus de connexion.

        // After logging in
        sessionStorage.loggedInUser = JSON.stringify(client.currentUser);

        // Log in
        if (sessionStorage.loggedInUser) {
           client.currentUser = JSON.parse(sessionStorage.loggedInUser);
        } else {
           // Regular login flow
       }

         // Log out
        client.logout();
        sessionStorage.loggedInUser = null;


<h2><a name="errors"></a>Utilisation Gestion des erreurs</h2>

Il existe plusieurs méthodes pour détecter, valider et résoudre les erreurs dans Mobile Services.

Par exemple, il est possible d'utiliser les scripts serveur inscrits dans un service mobile pour effectuer diverses opérations sur les données insérées et mises à jour, qu'il s'agisse de les valider ou de les modifier. Vous pouvez définir et inscrire un script serveur qui valide et modifie des données comme suit :

			function insert(item, user, request) {
			   if (item.text.length > 10) {
				  request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
			   } else {
			      request.execute();
			   }
			}

Ce script côté serveur valide la longueur des données de chaîne envoyées au service mobile et refuse les chaînes trop longues, en l'occurrence, celles qui font plus de 10 caractères.

Comme le service mobile valide les données et envoie des réponses d'erreur côté serveur, vous pouvez mettre à jour votre application HTML de manière à ce qu'elle traite les réponses d'erreur de la validation.

		todoItemTable.insert({
		   text: itemText,
		   complete: false
		})
		   .then(function (results) {
		   alert(JSON.stringify(results));
		}, function (error) {
		   alert(JSON.parse(error.request.responseText).error);
		});


En outre, vous transmettez le gestionnaire d'erreur comme deuxième argument à chaque accès aux données :

			function handleError(message) {
			   if (window.console && window.console.error) {
			      window.console.error(message);
			   }
			}

			client.getTable("tablename").read().then(function (data) { /* do something */ }, handleError);

<h2><a name="promises"></a>Utilisation Utilisation des promesses</h2>

Les promesses permettent de planifier un travail sur une valeur qui n'a pas encore été calculée. Il s'agit d'une abstraction pour la gestion des interactions avec des API asynchrones.

La promesse done est exécutée dès que la fonction qui lui a été fournie a réussi ou a généré une erreur. Contrairement à la promesse then, la génération d'une erreur non traitée au sein de la fonction est garantie, et à la fin de l'exécution des gestionnaires, cette fonction génère une erreur qui aurait été renvoyée depuis " then " en tant que promesse dans l'état d'erreur. Pour plus d'informations, consultez la page [done].

			promise.done(onComplete, onError);

Comme suit :

			var query = todoItemTable;
			query.read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

La promesse then est identique à la promesse done, mais contrairement à la promesse then, done garantit la génération d'une erreur qui n'est pas traitée au sein de la fonction. Si vous ne fournissez pas de gestionnaire d'erreurs à then et que l'opération génère une erreur, aucune exception n'est levée, mais une promesse est renvoyée dans l'état d'erreur. Pour plus d'informations, consultez la page [then].

			promise.then(onComplete, onError).done( /* Your success and error handlers */ );

Comme suit :

			var query = todoItemTable;
			query.read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

Vous pouvez utiliser les promesses de différentes manières. Vous pouvez chaîner des opérations de promesse en appelant then ou done sur la promesse renvoyée par la fonction then précédente. Utilisez then pour une étape intermédiaire de l'opération (par exemple, .then().then()), et done pour l'étape finale de l'opération (par exemple, .then().then().done()).  Vous pouvez chaîner plusieurs fonctions then, car then renvoie une promesse. Vous ne pouvez pas chaîner plusieurs méthodes done, car elles renvoient des éléments non définis. [Découvrez les différences entre then et done].

 			todoItemTable.insert({
 			   text: "foo"
 			}).then(function (inserted) {
 			   inserted.newField = 123;
 			   return todoItemTable.update(inserted);
 			}).done(function (insertedAndUpdated) {
 			   alert(JSON.stringify(insertedAndUpdated));
 			})

<h2><a name="customizing"></a>Utilisation Personnalisation des en-têtes de requête</h2>

Vous pouvez envoyer des en-têtes de requête personnalisés à l'aide de la fonction withFilter, en lisant et en écrivant les propriétés arbitraires de la requête sur le point d'être envoyée au sein du filtre. Vous pouvez ajouter un en-tête HTTP personnalisé si le script côté serveur en a besoin ou peut être amélioré grâce à lui.

			var client = new WindowsAzure.MobileServiceClient('https://your-app-url', 'your-key')
			   .withFilter(function (request, next, callback) {
			   request.headers.MyCustomHttpHeader = "Some value";
			   next(request, callback);
			});

Outre la personnalisation des en-têtes de requête, les filtres peuvent être utilisés dans bien d'autres opérations, par exemple pour examiner ou modifier les requêtes/réponses, ignorer les appels réseau, envoyer plusieurs appels, etc.

<h2><a name="hostnames"></a>Utilisation Utilisation de CORS (Cross-Origin Resource Sharing, partage des ressources cross-origin)</h2>

Pour contrôler les sites Web autorisés à interagir avec les requêtes et à en envoyer à votre service mobile, veillez à ajouter le nom d'hôte du site Web utilisé pour l'héberger dans la liste approuvée CORS (Cross-Origin Resource Sharing) à l'aide de l'onglet Configurer. Vous pouvez utiliser des caractères génériques le cas échéant. Par défaut, les nouveaux services mobiles indiquent aux navigateurs d'autoriser l'accès uniquement à partir de localhost, et CORS (Cross-Origin Resource Sharing) permet au code JavaScript de s'exécuter dans un navigateur sur un nom d'hôte externe à des fins d'interaction avec votre service mobile.  Cette configuration n'est pas requise pour les applications WinJS.

<h2><a name="nextsteps"></a>Étapes suivantes</h2>

Maintenant que vous avez consulté ce guide de fonctionnement, découvrez en détail comment effectuer des tâches importantes dans Mobile Services :

* [Prise en main de Mobile Services]
  <br/>Découvrez les règles de base d'utilisation de Mobile Services.

* [Prise en main des données]
  <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

* [Prise en main de l'authentification]
  <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

* [Validation et modification des données avec des scripts]
  <br/>LEn savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

* [Affinage des requêtes au moyen de la pagination]
  <br/>En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

* [Autorisation des utilisateurs avec des scripts]
  <br/>Découvrez comment prendre la valeur d'ID utilisateur fournie par Mobile Services en fonction de l'utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services.

<!-- Anchors. -->
[Présentation de Mobile Services]: #what-is
[Concepts]: #concepts
[  Création du client Mobile Services]: #create-client
[  Interrogation des données à partir d'un service mobile]: #querying
[Filtrage des données renvoyées]: #filtering
[Tri des données renvoyées]: #sorting
[Renvoi de données dans les pages]: #paging
[Sélection de colonnes spécifiques]: #selecting
[Recherche des données par ID]: #lookingup
[  Affichage des données dans l'interface utilisateur]: #binding
[  Insertion de données dans un service mobile]: #inserting
[  Modification des données d'un service mobile]: #modifying
[  Suppression des données d'un service mobile]: #deleting
[  Authentification des utilisateurs]: #caching
[  Gestion des erreurs]: #errors
[  Utilisation des promesses]: #promises
[  Personnalisation des en-têtes de requête]: #customizing
[  Utilisation de CORS (Cross-Origin Resource Sharing, partage des ressources cross-origin)]: #hostnames
[Étapes suivantes]: #nextsteps
[Exécution d'une opération de requête OData]: #odata-query



<!-- URLs. -->
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-html
[Kit de développement logiciel (SDK) Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=257545
[Prise en main des données]: http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started-with-data-html/
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-html
[Prise en main de l'authentification Windows Store]: /fr-fr/develop/mobile/tutorials/get-started-with-users-js
[then]: http://msdn.microsoft.com/fr-fr/library/windows/apps/br229728.aspx
[done]: http://msdn.microsoft.com/fr-fr/library/windows/apps/hh701079.aspx
[Découvrez les différences entre then et done]: http://msdn.microsoft.com/fr-fr/library/windows/apps/hh700334.aspx
[traitement des erreurs dans les promesses]: http://msdn.microsoft.com/fr-fr/library/windows/apps/hh700337.aspx

[sessionStorage]: http://msdn.microsoft.com/fr-fr/library/cc197062(v=vs.85).aspx
[localStorage]: http://msdn.microsoft.com/fr-fr/library/cc197062(v=vs.85).aspx

[ListView]: http://msdn.microsoft.com/fr-fr/library/windows/apps/br211837.aspx
[Liaison de données (applications du Windows Store en JavaScript et HTML)]: http://msdn.microsoft.com/fr-fr/library/windows/apps/hh758311.aspx
[Démarrage rapide de Windows Store JavaScript]: http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started
[Guide de démarrage HTML]: http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started-html
[Prise en main des données dans Windows Store JavaScript]: http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started-with-data-js
[Prise en main des données dans HTML/JavaScript]: http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started-with-data-html/
[Un exemple complet de configuration de ce scénario est disponible ici]: http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/single-sign-on-windows-8-js/
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-html
[Validation et modification des données avec des scripts]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-html
[Affinage des requêtes au moyen de la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-html
[Autorisation des utilisateurs avec des scripts]: /fr-fr/develop/mobile/tutorials/authorize-users-in-scripts-html
[login]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554236.aspx
[Authentification de votre application avec Active Directory]: /fr-fr/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[Codes de contrôle ASCII C0 et C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Interface de ligne de commande pour la gestion des tables Mobile Services]: http://www.windowsazure.com/fr-fr/manage/linux/other-resources/command-line-tools/#Mobile_Tables
[Référence des options de requête système OData]: http://go.microsoft.com/fwlink/p/?LinkId=444502

<!--HONumber=35_1-->
