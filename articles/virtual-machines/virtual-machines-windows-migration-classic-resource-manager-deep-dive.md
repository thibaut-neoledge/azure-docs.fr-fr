---
title: "Migration entre les modèles de déploiement pour les machines virtuelles Windows dans Azure| Microsoft Docs"
description: "Cet article présente une étude technique approfondie de la migration de ressources prise en charge par la plateforme de l’environnement Classic vers Azure Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 1ee40d32-a5e8-42a2-97d0-3232fd3cbb98
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 1/23/2017
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: d835d5825268a4ec0fa5b761f9b5714e3236b0ce
ms.openlocfilehash: 2dd35a65d1b5b6db1401cdf5737b5355aa6d564b


---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager
Examinons en détail la migration à partir du modèle de déploiement Azure classique vers le modèle de déploiement Azure Resource Manager. Nous examinons les ressources au niveau des fonctionnalités et des ressources pour vous aider à comprendre comment la plateforme Azure migre les ressources entre les deux modèles de déploiement. Pour en savoir plus, consultez l’article d’annonce de service [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md).

## <a name="detailed-guidance-on-migration"></a>Instructions détaillées sur la migration
Les représentations Classic et Resource Manager des ressources sont décrites dans la table suivante. D’autres fonctionnalités et ressources ne sont pas prises en charge actuellement.

| Représentation Classic | Représentation Resource Manager | Remarques détaillées |
| --- | --- | --- |
| Nom du service cloud |Nom DNS |Pendant la migration, un groupe de ressources est créé pour chaque service cloud correspondant au modèle de dénomination `<cloudservicename>-migrated`. Ce groupe de ressources contient toutes vos ressources. Le nom du service cloud devient un nom DNS associé à l’adresse IP publique. |
| Machine virtuelle |Machine virtuelle |Les propriétés spécifiques à la machine virtuelle sont inchangées après la migration. Certaines informations osProfile, notamment le nom de l’ordinateur, ne sont pas stockées dans le modèle de déploiement classique et restent vides après la migration. |
| Ressources de disque attachées à une machine virtuelle |Disques implicites attachés à une machine virtuelle |Les disques ne sont pas modélisés comme ressources de niveau supérieur dans le modèle de déploiement Resource Manager. Ils font l’objet d’une migration sous forme de disques implicites sous la machine virtuelle. Seuls des disques attachés à une machine virtuelle sont actuellement pris en charge. Les machines virtuelles Resource Manager peuvent désormais utiliser des comptes de stockage classiques, ce qui permet une migration facile des disques sans aucune mise à jour. |
| Extensions de machine virtuelle |Extensions de machine virtuelle |Toutes les extensions de ressource font l’objet d’une migration depuis le déploiement Classic, à l’exception des extensions XML. |
| Certificats de machine virtuelle |Certificats dans Azure Key Vault |Si un service cloud contient des certificats de service, la plateforme crée un coffre de clés Azure pour chaque service cloud et y déplace les certificats. Les machines virtuelles sont mises à jour pour référencer les certificats du coffre de clés. <br><br> **REMARQUE :** ne supprimez pas le coffre de clés, car cela peut provoquer l’échec de la machine virtuelle. Nous travaillons à l’amélioration des opérations sur le serveur principal afin que les coffres de clés puissent être supprimés ou déplacés en toute sécurité avec la machine virtuelle vers un nouvel abonnement. |
| Configuration WinRM |Configuration WinRM sous osProfile |La configuration de Windows Remote Management est déplacée sans être modifiée dans le cadre de la migration. |
| Propriété de groupe à haute disponibilité |Ressource de groupe à haute disponibilité |La spécification des groupes à haute disponibilité était une propriété de la machine virtuelle dans le modèle de déploiement Classic. Les groupes à haute disponibilité deviennent une ressource de niveau supérieur dans le cadre de la migration. Les configurations suivantes ne sont pas prises en charge : plusieurs groupes à haute disponibilité par service cloud, ou un ou plusieurs groupes à haute disponibilité en même temps que des machines virtuelles ne figurant dans aucun groupe à haute disponibilité d’un service cloud. |
| Configuration réseau sur une machine virtuelle |Interface réseau principale |La configuration réseau sur une machine virtuelle est représentée sous forme de ressource d’interface réseau principale après la migration. Dans le cas des machines virtuelles qui ne figurent pas dans un réseau virtuel, l’adresse IP interne change lors de la migration. |
| Plusieurs interfaces réseau sur une machine virtuelle |Interfaces réseau |Si plusieurs interfaces réseau sont associées à une machine virtuelle, chaque interface réseau devient une ressource de niveau supérieur dans le cadre de la migration vers le modèle de déploiement Resource Manager, avec toutes les propriétés. |
| Jeux de points de terminaison soumis à l’équilibrage de charge |Équilibrage de charge |Dans le modèle de déploiement Classic, la plateforme a affecté un équilibreur de charge implicite à chaque service cloud. Pendant la migration, une ressource d’équilibreur de charge est créée, et le jeu de points de terminaison d’équilibrage de charge constitue les règles de l’équilibreur de charge. |
| Règles NAT entrantes |Règles NAT entrantes |Les points de terminaison d’entrée définis sur la machine virtuelle sont convertis en règles de traduction NAT (Network Access Translation) du trafic entrant sous l’équilibreur de charge lors de la migration. |
| Adresse IP virtuelle |Adresse IP publique avec nom DNS |L’adresse IP virtuelle devient une adresse IP publique et est associée à l’équilibreur de charge. |
| Réseau virtuel |Réseau virtuel |Le réseau virtuel fait l’objet d’une migration vers le modèle de déploiement Resource Manager avec toutes ses propriétés. Un nouveau groupe de ressources est créé avec le nom `-migrated`. Il existe des [configurations non prises en charge](virtual-machines-windows-migration-classic-resource-manager.md). |
| IP réservées |Adresse IP publique avec méthode d’allocation statique |La migration des IP réservées associées à l’équilibreur de charge est effectuée en même temps que la migration du service cloud ou de la machine virtuelle. La migration d’adresses IP réservées non associées n’est pas prise en charge actuellement. |
| Adresse IP publique par machine virtuelle |Adresse IP publique avec méthode d’allocation dynamique |L’adresse IP publique associée à la machine virtuelle est convertie en ressource d’adresse IP publique avec la méthode d’allocation statique. |
| Groupes de sécurité réseau |Groupes de sécurité réseau |Les groupes de sécurité réseau (NSG) associés à un sous-réseau sont clonés dans le cadre de la migration vers le modèle de déploiement Resource Manager. Le groupe de sécurité réseau (NSG) du modèle de déploiement classique n’est pas supprimé durant la migration. Toutefois, les opérations du plan de gestion relatives au NSG sont bloquées pendant le processus de migration. |
| Serveurs DNS |Serveurs DNS |La migration des serveurs DNS associés à un réseau virtuel ou à la machine virtuelle est effectuée dans le cadre de la migration des ressources correspondantes avec toutes les propriétés. |
| Itinéraires définis par l’utilisateur (UDR) |Itinéraires définis par l’utilisateur (UDR) |Les Itinéraires définis par l’utilisateur associés à un sous-réseau sont clonés dans le cadre de la migration vers le modèle de déploiement Resource Manager. L’itinéraire défini par l’utilisateur dans le modèle de déploiement classique n’est pas supprimé durant la migration. Les opérations du plan de gestion relatives pour l’itinéraire défini par l’utilisateur sont bloquées pendant le processus de migration. |
| Propriété de transfert IP sur la configuration réseau d’une machine virtuelle |Propriété de transfert IP sur la carte d’interface réseau (NIC) |La propriété de transfert IP sur une machine virtuelle est convertie en propriété sur l’interface réseau pendant la migration. |
| Équilibreur de charge avec plusieurs adresses IP |Équilibreur de charge avec plusieurs ressources d’adresses IP publiques |Chaque adresse IP publique associée à l’équilibreur de charge est convertie en ressource d’adresse IP publique et associée à l’équilibreur de charge après la migration. |
| Noms DNS internes sur la machine virtuelle |Noms DNS internes sur la carte réseau |Pendant la migration, les suffixes DNS internes pour les machines virtuelles sont migrés vers une propriété en lecture seule nommée « InternalDomainNameSuffix » sur la carte réseau. Le suffixe reste inchangé après la migration, et la résolution des machines virtuelles doit continuer à fonctionner comme auparavant. |
| Passerelle de réseau virtuel |Passerelle de réseau virtuel |Les propriétés de la passerelle de réseau virtuel sont inchangées après la migration. L’adresse IP virtuelle associée à la passerelle reste inchangée également. |
| Site de réseau local |Passerelle de réseau local |Les propriétés du site de réseau local sont migrées sans modification vers une nouvelle ressource appelée Passerelle de réseau local. Ceci représente les préfixes d’adresse locale et l’adresse IP de la passerelle distante. |
| Références de connexions |Connexion |Les références de connectivité entre la passerelle et le site de réseau local dans la configuration réseau sont représentées par une ressource nouvellement créée appelée Connexion dans Resource Manager après la migration. Toutes les propriétés de référence de connectivité dans les fichiers de configuration réseau sont copiées sans modification dans la ressource de connexion nouvellement créée. La connectivité de réseau virtuel à réseau virtuel dans le déploiement Classic est obtenue en créant deux tunnels IPsec vers les sites de réseau local qui représentent les réseaux virtuels. Elle est transformée en type de connexion Vnet2Vnet dans le modèle Resource Manager sans qu’il soit nécessaire de faire appel à des passerelles de réseau local. |

## <a name="illustration-of-a-simple-migration-walkthrough"></a>Illustration d’une procédure pas à pas de migration simple
La capture d’écran suivante montre un service cloud existant avec une machine virtuelle (pas dans un réseau virtuel) après la phase de préparation :

![Représentation classique après préparation](./media/virtual-machines-windows-migration-classic-resource-manager/classic-migration-prepare-portal.png)

Une fois le processus de migration terminé, les captures d’écran suivantes montrent que les nouvelles ressources ont été créées dans un groupe de ressources : ![Représentation Resource Manager après la préparation](./media/virtual-machines-windows-migration-classic-resource-manager/resourcemanager-migration-prepare-portal.png)

## <a name="next-steps"></a>Étapes suivantes
À présent que vous savez en quoi consiste la migration des ressources IaaS Classic vers Resource Manager, vous pouvez commencer à effectuer la migration des ressources.

* [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide d’Azure PowerShell](virtual-machines-windows-ps-migration-classic-resource-manager.md)
* [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide de l’interface de ligne de commande Azure](virtual-machines-linux-cli-migration-classic-resource-manager.md)
* [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
* [Cloner une machine virtuelle Classic vers Azure Resource Manager à l’aide de scripts PowerShell](virtual-machines-windows-migration-scripts.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Passer en revue les erreurs courantes de migration](virtual-machines-migration-errors.md)




<!--HONumber=Jan17_HO5-->


