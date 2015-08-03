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
	ms.date="07/06/2015" 
	ms.author="robb"/>

#Architecture d’application sur Microsoft Azure
Ressources pour la création d'applications basées sur Microsoft Azure.

##Modèles de conception architecturale d’Azure
Microsoft publie une série de modèles de conception architecturale pour vous aider à composer vos propres créations personnalisées. Les modèles sont conçus pour être des guides architecturaux concis pouvant être combinés pour fournir des conseils sur la meilleure façon d’exploiter la plateforme Microsoft Azure pour répondre aux besoins professionnels de votre organisation.


[Vue d’ensemble](../azure-architectures-cpif-overview/) - [Mise en réseau hybride](../azure-architectures-cpif-infrastructure-hybrid-networking/) - [Traitement par lots hors site](../azure-architectures-cpif-foundation-offsite-batch-processing-tier/) - [Niveau de données multi-sites](../azure-architectures-cpif-foundation-multi-site-data-tier/) - [Niveau global du web à charge équilibrée](../azure-architectures-cpif-foundation-global-load-balanced-web-tier/) - [Niveau Azure Search](../azure-architectures-cpif-foundation-azure-search-tier/)
 
Chaque modèle contient :
 
- Une description de service
- Une liste des services Azure requis pour tirer parti du modèle
- Des diagrammes architecturaux
- Des dépendances architecturales
- Des limitations de conception ou des considérations qui peuvent avoir un impact sur le modèle
- Des interfaces et des points de terminaison
- Des anti-modèles
- Des considérations clés d’ordre général sur l’architecture, notamment sur la disponibilité et la résilience, les contrats SLA pour les services utilisés, la mise à l’échelle et les performances, les coûts et le fonctionnement.

##Plans d'architecture Microsoft

Microsoft publie un ensemble de plans d’architecture de haut niveau qui montrent comment créer des types spécifiques de systèmes à l’aide de produits Microsoft et de services Microsoft Azure. Chaque plan comprend un fichier Visio 2D que vous pouvez télécharger et modifier, un fichier PDF 3D plus coloré pour présenter le plan, ainsi qu’une vidéo qui décrit pas à pas la version 3D. Consultez la page [Plans d’architecture Microsoft](http://msdn.microsoft.com/dn630664).

Les diagrammes de plan 2D utilisent le jeu de symboles Cloud et Enterprise mentionné ci-dessous.

Les fichiers PDF de plan 3D sont créés dans un outil non-Microsoft, mais un modèle Visio est en cours de développement. Consultez une [vidéo bêta de formation du modèle ici.](http://aka.ms/3dBlueprintTemplate). Pour obtenir la version bêta du modèle de plan 3D Visio, envoyez un courrier électronique à l’adresse [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com).

![Diagrammes 3D des plans d’architecture Microsoft.](./media/architecture-overview/BluePrintThumb.jpg)

##Jeu de symboles/icônes Cloud et Enterprise

[Téléchargez le jeu de symboles/icônes Cloud et Enterprise](http://aka.ms/CnESymbols) pour créer des documents techniques qui décrivent Azure, Windows Server, SQL Server et autres produits Microsoft. Vous pouvez les utiliser dans les diagrammes d’architecture, les supports de formation, les présentations, les fiches techniques, les infographies, les livres blancs et même des ouvrages tiers si le livre est destiné à former des personnes à l’utilisation des produits Microsoft. Les symboles sont au format Visio et PNG. Les instructions d’utilisation des PNG dans PowerPoint sont fournies.

Le jeu de symboles est fourni tous les trimestres et est mis à jour à chaque publication de nouveaux services. Si vous voulez obtenir un aperçu de la dernière version, qui peut inclure des services Azure supplémentaires, envoyez un courrier électronique à l’adresse [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com).

Comme nous apprécions de recevoir des commentaires de nos clients, des instructions sont disponibles à ce sujet dans le téléchargement. Si vous avez utilisé les symboles, répondez à la petite [enquête](http://aka.ms/azuresymbolssurveyv2) en 5 questions ou envoyez-nous un courrier électronique à l’adresse mentionnée plus haut pour nous faire savoir s’ils sont utiles et comment vous vous en servez.

D’autres symboles sont disponibles dans le [gabarit Microsoft Office Visio](http://www.microsoft.com/fr-fr/download/details.aspx?id=35772), même s’ils ne sont pas optimisés pour des plans d’architecture comme le jeu CnE.

![Jeu de symboles/icônes Cloud et Enterprise](./media/architecture-overview/CnESymbols.png)

##Modèles de conception
Microsoft Patterns and Practices a publié le livre [Cloud Design Patterns](http://msdn.microsoft.com/library/dn568099.aspx) (Modèles de conception pour le cloud), disponible à la fois sur MSDN et sous forme de téléchargement au format PDF. Il existe également une affiche grand format qui énumère tous les modèles.

![Poster des modèles cloud de Patterns and Practices](./media/architecture-overview/PnPPatternPosterThumb.jpg)

##Éléments d'infographie relatifs à l'architecture
Microsoft publie plusieurs affiches/éléments d'infographie relatifs à l'architecture. Cela inclut la [création d’applications cloud réalistes](http://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/) et leur [mise à l’échelle à l’aide de Cloud Services](http://azure.microsoft.com/documentation/infographics/cloud-services/).

![Éléments d’infographie relatifs à l’architecture Azure](./media/architecture-overview/AzureArchInfographicThumb.jpg)

<!---HONumber=July15_HO4-->