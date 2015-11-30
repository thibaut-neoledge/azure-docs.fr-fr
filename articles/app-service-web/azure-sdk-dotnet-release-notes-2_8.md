
<properties 
   pageTitle="Notes de publication du Kit de développement logiciel (SDK) Azure pour .NET 2.8" 
   description="Notes de publication du Kit de développement logiciel (SDK) Azure pour .NET 2.8" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="11/18/2015"
   ms.author="juliako"/>

# Kit de développement logiciel (SDK) Azure pour .NET 2.8

##Vue d'ensemble
 
Cet article contient les notes de publication (qui incluent les problèmes connus et les dernières modifications) pour le Kit de développement logiciel (SDK) Azure .NET version 2.8.

Pour obtenir une liste complète des nouvelles fonctionnalités et mises à jour disponibles dans cette version, consultez l’annonce relative au [Kit de développement logiciel (SDK) 2.8 pour Visual Studio 2013 et Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## Télécharger le Kit de développement logiciel (SDK) Azure pour .NET 2.8

[Kit de développement logiciel (SDK) Azure .NET 2.8 pour Visual Studio 2015](http://go.microsoft.com/fwlink/?LinkId=699285)

[Kit de développement logiciel (SDK) Azure .NET 2.8 pour Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=699287)
 
## Prise en charge de .NET 4.5.2 

###Problèmes connus

Le Kit de développement logiciel (SDK) Azure 2.8 pour .NET vous permet de créer des packages de services cloud .NET 4.5.2. Toutefois, .NET Framework 4.5.2 ne sera pas installé sur les images de système d’exploitation invité par défaut avant la publication du système d’exploitation invité de janvier 2016. Avant cela, .NET Framework 4.5.2 sera disponible via une version de système d’exploitation invité distincte, celle de novembre 2015-02. Consultez la page [Versions du SE invité et matrice de compatibilité du Kit de développement logiciel (SDK) Azure](http://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/). Une fois l’image de novembre 2015-02 publiée, vous pourrez choisir d’utiliser cette image en mettant à jour votre fichier .cscfg. Dans le fichier de configuration du service, définissez l’attribut osVersion de l’élément ServiceConfiguration sur la chaîne WA-GUEST-OS-4.26\_201511-02.


##Azure Data Factory

###Problèmes connus 

Pendant la création d’un **modèle Data Factory** impliquant des exemples de données, le script Azure PowerShell peut échouer si la version installée sur l’ordinateur est ultérieure à la version 0.9.8.

Pour créer correctement ce type de projet, vous devez installer la [version Azure PowerShell 0.9.8](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi).


## Outils du gestionnaire de ressources Azure 

###Dernières modifications

Le script PowerShell fourni par le projet du groupe de ressources Azure a été mis à jour dans cette version pour fonctionner avec les nouvelles applets de commande Azure PowerShell, version 1.0. Ce nouveau script ne fonctionnera pas dans Visual Studio si vous utilisez une version du Kit de développement logiciel (SDK) antérieure à 2.8.

Les scripts issus des projets créés dans les versions antérieures du Kit de développement logiciel (SDK) ne s’exécuteront pas dans Visual Studio en cas d’utilisation du Kit de développement logiciel (SDK) 2.8. Tous les scripts continueront de fonctionner en dehors de Visual Studio avec la version appropriée des applets de commande Azure PowerShell.

Le Kit de développement logiciel (SDK) 2.8 requiert la version 1.0 des applets de commande Azure PowerShell. Toutes les autres versions du Kit de développement logiciel (SDK) nécessitent la version 0.9.8 des applets de commande Azure PowerShell. Pour plus d’informations, consultez [ce blog](http://go.microsoft.com/fwlink/?LinkID=623011).

##Outils Azure HDInsight

Les nouvelles mises à jour sont les suivantes :

- Vous pouvez exécuter votre requête Hive dans le cluster via HiveServer2 avec presque aucune surcharge et voir que le travail est consigné en temps réel.
- En utilisant la nouvelle vue d’exécution des tâches Hive, vous pouvez afficher les détails du travail et identifier les problèmes potentiels.

Pour plus d’informations, consultez la page relative au [Kit de développement logiciel (SDK) Azure 2.8 pour Visual Studio 2013 et Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

##Web Tools Extensions

###Problèmes connus

Les problèmes connus suivants seront résolus dans la prochaine version.

- Les mouvements de l’Explorateur de serveurs et du cloud en lien avec App Service pour les environnements autres que ceux de production (comme les clients Azure China ou Azure Stack) ne fonctionnent pas. Pour les clients concernés, le téléchargement du profil de publication à partir du portail Azure permettra d’activer la fonctionnalité de publication. Une version ultérieure permettra de réparer les mouvements tels que « Attacher le débogueur » et « Afficher les journaux de diffusion en continu » pour les clients Azure China et Stack. 
- Les clients peuvent voir des erreurs lors de la création d’un service d’application lorsque l’instance App Insights vers laquelle ils effectuent le déploiement est située dans une région différente de la région Est des États-Unis. Dans ces scénarios, la création d’un service d’application dans le portail et le téléchargement du profil de publication autoriseront les scénarios de publication. 


##Voir aussi

[Billet d’annonce du Kit de développement logiciel (SDK) Azure 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[Informations relatives à la prise en charge et au retrait pour le Kit de développement logiciel Azure SDK pour .NET et les API](https://msdn.microsoft.com/library/azure/dn479282.aspx)

<!---HONumber=Nov15_HO4-->