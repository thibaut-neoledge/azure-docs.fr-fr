<properties
	pageTitle="Créer et configurer des API gérées par Microsoft et gérées par le service informatique dans PowerApps Enterprise | Microsoft Azure"
	description="Découvrez les API disponibles dans PowerApps et comment les enregistrer dans le portail Azure"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="01/21/2016"
   ms.author="guayan"/>

# Inscrire une API gérée par Microsoft ou gérée par le service informatique
Il existe des API **gérées par Microsoft** et **gérées par le service informatique**. Quand vous activez PowerApps Enterprise, les API gérées par Microsoft sont disponibles automatiquement. La mémoire, la connectivité, les approbations, ainsi que d’autres paramètres, sont également gérés automatiquement. L’étape suivante consiste à entrer des paramètres utilisateur spécifiques, comme un compte et un mot de passe Twitter.

Grâce aux API gérées par le service informatique, vous pouvez contrôler et surveiller l’ensemble des paramètres, tels que la mémoire, la connectivité, les approbations, et bien plus encore. Les API gérées par le service informatique comprennent également les API qui peuvent se connecter à un système local, tel que SQL Server et SharePoint Server.

Pour utiliser les API **gérées par Microsoft** ou **gérées par le service informatique**, vous devez les inscrire dans le portail Azure. Une fois inscrites, vous pourrez les utiliser dans vos applications. Les options suivantes sont disponibles :

- Inscrire une API gérée par Microsoft préconstruite ou une API gérée par le service informatique (dans cette rubrique)
- Inscrire une application web, une application API et une application mobile hébergées dans [votre environnement App Service Environment](powerapps-register-api-hosted-in-app-service.md)
- Inscrire l’une de vos propres API Swagger à l’aide d’une [définition d’API Swagger 2.0](powerapps-register-existing-api-from-api-definition.md)

Cet article concerne l’**inscription des API gérées par Microsoft préconstruites et des API gérées par le service informatique**.

#### Conditions préalables

- Inscrivez-vous à [PowerApps Enterprise](powerapps-get-started-azure-portal.md).
- Créez un [environnement App Service](powerapps-get-started-azure-portal.md).


## Afficher les API disponibles gérées par Microsoft
Les API **gérées par Microsoft** sont fournies avec PowerApps Enterprise et sont également hébergées par Microsoft. Les API gérées par Microsoft sont souvent idéales pour vos applications. Par exemple, si votre application envoie un tweet, charge un fichier sur OneDrive ou affiche les données d’un fichier Excel, les API gérées par Microsoft constituent un bon choix.

Les API gérées par Microsoft offrent également les avantages suivants :

- Vous disposez de toutes les API gérées par Microsoft disponibles pour inscrire votre propre instance. 
- Les ressources, telles que le réseau, la mémoire ou les configurations de sécurité, sont surveillées de manière automatique. Par exemple, si vous avez besoin de davantage de mémoire pour afficher des données Excel dans votre application, de la mémoire est automatiquement ajoutée. 
- Une relation d’approbation est automatiquement créée entre votre application et l’API (par exemple, Office ou Twitter). 


#### API gérées par Microsoft

API | Description | Lien vers la procédure
--- | --- | ---
![][31] | **Dropbox**<br/><br/> Vous pouvez obtenir, mettre à jour ou supprimer des éléments, et bien plus encore. | [**Prise en main**](powerapps-create-api-dropbox.md)
![][32] | **Dynamics CRM Online**<br/><br/> Vous pouvez obtenir, mettre à jour ou supprimer des éléments, et bien plus encore. | [**Prise en main**](powerapps-create-api-crmonline.md)
![][33] | **Excel**<br/><br/> Vous pouvez obtenir, mettre à jour ou supprimer des éléments, et bien plus encore. | [**Prise en main**](powerapps-create-api-excel.md)
![][34] | **Google Drive**<br/><br/> Vous pouvez obtenir, mettre à jour ou supprimer des éléments, et bien plus encore. | [**Prise en main**](powerapps-create-api-googledrive.md)
![][35] | **Microsoft Translator**<br/><br/> Traduit du texte, détecte les langues, et bien plus encore. | [**Prise en main**](powerapps-create-api-microsofttranslator.md)
![][36] | **Office 365 Outlook**<br/><br/>Gérer votre courrier électronique. | [**Prise en main**](powerapps-create-api-office365-outlook.md)
![][37] | **Utilisateurs Office 365**<br/><br/> Accédez aux profils utilisateur, à leurs gestionnaires et à leurs collaborateurs directs, et bien plus encore. | [**Prise en main**](powerapps-create-api-office365-users.md)
![][38] | **OneDrive**<br/><br/> Vous pouvez obtenir, mettre à jour ou supprimer des éléments, et bien plus encore. | [**Prise en main**](powerapps-create-api-onedrive.md)
![][39] | **Salesforce**<br/><br/> Vous pouvez obtenir, mettre à jour ou supprimer des éléments, et bien plus encore. | [**Prise en main**](powerapps-create-api-salesforce.md)
![][40] | **SharePoint Online**<br/><br/> Vous pouvez obtenir, mettre à jour ou supprimer des éléments, et bien plus encore. | [**Prise en main**](powerapps-create-api-sharepointonline.md)
![][43] | **Twitter**<br/><br/> Envoyez et recherchez des tweets, affichez les abonnés, et bien plus encore. | [**Prise en main**](powerapps-create-api-twitter.md)


## Afficher les API disponibles gérées par le service informatique
Les API **gérées par le service informatique** sont contrôlées et gérées par vous. Elles ne s’exécutent pas dans l’environnement géré par Microsoft. Dans certains cas, l’utilisation de ces API dans votre propre environnement informatique peut répondre aux besoins de vos applications. Mettons, par exemple, que votre application utilise l’API Twitter et que vous deviez utiliser la clé Twitter de votre organisation (au lieu de la clé Twitter de Microsoft). Dans ce cas, il est préférable de configurer l’API Twitter comme une API gérée par le service informatique. Dans un autre exemple, votre application pourrait utiliser l’API SQL Server pour se connecter à une base de données locale. Dans un environnement géré par le service informatique, vous pouvez configurer un réseau virtuel ou utiliser Express Route pour vous connecter à un système local. C’est à vous de décider.

Les API gérées par Microsoft offrent également les avantages suivants :

- Les ressources, telles que le réseau, la mémoire ou les configurations de sécurité, sont surveillées par vous-même. Par exemple, si vous avez besoin de davantage de mémoire pour afficher des données Excel dans votre application, vous pouvez contrôler la quantité de mémoire à ajouter dans votre environnement. 
- Vous configurez la relation d’approbation et contrôlez la sécurité entre vos applications et l’API. Par exemple, vous pouvez choisir si l’API Office 365 peut être gérée par Microsoft (relation d’approbation automatique) ou utiliser l’API Office 365 dans votre propre environnement (création de votre propre relation d’approbation). 
- **Toutes** les API gérées par Microsoft peuvent également être gérées par le service informatique. Par exemple, vous pouvez créer votre propre instance d’Office 365 et disposer d’un contrôle total sur celle-ci. Vous pouvez ensuite utiliser votre API Office 365 gérée par le service informatique et l’API Office 365 gérée par Microsoft dans le même environnement. Cela dépend des besoins de votre application.
- Quand vous vous connectez à un système local ou quand vous utilisez l’API Recherche Bing, vous contrôlez la sécurité, l’authentification, les licences, et bien plus encore.


#### API gérées par le service informatique
> [AZURE.NOTE] N’oubliez pas que **toutes** les API gérées par Microsoft peuvent également être gérées par le service informatique. Les API suivantes sont uniquement gérées par le service informatique et ne peuvent pas être gérées par Microsoft.

API | Description | Lien vers la procédure
--- | --- | ---
![][30] | **Recherche Bing**<br/><br/>Incorporez des résultats de recherche, ajoutez des fonctionnalités de recherche, et bien plus encore. | [**Prise en main**](powerapps-create-api-bingsearch.md)
![][42] | **SQL Server**<br/><br/>Vous pouvez obtenir, mettre à jour ou supprimer des éléments, et bien plus encore. | [**Prise en main**](powerapps-create-api-sqlserver.md)
![][41] | **SharePoint Server**<br/><br/>Vous pouvez obtenir, mettre à jour ou supprimer des éléments, et bien plus encore. | [**Prise en main**](powerapps-create-api-sharepointserver.md)


#### Pourquoi enregistrer vos propres instances

Les API out-of-box gérées par Microsoft sont pratiques. Toutefois, le fait d’inscrire vos propres instances en tant qu’API gérées par le service informatique présente de nombreux avantages. En général, il est recommandé de créer des API gérées par le service informatique quand vous voulez :

- gérer entièrement les API, notamment l’accès utilisateur, la sécurité lors de la connexion à d’autres systèmes, les limites d’appels d’API, la surveillance et les fonctionnalités avancées telles que les stratégies, et bien plus encore ;
- accéder aux données locales étant donné qu’App Service Environment prend en charge les réseaux virtuels ;
- configurer les API pour les utilisateurs professionnels qui ne pourront peut-être pas les utiliser sans assistance.

Le tableau suivant compare les fonctionnalités des API gérées par Microsoft et celles des API gérées par le service informatique :

| Fonctionnalité | Gérées par Microsoft | Gérées par le service informatique |
| ---------- | ----------------- | ------------ |
| Limites d’appels d’API | Défini par Microsoft | Défini par vous-même (au moyen de stratégies) |
| Apportez votre propre clé pour les connexions aux applications SaaS | Non pris en charge | Pris en charge |
| Accès utilisateur à l’API | Activé pour tous les utilisateurs | Entièrement gérable au niveau du groupe et au niveau de l’utilisateur AAD |
| Surveillance des API | Non pris en charge | Pris en charge |
| Stratégies des API | Non pris en charge | Pris en charge |
| Accès utilisateur à la connexion | Affichage uniquement | Entièrement gérable au niveau du groupe et au niveau de l’utilisateur AAD |
| Gestion des connexions | Affichage uniquement | Entièrement gérable |


## Inscrire une API gérée par Microsoft ou gérée par le service informatique

1. Dans le [portail Azure](https://portal.azure.com/), connectez-vous avec votre compte professionnel (*yourUserName*@*YourCompany*.com). Vous êtes automatiquement connecté à l’abonnement de votre société.
2. Sélectionnez **Parcourir**, **PowerApps**, puis **Gérer les API**: ![][17]
3. Dans Gérer les API, sélectionnez **Ajouter**: ![][18]  
4. Dans **Ajouter une API**, entrez les propriétés de l’API :  

	- Dans **Nom**, entrez un nom pour votre API. Notez que le nom que vous entrez est inclus dans l’URL d’exécution de l’API. Utilisez un nom descriptif qui soit unique au sein de votre organisation.
	- Dans **Source**, sélectionnez **À partir des API disponibles**: ![][19]
5. Sélectionnez **API**, puis choisissez l’API que vous voulez inscrire : ![][20]
6. Sélectionnez une API, puis ajoutez des propriétés configurables.
7. Sélectionnez **Ajouter** pour terminer ces étapes.

> [AZURE.TIP] Quand vous inscrivez une API, vous l’inscrivez dans votre environnement App Service. Une fois dans l’environnement App Service, elle peut être utilisée par d’autres applications de l’environnement.


## Résumé et étapes suivantes

Dans cette rubrique, vous avez vu comment inscrire vos propres instances des API out-of-box fournies avec PowerApps. Pour plus d’informations sur PowerApps, consultez les rubriques et ressources connexes suivantes :


- [Configurer les propriétés de l’API](powerapps-configure-apis.md)
- [Octroyer aux utilisateurs l’accès aux API](powerapps-manage-api-connection-user-access.md)
- [Créer des applications dans PowerApps](https://powerapps.microsoft.com/tutorials/)


<!--References-->

[17]: ./media/powerapps-register-from-available-apis/registered-apis-part.png
[18]: ./media/powerapps-register-from-available-apis/add-api-button.png
[19]: ./media/powerapps-register-from-available-apis/add-api-blade.png
[20]: ./media/powerapps-register-from-available-apis/add-api-select-from-marketplace-blade.png
[30]: ./media/powerapps-register-from-available-apis/bingsearch.png
[31]: ./media/powerapps-register-from-available-apis/dropbox.png
[32]: ./media/powerapps-register-from-available-apis/dynamicscrmonline.png
[33]: ./media/powerapps-register-from-available-apis/excel.png
[34]: ./media/powerapps-register-from-available-apis/googledrive.png
[35]: ./media/powerapps-register-from-available-apis/microsofttranslator.png
[36]: ./media/powerapps-register-from-available-apis/office365outlook.png
[37]: ./media/powerapps-register-from-available-apis/office365users.png
[38]: ./media/powerapps-register-from-available-apis/onedrive.png
[39]: ./media/powerapps-register-from-available-apis/salesforce.png
[40]: ./media/powerapps-register-from-available-apis/sharepointonline.png
[41]: ./media/powerapps-register-from-available-apis/sharepointserver.png
[42]: ./media/powerapps-register-from-available-apis/sqlserver.png
[43]: ./media/powerapps-register-from-available-apis/twitter.png

<!---HONumber=AcomDC_0224_2016-->