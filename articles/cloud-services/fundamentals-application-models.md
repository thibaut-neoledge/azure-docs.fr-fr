<properties 
	pageTitle="Calcul des options d’hébergement fournies par Azure" 
	description="Découvrez les options d'hébergement de calcul Azure et leur fonctionnement : Virtual Machines, Sites Web, Cloud Services et bien d’autres." 
	services="cloud-services,virtual-machines"
	authors="Thraka" 
	documentationCenter=""
	manager="timlt"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="adegeo;cephalin;kathydav"/>




# Calcul des options d’hébergement fournies par Azure

Azure propose différents modèles d’hébergement d’applications. Chaque modèle offre différents ensembles de services. Par conséquent, celui que vous choisissez dépend précisément de ce que vous souhaitez faire. Cet article examine les options et décrit chaque technologie en donnant des exemples d’utilisation.

| Options de calcul | Public ciblé |
| ------------------ | --------   |
| [App Service] | Applications Web évolutives, applications mobiles, applications d’API et applications logiques pour n’importe quel appareil |
| [Cloud Services] | Applications cloud à N niveaux, hautement disponibles et évolutives avec plus de contrôle du système d'exploitation |
| [Virtual Machines] | Machines virtuelles Windows et Linux personnalisées avec contrôle total du système d'exploitation |

[AZURE.INCLUDE [Contenu](../../includes/app-service-choose-me-content.md)]

[AZURE.INCLUDE [Contenu](../../includes/cloud-services-choose-me-content.md)]

[AZURE.INCLUDE [Contenu](../../includes/virtual-machines-choose-me-content.md)]

## Autres Options

Azure offre également d’autres modèles d’hébergement de calcul à des fins plus spécialisées, comme les éléments suivants :

* [Mobile Services](/services/mobile-services/) :  
  optimisé pour offrir un serveur principal de cloud pour les applications qui s’exécutent sur des appareils mobiles.
* [Batch](/services/batch/) :  
  optimisé pour le traitement de grands volumes de tâches similaires, dans l’idéal des charges de travail qui se prêtent à l’exécution de tâches en parallèle sur plusieurs ordinateurs.
* [HDInsight (Hadoop)](/services/hdinsight/) :  
  optimisé pour l’exécution de travaux [MapReduce](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options/#hadoop) sur les clusters Hadoop. 

## Que dois-je utiliser ? Faire un choix

Les trois modèles généraux d’hébergement de calcul Azure vous permettent de créer des applications évolutives et fiables dans le cloud. Étant donné cette similitude fondamentale, quel modèle dois-je utiliser ?

App Service est le meilleur choix pour la plupart des applications web. Le déploiement et la gestion sont intégrés à la plateforme, les sites peuvent rapidement gérer des volumes importants de trafic et le gestionnaire d'équilibrage de charge et de trafic assurent une haute disponibilité. Vous pouvez facilement déplacer des sites existants vers App Service avec un [outil de migration en ligne](https://www.migratetoazure.net/), utiliser une application open source de la galerie d’applications web ou bien créer un site avec l’infrastructure et les outils de votre choix. La fonctionnalité [WebJobs](http://go.microsoft.com/fwlink/?linkid=390226) facilite l’ajout de traitement de tâches en arrière-plan à votre application, voire l’exécution d’une charge de travail de calcul qui n’est pas une application web.

Si vous avez besoin d'un plus grand niveau de contrôle sur l'environnement de serveur web, par exemple pour pouvoir vous connecter à distance au serveur ou pour configurer les tâches de démarrage du serveur, Azure Cloud Services est normalement la meilleure option.

Si vous avez une application qui nécessite des modifications importantes pour pouvoir fonctionner dans Sites Web Azure ou Azure Cloud Services, vous pouvez choisir Azure Virtual Machines pour simplifier la migration vers le cloud. Cependant, la configuration, la sécurisation et la gestion des machines virtuelles nécessitent bien plus de temps et de compétences que l'utilisation de Sites Web Azure et Azure Cloud Services. Si vous envisagez d'utiliser Azure Virtual Machines, tenez bien compte du temps de maintenance nécessaire pour mettre à jour, mettre à niveau et gérer votre environnement de machine virtuelle. Si vous avez une application qui nécessite des modifications importantes pour pouvoir fonctionner dans App Service ou Azure Cloud Services, vous pouvez choisir Azure Virtual Machines pour simplifier la migration vers le cloud. Cependant, la bonne configuration, sécurisation et gestion des machines virtuelles nécessite bien plus de temps et de compétences que l’utilisation d’App Service et Azure Cloud Services. Si vous envisagez d'utiliser Azure Virtual Machines, tenez bien compte du temps de maintenance nécessaire pour mettre à jour, mettre à niveau et gérer votre environnement de machine virtuelle.

Parfois, aucune option ne convient seule. Dans de telles situations, il est parfaitement légal de combiner des options. Supposons, par exemple, que vous créez une application dans laquelle vous souhaitez bénéficier des avantages en matière de gestion offerts par les rôles web Cloud Services, tout en ayant besoin également d'utiliser SQL Server standard hébergé sur une machine virtuelle pour des raisons de compatibilité ou de performance.

<!-- In this case, the best option is to combine compute hosting options, as the figure below shows.--

<a name="fig4"></a>
![07_CombineTechnologies][07_CombineTechnologies] 
 
**Figure: A single application can use multiple hosting options.**

As the figure illustrates, the Cloud Services VMs run in a separate cloud service from the Virtual Machines VMs. Still, the two can communicate quite efficiently, so building an app this way is sometimes the best choice.
[07_CombineTechnologies]: ./media/fundamentals-application-models/ExecModels_07_CombineTechnologies.png
!-->

[App Service]: #tellmeas
[Virtual Machines]: #tellmevm
[Cloud Services]: #tellmecs

## Étapes suivantes

* [Comparaison](../choose-web-site-cloud-service-vm/) entre App Service, Azure Cloud Services et Virtual Machines
* En savoir plus sur [App Service](../app-service-web-overview.md)
* En savoir plus sur [Cloud Service](services/cloud-services/)
* En savoir plus sur [Virtual Machines](https://msdn.microsoft.com/library/azure/jj156143.aspx) 

<!---HONumber=August15_HO6-->