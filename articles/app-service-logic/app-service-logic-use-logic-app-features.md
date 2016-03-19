<properties 
	pageTitle="Utiliser les fonctionnalités d’une application logique | Microsoft Azure" 
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
	ms.date="02/23/2016"
	ms.author="stepsic"/>
	
# Utiliser les fonctionnalités des applications logiques

Dans la [rubrique précédente](app-service-logic-create-a-logic-app.md), vous avez créé votre première application logique. Nous allons maintenant voir comment créer un processus plus complet avec des applications logiques App Services. Cette rubrique présente les nouveaux concepts suivants liés aux applications logiques :

- logique conditionnelle, qui exécute une action uniquement si une certaine condition est remplie ;
- mode code pour modifier une application logique existante ;
- options de démarrage d'un flux de travail.

Avant d’effectuer les étapes de cette rubrique, vous devez effectuer celles de la rubrique [Créer une application logique](app-service-logic-create-a-logic-app.md). Dans le [portail Azure], accédez à votre application logique, cliquez sur **Déclencheurs et actions** dans le résumé pour modifier la définition de l’application logique.

## Documents de référence

Les documents suivants peuvent vous être utiles :

- [API REST de gestion et d’exécution](https://msdn.microsoft.com/library/azure/dn948513.aspx) - notamment comment appeler directement des applications logiques
- [Référence sur le langage](https://msdn.microsoft.com/library/azure/dn948512.aspx) - liste complète de toutes les fonctions/expressions prises en charge
- [Types de déclencheurs et d'actions](https://msdn.microsoft.com/library/azure/dn948511.aspx) - les différents types d'actions et ce qu’elles prennent en entrée
- [Vue d'ensemble d’App Service](../app-service/app-service-value-prop-what-is.md) - description des composants à choisir pour la création d’une solution

## Ajout d’une logique conditionnelle

Bien que le flux d'origine fonctionne, certaines zones pourraient être améliorées.


### Logique conditionnelle
Cette application logique risque de causer un grand volume d’e-mails. Les étapes suivantes ajoutent une logique supplémentaire pour s’assurer que vous recevez uniquement un e-mail lorsque le tweet provient d’une personne avec un certain nombre de followers.

1. Cliquez sur le signe plus et recherchez l’action *Get User* pour twitter.

2. Transmettez le champ **Tweeted by** à partir du déclencheur pour obtenir les informations relatives à l’utilisateur de twitter.

	![Get User](./media/app-service-logic-use-logic-app-features/getuser.png)

3. Cliquez sur le signe plus à nouveau, mais cette fois-ci, sélectionnez **Add Condition**

4. Dans la première zone, cliquez sur **...** sous **Get User** pour trouver le champ **Followers count**.

5. Dans la liste déroulante, sélectionnez **Greater than**

6. Dans la deuxième zone, tapez le nombre de followers que les utilisateurs doivent avoir.

	![Logique conditionnelle](./media/app-service-logic-use-logic-app-features/conditional.png)

7.  Enfin, effectuez un glisser-déplacer de la zone e-mail vers la zone **If Yes**. Cela signifie que vous ne recevrez des e-mails que lorsque le nombre de followers est atteint.

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

1. En mode code, recherchez l’objet `parameters : {}` et insérez l’objet de rubrique suivant :

	    "topic" : {
		    "type" : "string",
		    "defaultValue" : "MicrosoftAzure"
	    }
    
2. Faites défiler la page jusqu’à l’action `twitterconnector`, recherchez la valeur de la requête et remplacez-la par `#@{parameters('topic')}`. Vous pouvez également utiliser la fonction **concat** pour joindre deux ou plusieurs chaînes, par exemple : `@concat('#',parameters('topic'))` est identique à celle ci-dessus.
 
Les paramètres constituent un bon moyen d'extraire des valeurs que vous êtes susceptible de modifier souvent. Ils sont particulièrement utiles quand vous devez substituer des paramètres dans différents environnements. Pour plus d’informations sur la façon de substituer des paramètres en fonction de l’environnement, consultez notre [Documentation sur l’API REST](http://msdn.microsoft.com/library/mt643788(Azure.100).aspx).

Maintenant, quand vous cliquez sur **Enregistrer**, toutes les heures les nouveaux tweets qui ont plus de 5 retweets seront envoyés dans un dossier nommé **tweets** dans votre dossier Dropbox.

Pour en savoir plus sur les définitions d'application logique, consultez la rubrique [Créer des définitions d'application logique](app-service-logic-author-definitions.md).

## Démarrage d'un flux de travail d'application logique
Il existe plusieurs options pour démarrer le flux de travail défini dans votre application logique. Un flux de travail peut toujours être démarré à la demande dans le [portail Azure].

### Déclencheurs de périodicité
Un déclencheur de périodicité s'exécute selon un intervalle que vous spécifiez. Quand le déclencheur a une logique conditionnelle, il détermine si le flux de travail doit s'exécuter. Un déclencheur indique qu’il doit s’exécuter en retournant un code d’état `200`. Quand il ne doit pas s’exécuter, il retourne un code d’état `202`.

### Rappel à l'aide des API REST
Les services peuvent appeler un point de terminaison d'application logique pour démarrer un flux de travail. Pour plus d’informations, consultez la page [Applications logiques en tant que points de terminaison pouvant être appelés](app-service-logic-connector-http.md). Pour démarrer ce type d’application logique à la demande, cliquez sur le bouton **Exécuter maintenant** dans la barre de commandes.

<!-- Shared links -->
[portail Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0224_2016-->