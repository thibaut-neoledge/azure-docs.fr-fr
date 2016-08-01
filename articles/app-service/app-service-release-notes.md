<properties 
   pageTitle="Notes de publication du Kit de développement logiciel (SDK) Azure pour .NET 2.5.1" 
   description="Notes de publication du Kit de développement logiciel (SDK) Azure pour .NET 2.5.1" 
   services="app-service" 
   documentationCenter=".net,nodejs,java" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="07/18/2016"
   ms.author="juliako"/>


# Notes de publication du Kit de développement logiciel (SDK) Azure pour .NET 2.5.1

Ce document contient les notes de publication du Kit de développement logiciel (SDK) Azure pour .NET version 2.5.1.

##Notes de publication du Kit de développement logiciel (SDK) Azure pour .NET 2.5.1

Les nouvelles fonctionnalités et mises à jour intégrées dans le Kit de développement logiciel (SDK) Azure pour .NET 2.5.1 sont présentées ci-dessous.

- Nouvelles fonctionnalités\\nouveaux scénarios associés aux **extensions d’outils web**.

	- Azure Web Sites a été renommé Azure App Service. Pour plus d’informations, consultez [Azure App Service et services Azure existants](app-service-changes-existing-services.md).
	- La prise en charge des applications API Azure (version préliminaire) a été ajoutée afin que les clients puissent publier des projets ASP.NET en tant qu'applications API, puis utiliser le mouvement Ajouter > Azure API App Client dans les projets C# pour générer du code basé sur la structure de l'application API déployée.
	- Le nœud Websites dans l'Explorateur de serveurs a été déconseillé en lieu et place du nœud Azure App Service, qui prend en charge le regroupement basé sur le groupe de ressources des applications API, mobiles et web d'Azure.
	- La prise en charge des applications mobiles Azure (version préliminaire) a été ajoutée afin que les clients puissent créer des projets Mobile Apps, ajouter des contrôleurs Mobile Apps, publier les projets et déboguer à distance des applications.
	- Le mouvement Ajouter > Azure API App Client prend désormais en charge les fichiers Swagger JSON locaux, afin que les développeurs d'API web puissent utiliser des packages NuGet tiers tels que Swashbuckle pour générer du code Swagger ou le créer manuellement. De cette façon, les développeurs d'applications clientes peuvent utiliser les fonctionnalités de génération de code pour utiliser n'importe quel point de terminaison Swagger dans des projets C#.
	- Les boîtes de dialogue de publication d'applications web et API ont été améliorées pour prendre en charge le concept du portail Azure de regroupement des ressources, et la sélection/création de groupes de ressources Azure et de plans App Service sont représentées dans la nouvelle boîte de dialogue d'approvisionnement d'applications web et API.
	- Les nœuds de l'Explorateur de serveurs des applications API Azure fournissent des liens vers le lien profond des applications API dans le portail Azure, ainsi que d'autres fonctionnalités telles que la diffusion en continu des journaux et le débogage à distance.

	Pour les problèmes connus et les limites actuelles dans le Kit de développement logiciel (SDK) Azure pour .NET 2.5.1, consultez la [section](app-service-release-notes.md#known_issues_2_5_1) ci-dessous.


- De nouvelles fonctionnalités\\nouveaux scénarios associés aux **outils HDInsight** dans Visual Studio sont activés dans cette version.
	- Validation locale des scripts Hive. Cliquez sur le bouton Valider le script dans la barre d'outils pour voir si votre script contient des erreurs.
	- Débogage amélioré des tâches Hive. Vous pouvez maintenant déboguer les tâches Hive en accédant aux journaux Yarn dans Visual Studio. Si votre application présente des problèmes de performances, l'examen des journaux Yarn fournit des informations utiles.
	- (Version préliminaire publique) Prise en charge d'IntelliSense et de la saisie semi-automatique des mots clés pour Hive. Pour vous aider à créer des scripts Hive, HDInsight Tools pour Visual Studio a ajouté la prise en charge d'IntelliSense et de la saisie semi-automatique des mots clés pour Hive.
	- Prise en charge de Storm. Vous pouvez désormais utiliser HDInsight Tools pour Visual Studio pour développer des topologies/spouts/bolts Storm en C#. Vous pouvez ensuite soumettre la topologie développée à un cluster Storm et voir l'état de la topologie/bolt/spout. Vous pouvez utiliser les journaux système et les journaux client pour dépanner vos topologies/bolts/spouts Storm. Vous pouvez également utiliser les ressources JAVA existantes dans Storm sur HDInsight.
	
	Pour plus d’informations, consultez la page [Prise en main de HDInsight Hadoop Tools pour Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).



##<a id="known_issues_2_5_1"></a>Limitations et problèmes connus du Kit de développement logiciel (SDK) Azure pour .NET 2.5.1

- Azure API Apps apparaît sous la forme d'une cible de déploiement pour Mobile Apps. Web Apps devrait être la seule destination pour Mobile Apps jusqu'à une version suivante.
- La mise en service d'Azure API App peut se terminer avec succès mais échouer de façon intermittente lors de la mise à jour de la progression dans la fenêtre d'activité d'Azure App Service. Une solution de contournement consiste à vérifier l'état de la nouvelle application API Azure dans le portail Azure.
- La séquence Fichier > Nouveau projet > Application API > F5 provoque une erreur HTTP, car il n'existe aucun fichier default/index.html. Une solution de contournement consiste à accéder manuellement à l'URL /api/values.
- Par intermittence, les icônes de l'Explorateur de serveurs apparaissent aplaties. Le redémarrage de Visual Studio résout ce problème.
- Si une exception est levée au cours de la mise en service de l'application web ou API (par exemple, des erreurs de dépassement de quota ou un nom de passerelle Azure API App en double), les erreurs affichent du texte JSON brut.
- Problèmes intermittents de création de projet quand Application Insights est vérifié au moment de la création du projet.
- Parfois, des espaces de noms manquent dans le code Azure API App Client généré. Il convient de les inclure manuellement (ou de les importer automatiquement via des signaux Visual Studio) pour compiler le code.
- Les projets d'application mobile doivent être publiés en applications web, mais vous devez choisir un site créé comme une application mobile dans le portail Azure, dans la mesure où les projets d'application mobile requièrent une base de données.
- La page de démarrage pour Mobile Apps utilise le terme « service mobile » au lieu du terme « applications mobiles »
- La création du projet d'application mobile peut prendre une minute.
- Pendant la mise en service de l'application API (dans certains cas), une erreur est renvoyée à partir de l'API Azure, indiquant que les autorisations n'ont pas pu être définies correctement, alors que l'application API a été correctement configurée et est prête à être utilisée. Vous pouvez définir manuellement les autorisations à l'aide du portail Azure.
- Application Insights n'est pas pris en charge sur les modèles d'application API et les modèles d'application mobile.
- Les projets d'application API ne peuvent pas être utilisés conjointement avec des projets de service cloud.
- Les modèles de projet d'application API sont disponibles uniquement en C#.
- L'utilisation de l'application API via le menu contextuel « Ajouter Azure API App Client » est prise en charge uniquement en C#.

 

<!---HONumber=AcomDC_0720_2016-->