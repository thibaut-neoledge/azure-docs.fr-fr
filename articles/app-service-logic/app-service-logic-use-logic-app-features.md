<properties 
	pageTitle="Utiliser les fonctionnalités des applications logiques"
	description="Découvrez comment utiliser les fonctionnalités avancées des applications logiques."
	authors="stepsic-microsoft-com"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2015"
	ms.author="stepsic"/>
	
# Utiliser les fonctionnalités des applications logiques

Dans la [rubrique précédente][Create a new logic app], vous avez créé votre première application logique. Nous allons maintenant voir comment créer un processus plus complet avec des applications logiques App Services. Cette rubrique présente les nouveaux concepts suivants liés aux applications logiques :

- logique conditionnelle, qui exécute une action uniquement si une certaine condition est remplie ;
- répétition d'actions ;
- mode code pour modifier une application logique existante ;
- options de démarrage d'un flux de travail.

Avant d’effectuer les étapes de cette rubrique, vous devez effectuer celles de la rubrique [Créer une application logique]. Dans le [portail Azure], accédez à votre application logique, cliquez sur **Déclencheurs et actions** dans le résumé pour modifier la définition de l’application logique.

## Documents de référence

Les documents suivants peuvent vous être utiles :

- [API REST de gestion et d’exécution](https://msdn.microsoft.com/library/azure/dn948513.aspx) - notamment comment appeler directement des applications logiques
- [Référence sur le langage](https://msdn.microsoft.com/library/azure/dn948512.aspx) - liste complète de toutes les fonctions/expressions prises en charge
- [Types de déclencheurs et d'actions](https://msdn.microsoft.com/library/azure/dn948511.aspx) - les différents types d'actions et ce qu’elles prennent en entrée
- [Vue d'ensemble d’App Service](app-service-value-prop-what-is.md) - description des composants à choisir pour la création d’une solution

## Ajout d'une logique conditionnelle et d'une répétition

Bien que le flux d'origine fonctionne, certaines zones pourraient être améliorées. Tout d'abord, l'action n'envoie que le premier tweet retourné. Logiquement, vous souhaiterez recevoir tous les tweets contenant le mot clé. Pour répéter une action pour une liste d’éléments, tels que les tweets retournés, vous devez utiliser la propriété `repeat`.

### Répétition
Repeat prend une liste d'éléments et exécute l'action pour chaque élément de la liste. Les étapes suivantes mettent à jour l'action existante pour utiliser des répétitions, ce qui est plus logique pour une liste de tweets.

1. Revenez au flux de travail que vous avez créé et cliquez sur le lien **Définition** dans **Essentials**. 

2. Pour modifier l’action **Connecteur Dropbox**, cliquez sur l’icône de crayon.

3. Cliquez sur l’icône d’engrenage et sélectionnez **Répéter sur une liste**.
 
2. À côté de la zone **Répéter**, cliquez sur `...` et sélectionnez **Corps**. Le texte suivant :

    	@body('twitterconnector')

	est inséré dans la zone de texte. Cette fonction génère une liste de tweets.

3. Sélectionnez tout le texte dans la zone de texte **Contenu** et supprimez-le. Ensuite, cliquez sur `...` et sélectionnez **Texte de tweet**. Cela insère la fonction **repeatItem()**, qui retourne chaque élément de la liste.

Pour finir, notez que les sorties des actions de répétition sont spéciales. Si vous souhaitez par exemple faire référence aux résultats de l’opération Dropbox, vous ne pouvez *pas* utiliser un `@actions('dropboxconnector').outputs.body` normal. Vous devez plutôt utiliser : `@actions('dropboxconnector').outputs.repeatItems`. Cela retourne une liste de toutes les exécutions de l'opération, ainsi que les sorties de chacune d'elles. Par exemple, `@first(actions('dropboxconnector').outputs.repeatItems).outputs.body.FilePath` renvoie le chemin d’accès du premier fichier téléchargé.

### Logique conditionnelle
Cette application logique provoque toujours le téléchargement d'un grand nombre de fichiers vers Dropbox. Les étapes suivantes ajoutent une logique supplémentaire pour s'assurer que vous recevez uniquement un fichier quand le tweet a un certain nombre de retweets.

1. Cliquez sur l’icône d’engrenage en haut de l’action et sélectionnez **Ajouter une condition à remplir**.

2. Dans la zone de texte, tapez ce qui suit :

    	@greater(repeatItem().Retweet_Count , 5)
    
	La fonction **greater** compare deux valeurs et autorise l’exécution de l’action uniquement quand la première valeur est supérieure à la deuxième. Pour accéder à une propriété donnée, utilisez un point (.) suivi du nom de la propriété, tel que `.Retweet_Count` ci-dessus.

3. Cochez la case pour enregistrer l'action Dropbox.

## Utilisation du mode code pour modifier une application logique

Outre le concepteur, vous pouvez modifier directement le code qui définit une application logique en procédant comme suit.

1. Cliquez sur le bouton **Mode code** dans la barre de commandes. 

	Cela ouvre un éditeur complet qui affiche la définition que vous venez de modifier.

	![Mode Code](./media/app-service-logic-use-logic-app-features/codeview.png)

    À l'aide de l'éditeur de texte, vous pouvez copier et coller les actions de votre choix dans la même application logique ou d'une application logique vers une autre. Vous pouvez aussi ajouter ou supprimer facilement des sections entières de la définition et partager des définitions avec d'autres utilisateurs.

2. Après avoir apporté vos modifications en mode code, cliquez simplement sur **Enregistrer**.

### Paramètres
Certaines fonctionnalités des applications logiques ne peuvent être utilisées qu'en mode code. Les paramètres en constituent un exemple. Les paramètres simplifient la réutilisation des valeurs dans votre application logique. Par exemple, si vous avez une adresse de messagerie que vous souhaitez utiliser dans plusieurs actions, vous devez la définir en tant que paramètre.

Le code suivant met à jour votre application logique existante pour utiliser des paramètres pour le terme de requête.

1. En mode code, recherchez l’objet `parameters : {}` et insérez l’objet de rubrique suivant :

	    "topic" : {
		    "type" : "string",
		    "defaultValue" : "MicrosoftAzure"
	    }
    
2. Faites défiler la page jusqu’à l’action `twitterconnector`, recherchez la valeur de la requête et remplacez-la par `#@{parameters('topic')}`. Vous pouvez également utiliser la fonction **concat** pour joindre deux ou plusieurs chaînes, par exemple : `@concat('#',parameters('topic'))` est identique à celle ci-dessus.
 
3. Pour finir, accédez à l’action `dropboxconnector` et ajoutez le paramètre de rubrique, comme suit :

    	/tweets/@{parameters('topic')}/@{repeatItem().TweetID}.txt

Les paramètres constituent un bon moyen d'extraire des valeurs que vous êtes susceptible de modifier souvent. Ils sont particulièrement utiles quand vous devez substituer des paramètres dans différents environnements. Pour plus d’informations sur la façon de substituer des paramètres en fonction de l’environnement, consultez notre [Documentation sur l’API REST](http://go.microsoft.com/fwlink/?LinkID=525617&clcid=0x409).

Maintenant, quand vous cliquez sur **Enregistrer**, toutes les heures les nouveaux tweets qui ont plus de cinq retweets seront envoyés dans un dossier nommé **tweets** dans votre dossier Dropbox.

Pour en savoir plus sur les définitions d'application logique, consultez la rubrique [Créer des définitions d'application logique](app-service-logic-author-definitions.md).

## Démarrage d'un flux de travail d'application logique
Il existe plusieurs options pour démarrer le flux de travail défini dans votre application logique. Un flux de travail peut toujours être démarré à la demande dans le [portail Azure].

### Déclencheurs de périodicité
Un déclencheur de périodicité s'exécute selon un intervalle que vous spécifiez. Quand le déclencheur a une logique conditionnelle, il détermine si le flux de travail doit s'exécuter. Un déclencheur indique qu’il doit s’exécuter en retournant un code d’état `200`. Quand il ne doit pas s’exécuter, il retourne un code d’état `202`.

### Rappel à l'aide des API REST
Les services peuvent appeler un point de terminaison d'application logique pour démarrer un flux de travail. Vous pouvez trouver le point de terminaison auquel accéder en accédant au panneau **Propriétés** à partir du bouton de la barre de commandes **Paramètres** dans votre application logique.

Vous pouvez utiliser ce rappel pour appeler une application logique depuis votre application personnalisée. Vous devez utiliser l’authentification **De base**. Le nom d’utilisateur `default` est créé pour vous et le mot de passe est le champ **Clé d’accès primaire** dans le panneau **Propriétés**. Par exemple :

        POST https://<< your endpoint >>/run?api-version=2015-02-01-preview
        Content-type: application/json
        Authorization: Basic << base-64 encoded string of default:<access key> >>
        {
            "name" : "nameOfTrigger",
            "outputs" : { "property" : "value" }
        }

Vous pouvez passer des sorties au flux de travail et y fait référence dans le flux de travail. Par exemple, avec le déclencheur ci-dessus, si vous incluez `@triggers().outputs.property`, vous obtiendrez `value`.

Pour plus d’informations, [consultez la documentation REST](http://go.microsoft.com/fwlink/?LinkID=525617&clcid=0x409).

### Exécution manuelle
Vous pouvez définir une application logique qui n'a pas de déclencheur. Dans ce cas, le flux de travail doit être démarré à la demande. Ce genre d'application logique convient mieux à un processus qui ne doit s'exécuter que de façon intermittente. Pour créer une application logique sans déclencheur, cochez la case **Exécuter cette logique manuellement** dans la zone **Démarrer la logique** du concepteur.

Pour démarrer l’application logique à la demande, cliquez sur le bouton **Exécuter maintenant** dans la barre de commandes.

<!-- Shared links -->
[Create a new logic app]: app-service-logic-create-a-logic-app.md
[Créer une application logique]: app-service-logic-create-a-logic-app.md
[portail Azure]: https://portal.azure.com

<!---HONumber=August15_HO8-->