---
title: "Planification de la capacité du cluster Service Fabric | Microsoft Docs"
description: "Considérations en matière de planification de la capacité du cluster Service Fabric. Types de nœuds, durabilité et niveaux de fiabilité"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/05/2017
ms.author: chackdan
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: c38b337d67b518f68be6dc8255fa97b78ba89dae
ms.contentlocale: fr-fr
ms.lasthandoff: 05/08/2017


---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Considérations en matière de planification de la capacité du cluster Service Fabric
Pour un déploiement de production, la planification de la capacité est une étape importante. Voici certains éléments que vous devez prendre en compte dans ce processus.

* Nombre de types de nœuds avec lesquels votre cluster doit démarrer
* Propriétés de chaque type de nœud (taille, principal ou non, accessibilité sur Internet, nombre de machines virtuelles, etc.)
* Caractéristiques de fiabilité et de durabilité du cluster

Nous allons présenter brièvement chacun de ces éléments.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Nombre de types de nœuds avec lesquels votre cluster doit démarrer
Tout d’abord, vous devez savoir à quoi va servir le cluster que vous créez et quels sont les types d’applications que vous voulez déployer dans ce cluster. Si vous n’êtes pas sûr de ce à quoi va servir le cluster, vous n’êtes probablement pas encore prêt pour le processus de planification de capacité.

Établissez le nombre de types de nœuds avec lesquels votre cluster doit démarrer.  Chaque type de nœud est mappé à un groupe de machines virtuelles identiques. Chaque type de nœud peut ensuite faire l’objet d’une montée ou descente en puissance de manière indépendante, avoir différents jeux de ports ouverts et présenter différentes métriques de capacité. Par conséquent, pour savoir combien de types de nœud vous avez besoin, vous devez vous poser les questions suivantes :

* Votre application inclut-elle plusieurs services ? Si oui, ces services doivent-ils être publics ou accessibles sur Internet ? Les applications standard contiennent un service de passerelle frontale qui reçoit des entrées d’un client, et un ou plusieurs services principaux qui communiquent avec les services frontaux. Par conséquent, dans ce cas, vous avez besoin d’au moins deux types de nœuds.
* Vos services (qui composent votre application) ont-ils des besoins d’infrastructure différents tels qu’une RAM plus volumineuse ou des cycles processeur plus élevés ? Supposons, par exemple, que l’application que vous voulez déployer contient un service frontal et un service principal. Le service frontal peut s’exécuter sur des machines virtuelles plus petites (par exemple, de taille D2) ayant des ports ouverts sur Internet.  Toutefois, le service principal est gourmand en calculs et doit s’exécuter sur des machines virtuelles plus grandes (par exemple, de taille D4, D6, D15) qui ne sont pas connectées à Internet.
  
  Dans cet exemple, même si vous pouvez décider de placer tous les services sur un seul type de nœud, nous vous recommandons de les placer dans un cluster avec deux types de nœuds.  Ainsi, chaque type de nœud a des propriétés distinctes telles que la connectivité Internet ou la taille de machine virtuelle. Le nombre de machines virtuelles peut être mis à l’échelle indépendamment, également.  
* Étant donné que vous ne pouvez pas prédire l’avenir, prenez les informations que vous connaissez et choisissez le nombre de types de nœuds dont vos applications ont besoin pour démarrer. Vous pourrez toujours ajouter ou supprimer des types de nœuds ultérieurement. Un cluster Service Fabric doit comprendre au moins un type de nœud.

## <a name="the-properties-of-each-node-type"></a>Propriétés de chaque type de nœud
Le **type de nœud** peut être considéré comme l’équivalent des rôles dans Cloud Services. Les types de nœuds définissent les tailles de machine virtuelle, le nombre de machines virtuelles et leurs propriétés. Chaque type de nœud qui est défini dans un cluster Service Fabric est configuré en tant que groupe de machines virtuelles identiques distinct. Les groupes de machines virtuelles identiques sont des ressources de calcul Azure que vous pouvez utiliser pour déployer et gérer une collection de machines virtuelles en tant que groupe. Chaque type de nœud est défini comme un groupe de machines virtuelles identiques distinct et peut faire l’objet d’une montée ou descente en puissance de manière indépendante, avoir différents jeux de ports ouverts et présenter différentes métriques de capacité.

Lisez [ce document](service-fabric-cluster-nodetypes.md) pour plus d’informations sur la relation entre les types de nœuds et les groupes de machines virtuelles identiques, et pour découvrir comment ouvrir une session RDP sur l’une des instances, ouvrir de nouveaux ports, etc.

Votre cluster peut avoir plusieurs types de nœuds, mais le type de nœud principal (le premier que vous définissez dans le portail) doit avoir au moins cinq machines virtuelles pour les clusters utilisés pour les charges de travail de production (ou au moins trois machines virtuelles pour les clusters de test). Si vous créez le cluster à l’aide d’un modèle Resource Manager, un attribut **is Primary** se trouve sous la définition du type de nœud. Le type de nœud principal est le type de nœud où sont placés les services système de Service Fabric.  

### <a name="primary-node-type"></a>Type de nœud principal
Pour un cluster avec plusieurs types de nœuds, vous devez définir l’un d’eux comme nœud principal. Voici les caractéristiques d’un type de nœud principal :

* La **taille minimale des machines virtuelles** pour le type de nœud principal est déterminée par le **niveau de durabilité** que vous choisissez. La valeur par défaut du niveau de durabilité est Bronze. Faites défiler vers le bas pour plus d’informations sur le niveau de durabilité et sur les valeurs qu’il peut prendre.  
* Le **nombre minimal de machines virtuelles** pour le type de nœud principal est déterminé par le **niveau de fiabilité** que vous choisissez. La valeur par défaut du niveau de fiabilité est Silver. Faites défiler vers le bas pour plus d’informations sur le niveau de fiabilité et sur les valeurs qu’il peut prendre. 

 

* Les services système de Service Fabric (par exemple, le service Gestionnaire de cluster ou le service Magasin d’images) sont placés sur le type de nœud principal et, par conséquent, la fiabilité et la durabilité du cluster sont déterminées par les valeurs de niveau correspondantes sélectionnées pour le type de nœud principal.

![Capture d’écran d’un cluster qui a deux types de nœuds ][SystemServices]

### <a name="non-primary-node-type"></a>Type de nœud non principal
Un cluster avec plusieurs types de nœud comprend un type de nœud principal et les autres types de nœud. Voici les caractéristiques d’un type de nœud non principal :

* La taille minimale des machines virtuelles pour ce type de nœud est déterminée par le niveau de durabilité que vous choisissez. La valeur par défaut du niveau de durabilité est Bronze. Faites défiler vers le bas pour plus d’informations sur le niveau de durabilité et sur les valeurs qu’il peut prendre.  
* Le nombre minimal de machines virtuelles pour ce type de nœud peut être un seul. Toutefois, vous devez choisir ce nombre en fonction du nombre de réplicas des applications/services que vous voulez exécuter dans ce type de nœud. Le nombre de machines virtuelles dans un type de nœud peut être augmenté une fois le cluster déployé.

## <a name="the-durability-characteristics-of-the-cluster"></a>Caractéristiques de durabilité du cluster
Le niveau de durabilité est utilisé pour indiquer au système les privilèges dont disposent vos machines virtuelles avec l’infrastructure Azure sous-jacente. Dans le type de nœud principal, ce privilège permet à Service Fabric de suspendre toute demande de l’infrastructure au niveau de la machine virtuelle (par exemple, un redémarrage de la machine virtuelle, une réinitialisation de la machine virtuelle ou une migration de machine virtuelle) qui influe sur la configuration requise du quorum pour les services système et vos services avec état. Dans les types de nœuds non principaux, ce privilège permet à Service Fabric de suspendre toute demande de l’infrastructure au niveau de la machine virtuelle (par exemple, le redémarrage de la machine virtuelle, la réinitialisation de la machine virtuelle, la migration de machine virtuelle, etc.) qui influe sur la configuration requise du quorum pour vos services avec état qui s’y exécutent.

Ce privilège est exprimé dans les valeurs suivantes :

* Gold : les travaux de l’infrastructure peuvent être suspendus pour une durée de 2 heures par jour. La durabilité Gold ne peut être activée que sur les références de machine virtuelle à nœud complet comme D15_V2, G5, etc.
* Silver : Les travaux de l’infrastructure peuvent être suspendus pour une durée de 30 minutes par jour (cette valeur n’est pas actuellement disponible pour utilisation. Une fois activée, elle sera disponible sur toutes les machines virtuelles standard avec un cœur minimum).
* Bronze : Aucun privilège. Il s’agit de la valeur par défaut.

## <a name="the-reliability-characteristics-of-the-cluster"></a>Caractéristiques de fiabilité du cluster
Le niveau de fiabilité est utilisé pour définir le nombre de réplicas des services système que vous voulez exécuter dans ce cluster sur le type de nœud principal. Plus le nombre de réplicas est important, plus les services système sont fiables dans votre cluster.  

Le niveau de fiabilité peut avoir les valeurs suivantes :

* Platinum : Exécutez les services système avec un nombre de jeux de réplicas cible égal à 9
* Gold : Exécutez les services système avec un nombre de jeux de réplicas cible égal à 7
* Silver : Exécutez les services système avec un nombre de jeux de réplicas cible égal à 5
* Bronze : Exécutez les services système avec un nombre de jeux de réplicas cible égal à 3

> [!NOTE]
> Le niveau de fiabilité que vous choisissez détermine le nombre minimal de nœuds que doit contenir votre type de nœud principal. Le niveau de fiabilité n’a aucune incidence sur la taille maximale du cluster. Vous pouvez donc disposer d’un cluster de 20 nœuds s’exécutant au niveau de fiabilité Bronze.
> 
> 

 Vous pouvez choisir de mettre à jour la fiabilité de votre cluster d’un niveau à l’autre. Cette opération déclenche les mises à niveau de cluster nécessaires pour modifier le nombre de jeux de réplicas des services système. Attendez que la mise à niveau en cours se termine avant d’apporter d’autres modifications au cluster, comme l’ajout de nœuds.  Vous pouvez surveiller la progression de la mise à niveau avec Service Fabric Explorer ou en exécutant [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)


## <a name="primary-node-type---capacity-guidance"></a>Type de nœud principal - Recommandations en matière de capacité

Voici nos recommandations pour planifier la capacité du type de nœud principal :

1. **Nombre d’instances de machine virtuelle pour exécuter une charge de travail de production dans Azure : ** vous devez définir la taille minimale du type de nœud principal sur 5.
2. **Nombre d’instances de machine virtuelle pour exécuter des charges de travail de production dans Azure** Vous devez définir la taille minimale du type de nœud principal sur 1 ou 3. Le cluster à un nœud s’exécute avec une configuration spéciale. Par conséquent, l’augmentation de la taille des instances de ce cluster n’est pas prise en charge. Le cluster à un nœud n’est pas fiable. Par conséquent, dans votre modèle Resource Manager, vous devez supprimer / ne pas spécifier cette configuration (ne pas définir la valeur de configuration ne suffit pas). Si vous configurez le cluster à un nœud via le portail, la configuration est automatiquement prise en charge. Les clusters à 1 et 3 nœuds ne sont pas pris en charge pour l’exécution des charges de travail de production. 
3. **Référence de machine virtuelle :** comme les services système s’exécutent sur le type de nœud principal, votre choix de référence de machine virtuelle pour celui-ci doit prendre en compte la charge maximale globale que vous prévoyez de placer dans le cluster. Voici une analogie pour illustrer mon propos ici : considérez le type de nœud principal comme vos « poumons », qui alimentent votre cerveau en oxygène ; si le cerveau ne reçoit pas assez d’oxygène, votre corps souffre. 

Les besoins en capacité d’un cluster sont déterminés par la charge de travail que vous prévoyez d’exécuter dans le cluster. Par conséquent, nous ne pouvons pas vous fournir de recommandations qualitatives pour votre charge de travail spécifique. Cependant, vous trouverez ci-dessous des recommandations générales qui vous aideront à bien démarrer.

Pour les charges de travail de production 


- La référence de machine virtuelle recommandée est Standard D3 ou Standard D3_V2 ou équivalent avec un minimum de 14 Go de mémoire sur disque SSD local.
- La référence de machine virtuelle minimale prise en charge est Standard D1 ou Standard D1_V2 ou équivalent avec un minimum de 14 Go de mémoire sur disque SSD local. 
- Les références de machine virtuelle à cœur partiel telles que Standard A0 ne sont pas prises en charge pour les charges de travail de production.
- La référence Standard A1 n’est pas prise en charge pour les charges de production pour des raisons de performances.


## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Type de nœud non principal - Recommandations en matière de capacité pour les charges de travail avec état

Lisez les informations suivantes pour les charges de travail qui utilisent les collections fiables ou les Reliable Actors Service Fabric. Pour en savoir plus sur les modèles de programmation, consultez [cet article](service-fabric-choose-framework.md).

1. **Nombre d’instances de machine virtuelle :** pour les charges de travail de production avec état, il est recommandé de les exécuter avec un nombre de réplicas minimal et cible de 5. Cela signifie qu’à l’état stationnaire, vous vous retrouvez avec un réplica (issu d’un jeu de réplicas) dans chaque domaine d’erreur et de mise à niveau. Le concept même de niveau de fiabilité pour le type de nœud principal permet de spécifier ce paramètre pour les services système.

Pour les charges de travail de production, la taille minimale recommandée pour le type de nœud non principal est donc de 5 si vous exécutez des charges de travail avec état sur ce dernier.

2. **Référence de machine virtuelle :** comme il s’agit du type de nœud sur lequel vos services d’application s’exécutent, votre choix de référence de machine virtuelle pour celui-ci doit prendre en compte la charge maximale que vous prévoyez de placer dans chaque nœud. Les besoins en capacité du type de nœud sont déterminés par la charge de travail que vous prévoyez d’exécuter dans le cluster. Par conséquent, nous ne pouvons pas vous fournir de recommandations qualitatives pour votre charge de travail spécifique. Cependant, vous trouverez ci-dessous des recommandations générales qui vous aideront à bien démarrer.

Pour les charges de travail de production 

- La référence de machine virtuelle recommandée est Standard D3 ou Standard D3_V2 ou équivalent avec un minimum de 14 Go de mémoire sur disque SSD local.
- La référence de machine virtuelle minimale prise en charge est Standard D1 ou Standard D1_V2 ou équivalent avec un minimum de 14 Go de mémoire sur disque SSD local. 
- Les références de machine virtuelle à cœur partiel telles que Standard A0 ne sont pas prises en charge pour les charges de travail de production.
- La référence Standard A1 n’est pas non plus prise en charge pour les charges de production pour des raisons de performances.


## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Type de nœud non principal - Recommandations en matière de capacité pour les charges de travail sans état

Lisez les informations suivantes pour les charges de travail sans état.

**Nombre d’instances de machines virtuelles :** pour les charges de travail de production sans état, la taille minimale prise en charge pour le type de nœud non principal est égale à 2. Vous pouvez ainsi exécuter deux instances sans état de votre application, ce qui permet à votre service de continuer à fonctionner en cas de perte d’une instance de machine virtuelle. 

> [!NOTE]
> Si votre cluster est exécuté sur une version de Service Fabric antérieure à la version 5.6, en raison d’un défaut dans le runtime (dont la résolution est prévue dans la version 5.6), la diminution de la taille du type de nœud non principal jusqu’à une valeur inférieure à 5 donne lieu à une défaillance sur le plan de l’intégrité du cluster jusqu’à ce que vous appeliez [ServiceFabricNodeState-Remove cmd](https://docs.microsoft.com/powershell/servicefabric/vlatest/Remove-ServiceFabricNodeState) avec le nom de nœud approprié. Pour plus d’informations, consultez [Augmenter ou diminuer la taille des instances d’un cluster Service Fabric](service-fabric-cluster-scale-up-down.md).
> 
>

**Référence de machine virtuelle :** comme il s’agit du type de nœud sur lequel vos services d’application s’exécutent, votre choix de référence de machine virtuelle pour celui-ci doit prendre en compte la charge maximale que vous prévoyez de placer dans chaque nœud. Les besoins en capacité du type de nœud sont déterminés entièrement par la charge de travail que vous prévoyez d’exécuter dans le cluster. Par conséquent, nous ne pouvons pas vous fournir de recommandations qualitatives pour votre charge de travail spécifique. Cependant, vous trouverez ci-dessous des recommandations générales qui vous aideront à bien démarrer.

Pour les charges de travail de production 


- La référence de machine virtuelle recommandée est Standard D3 ou Standard D3_V2 ou équivalent. 
- La référence de machine virtuelle minimale prise en charge est Standard D1 ou Standard D1_V2 ou équivalent. 
- Les références de machine virtuelle à cœur partiel telles que Standard A0 ne sont pas prises en charge pour les charges de travail de production.
- La référence Standard A1 n’est pas non plus prise en charge pour les charges de production pour des raisons de performances.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez terminé la planification de votre capacité et que vous avez configuré un cluster, lisez ce qui suit :

* [Sécurité d’un cluster Service Fabric](service-fabric-cluster-security.md)
* [Présentation du modèle d’intégrité de Service Fabric](service-fabric-health-introduction.md)
* [Relations des types de nœuds avec le groupe de machines virtuelles identiques](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png

