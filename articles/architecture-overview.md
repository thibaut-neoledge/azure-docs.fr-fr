<properties 
	pageTitle="Architecture d’application sur Microsoft Azure" 
	description="Architecture overview that covers common design patterns" 
	services="" 
	documentationCenter="" 
	authors="Rboucher" 
	manager="jwhit" 
	editor="mattshel"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015" 
	ms.author="robb"/>

#Architecture d’application sur Microsoft Azure
Ressources pour la création d'applications basées sur Microsoft Azure. Cela inclut des outils pour vous aider à dessiner des diagrammes pour décrire visuellement les systèmes logiciels.

##Cours de certification d'architecture Microsoft

Microsoft vient de publier un nouveau cours d'architecture prenant en charge l'examen de certification 70-534 de Microsoft. Il est [disponible gratuitement sur EDX.ORG](https://www.edx.org/course/architecting-microsoft-azure-solutions-microsoft-dev205x). Il utilise le nouveau [modèle 3D Blueprint Visio](#3d-blueprint-visio-template).

![Cours de certification d'architecture Microsoft](./media/architecture-overview/EDXCourse.png)

##Plans d'architecture Microsoft

Microsoft publie un ensemble de [plans d'architecture](http://aka.ms/azblueprints) de haut niveau qui montrent comment créer des types spécifiques de systèmes à l'aide des produits Microsoft.

Chaque plan inclut les éléments suivants :

- Fichier **2D Visio** 2003 plat que vous pouvez télécharger et modifier. 
- Fichier **PDF de perspective 3D** en couleur pour présenter le plan à un public moins technique.
- Une **vidéo** qui vous guide dans la version 3D. 

Les plans utilisent le [jeu de symboles Cloud et Enterprise](#symbol-and-icon-sets).

![Diagrammes 3D des plans d’architecture Microsoft.](./media/architecture-overview/BluePrintThumb.jpg)



##Modèle 3D Blueprint Visio
Les versions 3D des [plans d'architecture Microsoft](http://aka.ms/azblueprints) ont initialement été créés avec un outil autre que Microsoft, mais un nouveau modèle Visio 2013 a été publié le 5 août 2015 dans le cadre d'un [cours de certification d'architecture Microsoft distribué sur EDX.ORG](#microsoft-architecture-certification-course).

Le modèle est également disponible en dehors du cours.

- Commencez par [visionner la vidéo de formation](http://aka.ms/3dBlueprintTemplateVideo) afin de savoir ce qu'il peut faire.   
- Téléchargez le [modèle Microsoft 3D Blueprint Visio](http://aka.ms/3DBlueprintTemplate).
- Téléchargez les [symboles Cloud et Enterprise](#symbol-and-icon-sets) à utiliser avec le modèle 3D.

Envoyez-nous un e-mail à l'adresse [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) si vous avez des questions spécifiques auxquelles les supports de formation n'apportent pas de réponse ou pour envoyer vos commentaires. La facilité d'utilisation est l'un des principaux objectifs du modèle, donc dites-nous ce qui fonctionne et ce qui ne fonctionne pas.

![Modèle Microsoft 3D Blueprint Visio](./media/architecture-overview/3DBlueprintVisioTemplate.jpg)



##Jeux de symboles et d'icônes 

[Visionnez la vidéo de formation sur Visio et les symboles](http://aka.ms/CnESymbolsVideo), puis [téléchargez le jeu de symboles Cloud et Enterprise](http://aka.ms/CnESymbols) pour créer des documents techniques qui décrivent Azure, Windows Server, SQL Server, etc. Vous pouvez utiliser les symboles dans les diagrammes d'architecture, les supports de formation, les présentations, les fiches techniques, les infographies, les livres blancs et même des ouvrages tiers si le livre est destiné à former des personnes à l'utilisation des produits Microsoft. Toutefois, ils ne sont pas destinés à être utilisés dans les interfaces utilisateur.

Les symboles CnE sont aux formats Visio et PNG. Vous trouverez des instructions supplémentaires d'utilisation des PNG dans PowerPoint.

Le jeu de symboles est fourni tous les trimestres et est mis à jour à chaque publication de nouveaux services.

D’autres symboles sont disponibles dans le [gabarit Microsoft Office Visio](http://www.microsoft.com/fr-fr/download/details.aspx?id=35772), même s’ils ne sont pas optimisés pour des plans d’architecture comme le jeu CnE.


**Commentaires :** si vous avez utilisé les symboles CnE, répondez à l'[enquête](http://aka.ms/azuresymbolssurveyv2) qui ne contient que 5 questions ou envoyez-nous un e-mail à l'adresse [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) concernant des questions et des problèmes spécifiques. Nous aimerions connaître votre avis, y compris les commentaires positifs, afin de décider de continuer dans cette voie ou non.


![Jeu de symboles/icônes Cloud et Enterprise](./media/architecture-overview/CnESymbols.png)


##Modèles de conception architecturale d’Azure
Microsoft publie une série de modèles de conception architecturale pour vous aider à composer vos propres créations personnalisées. Les modèles sont conçus pour être des guides architecturaux concis pouvant être combinés pour fournir des conseils sur la meilleure façon d’exploiter la plateforme Microsoft Azure pour répondre aux besoins professionnels de votre organisation.


[Vue d'ensemble](../azure-architectures-cpif-overview/) - [Mise en réseau hybride](../azure-architectures-cpif-infrastructure-hybrid-networking/) - [Traitement par lots hors site](../azure-architectures-cpif-foundation-offsite-batch-processing-tier/) - [Niveau de données multi-sites](../azure-architectures-cpif-foundation-multi-site-data-tier/) - [Niveau global du web à charge équilibrée](../azure-architectures-cpif-foundation-global-load-balanced-web-tier/) - [Niveau Azure Search](../azure-architectures-cpif-foundation-azure-search-tier/)
 
Chaque modèle contient :
 
- Une description de service
- Une liste des services Azure requis pour tirer parti du modèle
- Des diagrammes architecturaux
- Des dépendances architecturales
- Des limitations de conception ou des considérations qui peuvent avoir un impact sur le modèle
- Des interfaces et des points de terminaison
- Des anti-modèles
- Des considérations clés d’ordre général sur l’architecture, notamment sur la disponibilité et la résilience, les contrats SLA pour les services utilisés, la mise à l’échelle et les performances, les coûts et le fonctionnement.


![Modèles de conception architecturale d'Azure](./media/architecture-overview/AzureArchPatterns.jpg)

##Poster Modèles de conception
Microsoft Patterns and Practices a publié le livre [Cloud Design Patterns](http://msdn.microsoft.com/library/dn568099.aspx) (Modèles de conception pour le cloud), disponible à la fois sur MSDN et sous forme de téléchargement au format PDF. Il existe également une affiche grand format qui énumère tous les modèles.

![Poster des modèles cloud de Patterns and Practices](./media/architecture-overview/PnPPatternPosterThumb.jpg)

##Éléments d'infographie relatifs à l'architecture
Microsoft publie plusieurs affiches/éléments d'infographie relatifs à l'architecture. Cela inclut la [création d’applications cloud réalistes](http://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/) et leur [mise à l’échelle à l’aide de Cloud Services](http://azure.microsoft.com/documentation/infographics/cloud-services/).

![Éléments d’infographie relatifs à l’architecture Azure](./media/architecture-overview/AzureArchInfographicThumb.jpg)

<!---HONumber=August15_HO7-->