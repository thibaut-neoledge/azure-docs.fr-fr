---
title: Erreurs courantes lors de la migration de Classic vers Azure Resource Manager | Microsoft Docs
description: "Cet article répertorie les erreurs les plus courantes et leur atténuation lors la migration de ressources IaaS d’Azure Service Management vers la pile Azure Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 5bc03a1b-eb1c-438c-83d9-f0e9d61f1b6a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/13/2016
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 345db9b2e45937ea45329acf780601e4e0fbd504


---
# <a name="common-errors-during-classic-to-azure-resource-manager-migration"></a>Erreurs courantes lors de la migration de Classic vers Azure Resource Manager
Cet article répertorie les erreurs les plus courantes et leur atténuation lors la migration de ressources IaaS du modèle de déploiement Azure Classic vers la pile Azure Resource Manager.

## <a name="list-of-errors"></a>Liste d’erreurs
| Chaîne d’erreur | Atténuation |
| --- | --- |
| Erreur interne du serveur |Dans certains cas, il s’agit d’une erreur temporaire qui disparaît à la tentative suivante. Si elle persiste, [contactez le support technique Azure](../azure-supportability/how-to-create-azure-support-request.md), car un examen des journaux de la plateforme est nécessaire. <br><br> **REMARQUE :** lorsque l’incident est suivi par l’équipe de support, ne tentez aucune procédure d’atténuation automatique, car cela pourrait avoir des conséquences inattendues sur votre environnement. |
| La migration n’est pas prise en charge pour le déploiement {deployment-name} dans le service hébergé {hosted-service-name} car il s’agit d’un déploiement PaaS (Web/Worker). |Cela se produit lorsque le déploiement contient un rôle web/de travail. Étant donné que la migration n’est prise en charge que pour les Machines Virtuelles, veuillez supprimer le rôle web/de travail du déploiement et effectuer une nouvelle tentative de migration. |
| Échec du déploiement du modèle {template-name}. CorrelationId={guid} |Sur le serveur principal du service de migration, nous utilisons des modèles Azure Resource Manager pour créer des ressources dans la pile Azure Resource Manager. Les modèles étant idempotents, vous pouvez généralement réessayer en toute sécurité l’opération de migration pour passer outre cette erreur. Si elle persiste, veuillez [contactez le support Azure](../azure-supportability/how-to-create-azure-support-request.md) et lui donner le CorrelationId. <br><br> **REMARQUE :** lorsque l’incident est suivi par l’équipe de support, ne tentez aucune procédure d’atténuation automatique, car cela pourrait avoir des conséquences inattendues sur votre environnement. |
| Le réseau virtuel {virtual-network-name} n’existe pas. |Cela peut se produire si vous avez créé le réseau virtuel dans le nouveau Portail Azure. Le nom réel du réseau virtuel suit le modèle « Group * <VNET name>». |
| La machine virtuelle {vm-name} du service hébergé {hosted-service-name} contient une extension {extension-name} qui n’est pas prise en charge dans Azure Resource Manager. Il est recommandé de la désinstaller de la machine virtuelle avant de poursuivre la migration. |Les extensions XML telles que BGInfo 1.* ne sont pas prises en charge dans Azure Resource Manager. Par conséquent, elles ne peuvent pas faire l’objet d’une migration. Si elles sont toujours installées sur la machine virtuelle, elles sont automatiquement désinstallées avant la fin de la migration. |
| La machine virtuelle {vm-name} du service hébergé {hosted-service-name} contient l’extension VMSnapshot/VMSnapshotLinux, dont la migration n’est actuellement pas prise en charge. Désinstallez-la de la machine virtuelle et rajoutez-la à l’aide d’Azure Resource Manager une fois la migration terminée. |C’est le scénario dans lequel la machine virtuelle est configurée pour la Sauvegarde Azure. Étant donné qu’il n’est pas pris en charge pour le moment, suivez la solution de contournement à la page https://aka.ms/vmbackupmigration |
| La machine virtuelle {vm-name} du service hébergé {hosted-service-name} contient une extension {extension-name} dont l’état n’est pas généré à partir de la machine virtuelle. Par conséquent, elle ne peut pas faire l’objet d’une migration. Assurez-vous que l’état de l’extension est généré ou désinstallez l’extension de la machine virtuelle et effectuez une nouvelle tentative de migration. <br><br> La machine virtuelle {vm-name} du service hébergé {hosted-service-name} contient une extension {extension-name} qui génère l’état de gestionnaire : {handler-status}. Par conséquent, la machine virtuelle ne peut pas être migrée. Assurez-vous que l’état du gestionnaire de l’extension généré est {handler-status} ou désinstallez-la de la machine virtuelle et effectuez une nouvelle tentative de migration. <br><br> L’agent de la machine virtuelle {vm-name} du service hébergé {hosted-service-name} génère l’état global « Pas prêt ». Par conséquent, la machine virtuelle ne peut pas faire l’objet d’une migration, si elle a une extension migrable. Assurez-vous que l’agent de la machine virtuelle génère l’état global de l’agent « Prêt ». Reportez-vous à https://aka.ms/classiciaasmigrationfaqs. |L’agent invité Azure et les extensions de machines virtuelles ont besoin d’un accès Internet sortant au compte de stockage de la machine virtuelle pour remplir leur état. Causes les plus courantes d’échec de l’état : <li> un groupe de sécurité réseau bloque l’accès sortant à Internet ;
 <li> le réseau virtuel a des serveurs DNS locaux et la connectivité DNS est perdue. <br><br> Si vous continuez à voir l’état « non pris en charge », vous pouvez désinstaller les extensions pour ignorer cette vérification et poursuivre la migration. |
| La migration n’est pas prise en charge pour le déploiement {deployment-name} dans le service hébergé {hosted-service-name} car il contient plusieurs groupes à haute disponibilité. |Actuellement, seuls les services hébergés contenant au maximum un groupe à haute disponibilité peuvent faire l’objet d’une migration. Pour contourner ce problème, déplacez les groupes à haute disponibilité excédentaires et les machines virtuelles qu’ils contiennent vers un autre service hébergé. |
| La migration n’est pas prise en charge pour le déploiement {deployment-name} dans le service hébergé {hosted-service-name} car il contient des machines virtuelles qui ne font pas partie du groupe à haute disponibilité, même si le service hébergé en contient une. |La solution de contournement de ce scénario consiste à déplacer toutes les machines virtuelles dans un même groupe à haute disponibilité ou à supprimer toutes les machines virtuelles du groupe à haute disponibilité dans le service hébergé. |
| Le compte de stockage/service hébergé/réseau virtuel {virtual-network-name} est en cours de migration et, par conséquent, n’est pas modifiable. |Cette erreur se produit lorsque l’opération de migration « Préparer » a été effectuée sur la ressource et qu’une opération susceptible d’apporter une modification à la ressource est déclenchée. En raison du verrouillage du plan de gestion après l’opération « Préparer », les modifications apportées à la ressource sont bloquées. Pour déverrouiller le plan de gestion, vous pouvez exécuter l’opération de migration « Valider » pour terminer la migration ou l’opération de migration « Abandonner » pour annuler l’opération « Préparer ». |
| La migration n’est pas autorisée pour le service hébergé {hosted-service-name} car il contient une machine virtuelle {vm-name} à l’état : RoleStateUnknown. La migration n’est autorisée que lorsque la machine virtuelle est à l’un des états suivants : En cours d’exécution, Arrêtée, Arrêtée libérée. |La machine virtuelle peut être en cours de transition d’état, ce qui se produit habituellement lors d’une opération de mise à jour sur le service hébergé, comme un redémarrage, l’installation d’une extension, etc. Il est recommandé d’attendre que l’opération de mise à jour soit terminée sur le service hébergé avant d’essayer d’effectuer la migration. |

## <a name="next-steps"></a>Étapes suivantes
Voici une liste d’articles qui expliquent le processus de migration.

* [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
* [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide d’Azure PowerShell](virtual-machines-windows-ps-migration-classic-resource-manager.md)
* [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide de l’interface de ligne de commande Azure](virtual-machines-linux-cli-migration-classic-resource-manager.md)




<!--HONumber=Nov16_HO3-->


