<properties
	pageTitle="Prise en main d’Azure Search dans NodeJS | Microsoft Azure | Service de recherche cloud hébergé"
	description="Guide de création d’une application de recherche sur un service de recherche Azure hébergée dans le cloud en utilisant le langage de programmation NodeJS."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor="v-lincan"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="11/04/2015"
	ms.author="heidist"/>

# Prise en main d'Azure Search dans NodeJS

Apprenez à créer une application NodeJS personnalisée, qui utilise Azure Search pour ses fonctionnalités de recherche. Ce didacticiel utilise l’[API REST du service Azure Search](https://msdn.microsoft.com/library/dn798935.aspx) pour créer les objets et opérations utilisés dans cet exercice.

Nous avons utilisé [NodeJS](https://nodejs.org) et NPM, [Sublime Text 3](http://www.sublimetext.com/3) et Windows PowerShell sur Windows 8.1 pour développer et tester ce code.

Pour exécuter cet exemple, vous devez disposer d'un service Azure Search, auquel vous pouvez vous connecter dans le [portail Azure](https://portal.azure.com).

> [AZURE.TIP]Téléchargez le code source de ce didacticiel à l’adresse [AzureSearchNodeJSIndexerDemo](http://go.microsoft.com/fwlink/p/?LinkId=530198).

## À propos des données

Cet exemple d'application utilise des données de l’[USGS (United States Geological Services)](http://geonames.usgs.gov/domestic/download_data.htm), concernant l'État de Rhode Island pour réduire la taille du jeu de données. Nous allons utiliser ces données pour créer une application de recherche qui renvoie des bâtiments repères, tels que les hôpitaux et les écoles, ainsi que des caractéristiques géologiques, telles que les ruisseaux, les lacs et les sommets.

Dans cette application, le programme **DataIndexer** crée et charge l'index à l'aide d'une construction de type [Index](https://msdn.microsoft.com/library/azure/dn798918.aspx), en récupérant le jeu de données USGS filtré à partir d'une base de données SQL Azure publique. Les informations d'identification et de connexion à la source de données en ligne sont fournies dans le code du programme. Aucune configuration supplémentaire n'est nécessaire.

> [AZURE.NOTE]Nous avons appliqué un filtre à ce jeu de données pour ne pas dépasser la limite de 10 000 documents du niveau de tarification gratuit. Si vous utilisez le niveau standard, cette limite ne s'applique pas. Pour plus d'informations sur la capacité de chaque niveau de tarification, consultez la section [Limites et contraintes](search-limits-quotas-capacity.md).

## Créer le service

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la barre de lancement, cliquez sur **Nouveau** > **Données + stockage** > **Recherche**.

     ![][1]

3. Définissez le nom du service, le niveau de tarification, le groupe de ressources, l'abonnement et l'emplacement. Ces paramètres sont requis et ne sont plus modifiables une fois le service approvisionné.

     ![][2]

	- Le **nom du service** doit être unique, en minuscules et contenir moins de 15 caractères sans espaces. Il devient partie intégrante du point de terminaison de votre service Azure Search. Consultez [Règles d'affectation des noms](https://msdn.microsoft.com/library/azure/dn857353.aspx) pour plus d'informations sur les conventions d'affectation des noms.

	- Le **niveau de tarification** détermine la capacité et la facturation. Les deux niveaux offrent les mêmes fonctionnalités, mais à des niveaux de ressources différents.

		- La **version gratuite** s'applique aux clusters partagés avec d'autres abonnés. Elle offre une capacité suffisante pour tester les didacticiels et écrire du code de validation technique, mais elle n'est pas destinée aux applications de production. En général, le déploiement d'un service gratuit ne prend que quelques minutes.
		- La version **standard** s'applique aux ressources dédiées et est hautement évolutive. À l'origine, un service standard est fourni avec un réplica et une partition, mais vous pouvez ajuster la capacité une fois le service créé. Le déploiement d'un service standard prend plus de temps, environ quinze minutes.

	- Les **groupes de ressources** sont des conteneurs pour les services et les ressources utilisés dans un but commun. Par exemple, si vous créez une application de recherche personnalisée basée sur Azure Search, Sites web Azure ou le stockage d'objets blob, vous pouvez créer un groupe de ressources qui réunit ces services dans les pages de gestion du portail.

	- L’**abonnement** vous permet de choisir votre abonnement, si vous en avez plusieurs.

	- L’**emplacement** correspond à la zone géographique du centre de données. Actuellement, toutes les ressources doivent s'exécuter dans le même centre de données. La répartition de ressources entre plusieurs centres de données n'est pas prise en charge.

4. Cliquez sur **Créer** pour approvisionner le service.

Surveillez les notifications dans la barre de lancement. Une notification s'affiche lorsque le service est prêt à être utilisé.

<a id="sub-2"></a>
## Rechercher le nom et la clé API de votre service Azure Search

Une fois le service créé, revenez au portail pour obtenir l'URL ou `api-key`. Pour vous connecter à votre service de recherche, vous devez saisir l'URL et une `api-key` afin d’authentifier l'appel.

1. Dans la barre de lancement, cliquez sur **Accueil** puis sur le service de recherche pour ouvrir le tableau de bord du service.

2. Le tableau de bord des services affiche des vignettes contenant des informations essentielles, ainsi que l'icône de clé permettant d'accéder aux clés administrateur.

  	![][3]

3. Copiez l'URL du service, une clé d’administration et une clé de requête. Vous en aurez besoin plus tard, pour les ajouter au fichier config.js.

## Télécharger les fichiers exemples

Utilisez l'une des approches suivantes pour télécharger l'exemple.

1. Accédez à [AzureSearchNodeJSIndexerDemo](http://go.microsoft.com/fwlink/p/?LinkId=530198).
2. Cliquez sur **Download ZIP**, enregistrez le fichier ZIP sur le disque, puis extrayez tous les fichiers qu'il contient.

Toutes les modifications et instructions d'exécution ultérieures seront effectuées sur les fichiers de ce dossier.

Si vous instruction de chemin contient GIT, vous pouvez ouvrir une fenêtre PowerShell et taper `git clone https://github.com/EvanBoyle/AzureSearchNodeJSIndexerDemo.git`.

## Mettre à jour le fichier config.js. avec l’URL et la clé API du service de recherche

À l'aide de l'URL et des clés API que vous avez copiées précédemment, spécifiez l'URL, la clé d’administration et la clé de requête dans le fichier de configuration.

Les clés d’administration octroient un contrôle total sur les opérations du service, notamment la création ou la suppression d'un index et le chargement de documents. En revanche, les clés de requête sont réservées aux opérations en lecture seule, généralement utilisées par les applications clientes qui se connectent à Azure Search.

Dans cet exemple, nous incluons la clé de requête pour renforcer la bonne pratique consistant à utiliser la clé de requête dans les applications clientes.

La capture d'écran suivante montre le fichier **config.js** ouvert dans un éditeur de texte, avec les entrées appropriées encadrées en rouge pour que vous puissiez voir où mettre à jour le fichier avec les valeurs correspondant à votre service de recherche.

![][5]


## Héberger un environnement d’exécution pour l’exemple

Cet exemple nécessite un serveur HTTP, que vous pouvez installer globalement à l'aide de npm.

Exécutez les commandes suivantes dans une fenêtre PowerShell :

1. Accédez au dossier qui contient le fichier **package.json**.
2. Saisissez `npm install`.
2. Saisissez `npm install -g http-server`.

## Générer l’index et exécuter l'application

1. Saisissez `npm run indexDocuments`.
2. Saisissez `npm run build`.
3. Saisissez `npm run start_server`.
4. Dans votre navigateur, accédez à `http://localhost:8080/index.html`.

## Exécuter une recherche sur les données USGS

Le jeu de données USGS comprend les enregistrements qui correspondent à l'État de Rhode Island. Si vous cliquez sur **Search** et que le champ de recherche est vide, vous obtiendrez les 50 premières entrées, ce qui correspond au paramétrage par défaut.

Saisissez un terme pour que le moteur de recherche puisse travailler. Essayez d'entrer le nom d’une figure locale. « Roger Williams » fut le premier gouverneur de Rhode Island. De nombreux parcs, bâtiments et écoles portent son nom.

![][9]

Vous pouvez également essayer les termes suivants :

- Pawtucket
- Pembroke
- goose +cape


## Étapes suivantes

Ceci est le premier didacticiel Azure Search basé sur NodeJS et le jeu de données USGS. Au fil du temps, nous le compléterons avec des fonctionnalités de recherche supplémentaires que vous souhaiterez peut-être utiliser dans vos solutions personnalisées.

Si vous connaissez déjà Azure Search, vous pouvez utiliser cet exemple comme tremplin pour tester des générateurs de suggestions (requêtes prédictives ou à saisie semi-automatique), des filtres et la navigation à facettes. Vous pouvez également améliorer la page des résultats de la recherche en ajoutant des décomptes et en traitant les documents par lots afin que les utilisateurs puissent parcourir les résultats.

Vous découvrez Azure Search ? Nous vous recommandons de suivre les autres didacticiels pour comprendre ce que vous pouvez créer. Consultez les autres ressources disponibles dans notre [page de documentation](http://azure.microsoft.com/documentation/services/search/). Vous pouvez également cliquer sur les liens dans notre [liste de vidéos et de didacticiels](search-video-demo-tutorial-list.md) pour obtenir des informations supplémentaires.

<!--Image references-->
[1]: ./media/search-get-started-nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-nodejs/AzSearch-NodeJS-configjs.png
[9]: ./media/search-get-started-nodejs/rogerwilliamsschool.png

<!---HONumber=Nov15_HO3-->