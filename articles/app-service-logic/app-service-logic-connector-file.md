<properties 
	pageTitle="Connecteur File"
	description="Prise en main du connecteur File"
	authors="anuragdalmia" 
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
	ms.date="04/09/2015"
	ms.author="andalmia"/>

# Utilisation du connecteur File dans votre application logique #

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux.

Le connecteur File vous permet de télécharger et de supprimer des fichiers sur un ordinateur hôte. Il utilise le Gestionnaire des connexions hybrides pour la connectivité hybride vers l'ordinateur hôte.

## Création d’un connecteur File pour votre application logique ##
Pour utiliser le connecteur File, vous devez d’abord créer une instance de l’application API de ce connecteur. Pour cela, procédez comme suit :

1.	Ouvrez Azure Marketplace à l’aide de l’option + NOUVEAU située à gauche du portail Azure.
2.	Accédez à « Marketplace > Apps API » et recherchez « Connecteur File ».
3.	Configurez le connecteur File comme suit :
 
	![][1]
 
	- **Nom** : indiquez le nom de votre connecteur File.
	- **Paramètres du package**
		- **Dossier racine** : indiquez le chemin du dossier racine sur votre ordinateur hôte. Par exemple D:\FileConnectorTest
		- **Chaîne de connexion au Service Bus** : indiquez la chaîne de connexion au Service Bus. Vérifiez que l’espace de noms du Service Bus est de type Standard et NON Basique pour permettre l’utilisation de Service Bus Relays. Service Bus Relay est utilisé pour se connecter au Gestionnaire des connexions hybrides. 
	- **Plan App Service** : sélectionnez ou créez un plan App Service.
	- **Niveau de tarification** : choisissez un niveau de tarification pour le connecteur.
	- **Groupe de ressources** : sélectionnez ou créez un groupe de ressources où doit résider le connecteur.
	- **Abonnement** : choisissez un abonnement dans lequel vous souhaitez créer ce connecteur.
	- **Emplacement** : choisissez l’emplacement géographique dans lequel vous voulez déployer le connecteur.

4. Cliquez sur Créer. Un connecteur File est créé.

## Configurer le Gestionnaire des connexions hybrides ##
Une fois l’instance de l’application API créée, accédez à son tableau de bord. Pour ce faire, cliquez sur Parcourir > Applications API > sélectionnez l’application API de votre connecteur File. À ce stade, le Gestionnaire des connexions hybrides doit être configuré. Pour plus d'informations sur la configuration et le dépannage du Gestionnaire des connexions hybrides, consultez la rubrique [Utilisation du Gestionnaire des connexions hybrides].

## Utilisation du connecteur File dans votre application logique ##
Une fois votre application API créée, vous pouvez utiliser le connecteur File comme action pour votre application logique. Pour cela, vous devez procéder comme suit :

1.	Créez une application logique et choisissez le groupe de ressources qui contient le connecteur File. Suivez les instructions indiquées dans la rubrique [Création d'une application logique].  	
	
2.	Ouvrez « Déclencheurs et actions » dans l’application logique créée pour ouvrir le Concepteur d’applications logiques et configurez votre flux.
	
3.	Le connecteur File s’affiche dans la section « Applications API dans ce groupe de ressources » de la galerie située à droite.
 	
4.	Vous pouvez déposer l’application API du connecteur File dans l’éditeur en cliquant sur « Connecteur File ». Le connecteur contient un déclencheur et 4 actions.
 
	![][5]

6.	Chacun de ces éléments a des propriétés. L’image ci-dessous montre les propriétés du déclencheur et de l’action « Obtenir le fichier ».
 
	![][6]

7. Pour être utilisables dans votre flux, le déclencheur et l’action doivent être configurés. De même, d’autres actions peuvent être configurées.

> [AZURE.NOTE]Le déclencheur de fichier supprimera le fichier lorsque celui-ci sera correctement lu à partir du dossier.

## API REST du connecteur File ##
Pour utiliser le connecteur hors d’une application logique, vous devez faire appel aux API REST exposées par celui-ci. Pour afficher ces définitions d’API, sélectionnez Parcourir -> Application API -> Connecteur File. Maintenant, cliquez sur le filtre Définition des API sous la section Résumé pour afficher toutes les API exposées par ce connecteur.

	![][7]

Pour plus d’informations sur les API, consultez la rubrique [Définition des API du connecteur File].

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[Création d'une application logique]: app-service-logic-create-a-logic-app.md
[Définition des API du connecteur File]: https://msdn.microsoft.com/fr-fr/library/dn936296.aspx
[Utilisation du Gestionnaire des connexions hybrides]: app-service-logic-hybrid-connection-manager.md

<!---HONumber=62-->