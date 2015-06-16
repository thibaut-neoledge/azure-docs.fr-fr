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

Le connecteur File vous permet de télécharger et de supprimer des fichiers sur un ordinateur hôte.

## Création d'un connecteur File pour votre application logique ##
Pour utiliser le connecteur File, vous devez d'abord créer une instance de l'application API de ce connecteur. Pour cela, procédez comme suit :

1.	Ouvrez Azure Marketplace à l'aide de l'option + NOUVEAU en bas à gauche du portail Azure.
2.	Accédez à " Web et mobilité > Azure Marketplace " et recherchez " Connecteur File ".
3.	Configurez le connecteur File comme suit :
 
	![][1]
 
	- **Nom** : indiquez le nom de votre connecteur File.
	- **Paramètres du package**
		- **Dossier racine** : indiquez le chemin du dossier racine sur votre ordinateur hôte. Par exemple : D:\FileConnectorTest
		- **Chaîne de connexion au Service Bus** : indiquez la chaîne de connexion au Service Bus. Vérifiez que l'espace de noms du Service Bus est de type Standard et NON Basique.
	- **Plan de service de l'application** : sélectionnez ou créez un plan de service de l'application.
	- **Niveau de tarification** : choisissez un niveau de tarification pour le connecteur.
	- **Groupe de ressources** : sélectionnez ou créez un groupe de ressources dans lequel doit résider le connecteur.
	- **Abonnement** : choisissez un abonnement dans lequel vous voulez que ce connecteur soit créé.
	- **Emplacement** : choisissez l'emplacement géographique dans lequel vous voulez déployer le connecteur.

4. Cliquez sur Créer. Un connecteur File est créé.
5. Une fois l'instance de l'application API créée, accédez à son tableau de bord. Pour ce faire, cliquez sur Parcourir -> Applications API -> Sélectionnez l'application API de votre connecteur File.
6. Dans le tableau de bord du connecteur File, le filtre " Connexion hybride " affiche " Installation locale incomplète ". Pour finaliser l'installation hybride, ouvrez ce tableau de bord depuis l'ordinateur hôte dont le système de fichiers doit être connecté. Ensuite, cliquez sur le filtre " Connexion hybride ". Dans la lame " Connexion hybride ", cliquez sur le lien " Télécharger et configurer " pour installer le Gestionnaire de connexion hybride locale.

	![][2]

7. Le programme d'installation qui démarre vous demande la " Chaîne de connexion à l'écouteur de relais ". Copiez et collez la " Chaîne de configuration principale " située dans la lame " Connexion hybride ". Cliquez sur Installer.

	![][3]

8. Pour vérifier que l'installation hybride a abouti, rouvrez le tableau de bord du connecteur File. L'état de la connexion hybride doit être " Connecté ".

	![][4]

## Utilisation du connecteur File dans votre application logique ##
Une fois votre application API créée, vous pouvez utiliser le connecteur File comme action pour votre application logique. Pour cela, vous devez procéder comme suit :

1.	Créez une application logique et choisissez le groupe de ressources qui contient le connecteur File. Suivez les instructions pour [Créer une application logique].  	
	
2.	Ouvrez " Déclencheurs et actions " dans l'application logique créée pour ouvrir le Concepteur d'applications logiques et configurez votre flux.  	
	
3.	Le connecteur File s'affiche dans la section " Applications API dans ce groupe de ressources " de la galerie située à droite.
 	
4.	Vous pouvez déposer l'application API du connecteur File dans l'éditeur en cliquant sur " Connecteur File ". Le connecteur contient un déclencheur et 4 actions.
 
	![][5]

6.	Chacun de ces éléments a des propriétés. L'image ci-dessous montre les propriétés du déclencheur et de l'action " Obtenir le fichier ".
 
	![][6]

7. Pour être utilisables dans votre flux, le déclencheur et l'action doivent être configurés. De même, d'autres actions peuvent être configurées.

8. Pour utiliser le connecteur hors d'une application logique, vous devez faire appel aux API REST exposées par celui-ci. Pour afficher ces définitions d'API, sélectionnez Parcourir -> Application API -> Connecteur File. Maintenant, cliquez sur le filtre Définition des API sous la section Résumé pour afficher toutes les API exposées par ce connecteur.

	![][7]

9. Pour plus d'informations sur les API, consultez la rubrique [Définition des API du connecteur File].

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[2]: ./media/app-service-logic-connector-file/img2.PNG
[3]: ./media/app-service-logic-connector-file/img3.PNG
[4]: ./media/app-service-logic-connector-file/img4.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[Créer une application logique]: app-service-logic-create-a-logic-app.md
[Définition des API du connecteur File]: https://msdn.microsoft.com/en-US/library/dn936296.aspx


<!--HONumber=52--> 