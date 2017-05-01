---
title: "Considérations sur l’utilisation d’images de machine virtuelle Oracle | Microsoft Docs"
description: "En savoir plus sur les configurations prises en charge et les limitations pour une machine virtuelle Oracle sur Windows Server dans Azure avant de déployer."
services: virtual-machines-windows
documentationcenter: 
manager: timlt
author: rickstercdn
tags: azure-service-management
ms.assetid: 5d71886b-463a-43ae-b61f-35c6fc9bae25
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 1a808a83a8ed1162d57f7c5a49e34b2e3be50833
ms.lasthandoff: 04/12/2017


---
# <a name="miscellaneous-considerations-for-oracle-virtual-machine-images"></a>Remarques diverses concernant les images de machines virtuelles Oracle
Cet article aborde différentes considérations relatives aux machines virtuelles Oracle dans Azure, qui reposent sur des images logicielles Oracle fournies par Oracle.  

* Images de machines virtuelles Oracle Database
* Images de machines virtuelles Oracle WebLogic Server
* Images de machine virtuelle JDK Oracle

## <a name="oracle-database-virtual-machine-images"></a>Images de machines virtuelles Oracle Database

### <a name="no-static-internal-ip"></a>Aucune adresse IP interne statique
Microsoft Azure attribue une adresse IP interne à chaque machine virtuelle. Sauf si elle fait partie d’un réseau virtuel, l’adresse IP de la machine virtuelle est de type dynamique et peut changer lorsqu’elle redémarre. Cela peut entraîner des problèmes, car la base de données Oracle s’attend à une adresse IP statique. Pour éviter ce problème, pensez à ajouter la machine virtuelle à Microsoft Azure Virtual Network. Pour plus d’informations, voir les sections relatives aux [réseaux virtuels](https://azure.microsoft.com/documentation/services/virtual-network/) et à la [création d’un réseau virtuel cloud uniquement dans Azure](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

### <a name="attached-disk-configuration-options"></a>Options de configuration des disques connectés

Les disques attachés s’appuient sur le service de stockage d’objets blob Microsoft Azure. En théorie, chaque disque standard peut proposer un maximum de 500 entrées/sorties par seconde (IOPS). Notre offre de disque Premium est conseillée pour les charges de travail de base de données hautes performances et peut atteindre jusqu’à 5 000 IOPS par disque. Même si vous pouvez utiliser un seul disque si cela correspond à vos besoins en termes de performances, vous pouvez souvent améliorer les performances d’IOPS effectives si vous utilisez plusieurs disques attachés, répartissez les données de la base de données sur ces disques, puis lancez ASM (Oracle Automatic Storage Management). Pour plus d’informations, voir [Oracle Automatic Storage Overview](http://www.oracle.com/technetwork/database/index-100339.html). Bien qu’il soit possible d’utiliser la distribution sur plusieurs disques au niveau du système d’exploitation, des compromis sont nécessaires pour chacun de ces choix. 

Tenez compte des deux approches permettant d’attacher plusieurs disques, selon que vous souhaitez prioriser les performances des opérations de lecture ou d’écriture dans votre base de données :

* **Utilisé seul, le logiciel Oracle ASM** est susceptible de fournir des performances optimisées pour les opérations d’écriture, mais moins d’IOPS pour les opérations de lecture par rapport à l’approche utilisant des baies de disques. L’illustration suivante décrit cette organisation, de manière logique.  
    ![](media/mysql-2008r2/image2.png)

> [!IMPORTANT]
> Établissez un compromis entre les performances en écriture et les performances en lecture en procédant au cas par cas. Les résultats réels peuvent varier. Vous devez réaliser vos propres tests. ASM privilégie les opérations d’écriture, la distribution par le système d’exploitation privilégie les opérations de lecture.
> 

### <a name="clustering-rac-is-not-supported"></a>La gestion de clusters (RAC) n’est pas prise en charge.
RAC (Oracle Real Application Clusters) est conçu pour atténuer les conséquences de la défaillance d’un nœud dans une configuration locale de cluster à plusieurs nœuds.  Il s’appuie sur deux technologies locales qui ne fonctionnent pas dans les environnements de cloud public hyperscale comme Microsoft Azure : réseau multidiffusion et disque partagé. Si vous souhaitez créer une configuration à plusieurs nœuds géo-redondants pour une base de données Oracle, vous devez implémenter la réplication des données avec Oracle DataGuard.

### <a name="high-availability-and-disaster-recovery-considerations"></a>Remarques relatives à la haute disponibilité et à la récupération d’urgence
Lors de l’utilisation de Oracle Database dans les machines virtuelles Azure, vous êtes responsable de l’implémentation d’une solution de récupération d’urgence et de haute disponibilité pour éviter tout temps d’arrêt. Vous êtes également chargé de sauvegarder votre application et vos données.

Pour assurer la haute disponibilité et la récupération d’urgence pour Oracle Database Enterprise Edition (sans RAC) sur Microsoft Azure, vous pouvez utiliser [Data Guard, Active Data Guard](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html) ou [Oracle Golden Gate](http://www.oracle.com/technetwork/middleware/goldengate), en plaçant deux bases de données sur deux machines virtuelles distinctes. Ces deux machines doivent se trouver dans le même [réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/), afin de pouvoir être accessibles l’une à l’autre via l’adresse IP privée persistante.  En outre, nous vous recommandons de placer les machines virtuelles au sein du même [groupe à haute disponibilité](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), afin de permettre à Azure de les placer dans des domaines d’erreur et de mise à niveau distincts. Chaque machine virtuelle doit disposer d’au moins 2 Go de mémoire et de 5 Go d’espace disque.

Grâce à Oracle Data Guard, vous pouvez assurer la haute disponibilité du système en plaçant la base de données primaire sur une machine virtuelle et une base de données secondaire (de veille) sur une autre, et en configurant une réplication monodirectionnelle entre ces bases de données. Vous bénéficiez ainsi d’un accès en lecture à la copie de la base de données. Oracle GoldenGate vous permet de configurer une réplication bidirectionnelle entre les deux bases de données. Pour savoir comment configurer une solution de haute disponibilité pour vos bases de données en utilisant ces outils, voir la documentation relative à [Active Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) et [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) sur le site web d’Oracle. Si vous avez besoin d’un accès en lecture et en écriture à la copie de la base de données, vous pouvez utiliser [Oracle Active Data Guard](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Images de machines virtuelles Oracle WebLogic Server
* **Le clustering est pris en charge sur la version Enterprise Edition uniquement.** Vous disposez d’une licence pour utiliser le clustering WebLogic uniquement lorsque vous utilisez l’édition Enterprise de WebLogic Server. N’utilisez pas le clustering avec WebLogic Server Standard Edition.
* **Délai d’attente de la connexion :** si votre application s’appuie sur des connexions aux points de terminaison publics d’un autre service cloud Azure (par exemple, un service de niveau base de données), Azure peut fermer ces connexions ouvertes après quatre minutes d’inactivité. Cette opération peut affecter les fonctions et applications basées sur des pools de connexions, car les connexions inactives pendant plus de 4 minutes peuvent ne plus être valides. Si votre application est affectée, envisagez d’activer la logique de connexion persistante sur vos pools de connexions.
  
   Si un point de terminaison est *interne* au déploiement du service cloud Azure (comme une machine virtuelle de base de données autonome au sein du *même* service cloud que vos machines virtuelles WebLogic), la connexion est directe et ne repose pas sur l’équilibreur de charge Azure ; de ce fait, elle n’est pas soumise à un délai d’attente de la connexion.
* **La multidiffusion UDP n’est pas gérée.** Azure prend en charge la monodiffusion UDP, mais ni la diffusion, ni la multidiffusion. WebLogic Server est capable de s’appuyer sur les fonctionnalités de monodiffusion UDP d’Azure. Pour optimiser les résultats lorsque vous recourez à la monodiffusion UDP, nous vous recommandons de conserver une taille de cluster statique, ou de maintenir ce dernier à une taille maximale de 10 serveurs gérés.
* **WebLogic Server attend des ports publics et privés identiques pour l’accès T3 (par exemple, lorsque vous utilisez Enterprise JavaBeans).** Imaginez un scénario à plusieurs niveaux, dans lequel une application de couche de service (EJB) est en cours d’exécution sur un cluster WebLogic Server comprenant deux serveurs gérés ou plus, au sein d’un service cloud nommé **SLWLS**. Le niveau client se trouve dans un autre service cloud, qui exécute un programme Java simple s’efforçant d’appeler Enterprise JavaBeans dans la couche de service. Comme il est nécessaire d’équilibrer la couche de service, un point de terminaison public avec équilibrage de charge doit être créé pour les machines virtuelles du cluster WebLogic Server. Si le port privé que vous spécifiez pour ce point de terminaison est différent du port public (par exemple, 7006:7008), une erreur semblable à ce qui suit se produit :
  
       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:
  
       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]
  
   En effet, WebLogic Server attend un port d’équilibreur de charge et un port de serveur géré WebLogic identiques pour tout accès T3 distant. Dans le cas ci-dessus, le client accède au port 7006 (port d’équilibreur de charge) et le serveur géré est à l’écoute sur le port 7008 (port privé). Cette restriction porte uniquement sur l’accès T3, et non sur le protocole HTTP.
  
   Pour éviter ce problème, utilisez l’une des solutions de contournement suivantes :
  
  * Utilisez les mêmes numéros de port public et privé pour les points de terminaison avec équilibrage de charge dédiés à l’accès T3.
  * Incluez le paramètre JVM suivant lors du démarrage de WebLogic Server :
    
         -Dweblogic.rjvm.enableprotocolswitch=true

Pour plus d’informations, voir l’article **860340.1** à l'adresse <http://support.oracle.com>.

* **Limitations relatives à l’équilibrage de charge et au clustering dynamique.** Partons du principe que vous souhaitez utiliser un cluster dynamique dans WebLogic Server et l’exposer en tant que point unique de terminaison public avec équilibrage de charge. Cette opération est possible tant que vous utilisez un numéro de port fixe pour chaque serveur géré (et non affecté de manière dynamique à partir d’une plage de valeurs), et que le nombre de serveurs gérés démarrés reste inférieur à celui des machines surveillées par l’administrateur (en d’autres termes, il faut un seul serveur géré par machine virtuelle). Si, suite à votre configuration, le nombre de serveurs WebLogic démarrés est supérieur à celui des machines virtuelles (c’est-à-dire si plusieurs instances WebLogic Server partagent une même machine virtuelle), une seule instance WebLogic Server peut être liée à un numéro de port donné à la fois ; les autres instances de la machine virtuelle échouent.
  
   D’un autre côté, si vous configurez le serveur d’administration de façon à affecter automatiquement des numéros de port uniques à ses serveurs gérés, aucun équilibrage de charge ne sera possible, car Microsoft Azure ne prend pas en charge le mappage d’un port public unique à plusieurs ports privés (opération alors requise pour cette configuration).
* **Exécution de plusieurs instances WebLogic sur une seule machine virtuelle.** Selon les exigences liées au déploiement, peut-être envisagerez-vous d’exécuter plusieurs instances WebLogic Server sur la même machine virtuelle, si la taille de cette dernière est suffisante. Ainsi, sur une machine virtuelle de taille moyenne contenant deux cœurs, vous pouvez opter pour l’exécution de deux instances WebLogic Server. Toutefois, nous vous recommandons malgré tout d’éviter toute introduction de points de défaillance uniques dans votre architecture (situation susceptible de survenir si vous utilisez une seule machine virtuelle exécutant plusieurs instances WebLogic Server). L’utilisation d’un minimum de deux machines virtuelles peut s’avérer plus efficace ; chaque machine virtuelle peut alors exécuter plusieurs instances WebLogic Server. Chacune de ces instances peut continuer de faire partie du même cluster. Toutefois, il n’est actuellement pas possible d’utiliser Microsoft Azure pour équilibrer la charge des points de terminaison exposés par des déploiements WebLogic Server de ce type au sein d’une même machine virtuelle. En effet, l’équilibreur de charge Microsoft Azure requiert la répartition des serveurs avec équilibrage de charge sur différentes machines virtuelles uniques.

## <a name="oracle-jdk-virtual-machine-images"></a>Images de machine virtuelle JDK Oracle
* **Dernières mises à jour de JDK 6 et 7.** Nous recommandons d’utiliser la dernière version publique de Java prise en charge (il s’agit actuellement de la version 8). Cependant, Azure propose également des images de JDK 6 et 7. Ces images sont destinées aux applications héritées qui ne peuvent pas encore être mises à niveau vers la version 8 de JDK. Il se peut que les mises à jour des images JDK précédentes ne soient plus mises à la disposition du public. Toutefois, suite au partenariat entre Microsoft et Oracle, les images de JDK 6 et 7 proposées par Microsoft Azure ont pour objectif de contenir une mise à jour plus récente, non destinée au public, qui est généralement proposée par Oracle à certains clients spécifiques bénéficiant d’un support Oracle. De nouvelles versions des images de JDK seront mises à disposition au fur et à mesure, en parallèle avec les versions de JDK 6 et 7 mises à jour.
  
   Le JDK disponible dans ces images de JDK 6 et 7, ainsi que les machines virtuelles et les images dérivées, peuvent uniquement être utilisées au sein d’Azure.
* **JDK 64 bits.** Les images de machine virtuelle Oracle WebLogic Server et les images de machine virtuelle Oracle JDK fournies par Microsoft Azure contiennent les versions 64 bits de Windows Server et de JDK.

## <a name="additional-resources"></a>Ressources supplémentaires
[Liste des images de machines virtuelles Oracle pour Azure](../../linux/classic/oracle-images.md)


