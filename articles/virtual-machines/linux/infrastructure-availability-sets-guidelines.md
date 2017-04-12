---
title: "Groupes à haute disponibilité pour les machines virtuelles Linux dans Azure | Microsoft Docs"
description: "Découvrez-en plus sur les principales instructions de conception et d’implémentation pour le déploiement de groupes à haute disponibilité dans des services d’infrastructure Azure."
documentationcenter: 
services: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 24f1d91c-8cc0-4251-bb67-ac4c4c37e8cd
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 9fe18ba70c98baacae99e4f26506510921dcf894
ms.lasthandoff: 04/03/2017


---
# <a name="azure-availability-sets-guidelines-for-linux-vms"></a>Instructions pour les groupes à haute disponibilité Azure pour les machines virtuelles Linux

[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Cet article se concentre sur la compréhension des étapes de planification requises pour les groupes à haute disponibilité afin d’assurer que vos applications restent accessibles aux cours des événements planifiés et non planifiés.

## <a name="implementation-guidelines-for-availability-sets"></a>Instructions d’implémentation pour groupes à haute disponibilité
Décisions :

* De combien de groupes à haute disponibilité avez-vous besoin pour les différents rôles et niveaux de votre infrastructure d’application ?

Tâches :

* Définissez le nombre de machines virtuelles requises pour chaque niveau d’application.
* Déterminez si vous devez ajuster le nombre de domaines d’erreur ou de mise à jour à utiliser pour votre application.
* Définissez les groupes à haute disponibilité requis à l’aide de votre convention de dénomination et des machines virtuelles qui s’y trouvent. Une machine virtuelle ne peut résider que dans un seul groupe à haute disponibilité. 

## <a name="availability-sets"></a>Groupes à haute disponibilité
Dans Azure, les machines virtuelles peuvent être placées dans un groupement logique appelé groupe à haute disponibilité. Lorsque vous créez des machines virtuelles au sein d’un groupe à haute disponibilité, la plateforme Azure répartit le placement de ces machines virtuelles sur l’infrastructure sous-jacente. En cas d’événement de maintenance planifiée pour la plateforme Azure ou de panne de l’infrastructure ou du matériel sous-jacent, l’utilisation des groupes à haute disponibilité assure qu’au moins une machine virtuelle est en cours d’exécution.

En tant que meilleure pratique, les applications ne doivent pas résider sur une seule machine virtuelle. Un groupe à haute disponibilité qui contient une seule machine virtuelle ne gagne aucune protection contre les événements planifiés ou non planifiés dans la plateforme Azure. Le [Contrat de niveau de service Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines) nécessite deux machines virtuelles ou plus au sein d’un groupe à haute disponibilité défini afin de permettre la distribution des machines virtuelles sur l’infrastructure sous-jacente. Si vous utilisez [Stockage Premium Azure](../../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), le contrat SLA Azure s’applique à une seule machine virtuelle.

L’infrastructure sous-jacente dans Azure est divisée en plusieurs clusters de matériel. Chaque cluster de matériel peut prendre en charge une plage de tailles de machine virtuelle. Un groupe à haute disponibilité ne peut être hébergé que sur un seul cluster de matériel à la fois. Par conséquent, les tailles des machines virtuelles qui peuvent exister dans un groupe à haute disponibilité sont limitées aux tailles des machines virtuelles prises en charge par le cluster de matériel. Le cluster de matériel pour le groupe à haute disponibilité est sélectionné lorsque la première machine virtuelle du groupe à haute disponibilité est déployée ou lors du démarrage de la première machine virtuelle dans un groupe à haute disponibilité où toutes les machines virtuelles se trouvent actuellement en état arrêté-libéré. La commande suivante de l’interface de ligne de commande peut être utilisée pour déterminer les tailles des machines virtuelles disponibles pour un groupe à haute disponibilité : « az vm list-sizes --location \<chaîne\> »

Chaque cluster matériel est divisé en plusieurs domaines de mise à jour et d’erreur. Ces domaines sont définis par les hôtes qui partagent un cycle de mise à jour commun, ou une infrastructure physique similaire, comme l’alimentation ou la mise en réseau. Azure distribue automatiquement vos machines virtuelles au sein d’un groupe à haute disponibilité sur plusieurs domaines pour assurer la disponibilité et la tolérance aux pannes. Selon la taille de votre application et le nombre de machines virtuelles au sein d’un groupe à haute disponibilité, vous pouvez régler le nombre de domaines que vous souhaitez utiliser. Vous pouvez en savoir plus sur [la gestion de la disponibilité et l’utilisation des domaines d’erreur et de mise à jour](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Lorsque vous concevez l’infrastructure de votre application, vous devez également planifier les couches Application à utiliser. Groupez les machines virtuelles qui ont la même fonction dans des groupes à haute disponibilité définis, comme un groupe à haute disponibilité pour vos machines virtuelles frontales exécutant nginx ou Apache. Créez un groupe à haute disponibilité distinct pour les machines virtuelles principales tournant sous MongoDB ou MySQL. L’objectif est de vous assurer que chaque composant de votre application est protégé par un groupe à haute disponibilité et qu’au moins une instance est toujours exécutée.

Les équilibreurs de charge peuvent être utilisés devant chaque couche d’application pour fonctionner avec un groupe à haute disponibilité et assurer que le trafic peut être acheminé vers une instance en cours d’exécution. Sans équilibrage de charge, vos machines virtuelles peuvent continuer à s’exécuter dans l’ensemble des événements de maintenance planifiée et non planifiée, mais il se peut alors que vos utilisateurs finaux ne puissent pas les résoudre si la machine virtuelle principale n’est pas disponible à ce moment précis.

Lorsque vous utilisez des disques non managés, concevez votre application à des fins de haute disponibilité au niveau de la couche de stockage. Il est recommandé d’utiliser un compte de stockage séparé pour chaque machine virtuelle dans un groupe à haute disponibilité. Conservez tous les disques (système d’exploitation et données) associés à une machine virtuelle dans le même compte de stockage. Les [limites](../../storage/storage-scalability-targets.md) du compte de stockage doivent être prises en compte lorsque vous ajoutez plusieurs disques durs virtuels à un compte de stockage. Pour les [disques managés Azure](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), la répartition des disques sous-jacents est effectuée automatiquement.

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]


