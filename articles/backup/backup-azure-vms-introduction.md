---
title: Planification de votre infrastructure de sauvegarde de machines virtuelles dans Azure | Microsoft Docs
description: "Considérations importantes avant la sauvegarde de machines virtuelles dans Azure"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: sauvegarde de machines virtuelles
ms.assetid: 19d2cf82-1f60-43e1-b089-9238042887a9
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/1/2017
ms.author: markgal;trinadhk
translationtype: Human Translation
ms.sourcegitcommit: 4059254d2c8eadd92cb41c2dac901939f2436f5c
ms.openlocfilehash: 09a50164a6f501c974af0ccfcbf8b12f18afaeaf
ms.lasthandoff: 03/02/2017


---
# <a name="plan-your-vm-backup-infrastructure-in-azure"></a>Planification de votre infrastructure de sauvegarde de machines virtuelles dans Azure
Cet article formule des suggestions de performances et de ressources pour vous aider à planifier votre infrastructure de sauvegarde de machines virtuelles. Il définit également les principaux aspects du service Backup, qui peuvent être essentiels afin de vous aider à déterminer votre architecture, à planifier sa capacité et à définir des planifications. Si vous avez [préparé votre environnement](backup-azure-vms-prepare.md), la planification est l’étape à suivre avant de passer à la [sauvegarde des machines virtuelles](backup-azure-vms.md). Si vous avez besoin d’informations sur les machines virtuelles Azure, consultez la [Documentation sur les machines virtuelles](https://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="how-does-azure-back-up-virtual-machines"></a>Comment Azure sauvegarde-t-il des machines virtuelles Azure ?
Lorsque le service Azure Backup lance le travail de sauvegarde à l’heure planifiée, il déclenche l’extension de sauvegarde pour prendre un instantané à un moment donné. Cet instantané est pris de façon coordonnée avec le service VSS (Service de copie shadow de volume) pour obtenir un instantané cohérent des disques sur la machine virtuelle sans avoir à arrêter cette dernière.

Une fois l’instantané réalisé, les données sont transférées par le service Azure Backup dans un coffre de sauvegarde. Pour rendre le processus de sauvegarde plus efficace, le service identifie et transfère uniquement les blocs de données qui ont été modifiés depuis la sauvegarde précédente.

![Architecture de la sauvegarde des machines virtuelles Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

Une fois le transfert de données terminé, l’instantané est supprimé et un point de récupération est créé.

> [!NOTE]
> Azure Backup n’inclut pas de disque temporaire joint à la machine virtuelle lorsque vous effectuez la sauvegarde. En savoir plus sur le [disque temporaire](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)
>
>

### <a name="data-consistency"></a>Cohérence des données
La sauvegarde et la restauration des données critiques d’entreprise sont compliquées par le fait qu’elles doivent être sauvegardées alors que les applications qui génèrent les données sont en cours d’exécution. Pour résoudre ce problème, Azure Backup fournit des sauvegardes cohérentes avec les charges de travail de Microsoft en utilisant le service VSS pour vous assurer que les données sont correctement inscrites dans le compte de stockage.

> [!NOTE]
> Pour les machines virtuelles Linux, seule les sauvegardes fichiers compatibles sont possibles, car l’équivalent de la plateforme VSS n’existe pas sous Linux.
>
>

Azure Backup effectue la sauvegarde complète VSS sur les machines virtuelles Windows (en savoir plus sur la [sauvegarde complète VSS](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)). Pour activer les sauvegardes de copie VSS, la clé de registre suivant doit être définie sur la machine virtuelle.

```
[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
"USEVSSCOPYBACKUP"="TRUE"
```


Ce tableau décrit les types de compatibilité et les conditions dans lesquelles elles se produisent pendant les procédures de sauvegarde et de restauration de machine virtuelle Azure.

| Cohérence | En fonction du service VSS | Explication et détails |
| --- | --- | --- |
| Cohérence des applications |Oui |La cohérence des applications est idéale pour les charges de travail Microsoft, car elle apporte les garanties suivantes :<ol><li> la machine virtuelle *démarre*. <li>Les données *ne sont pas endommagées*. <li>Il n’y a *aucune perte de données*.<li> Les données sont cohérentes vis-à-vis de l’application qui les utilise grâce à la sollicitation de l’application au moment de la sauvegarde (à l’aide de VSS).</ol> La plupart des charges de travail de Microsoft ont des enregistreurs VSS qui effectuent des actions de charges de travail spécifiques relatives à la cohérence des données. Par exemple, Microsoft SQL Server dispose d’un enregistreur VSS qui garantit que les écritures dans le journal des transactions et de la base de données sont effectuées correctement.<br><br> Pour les sauvegardes d’une machine virtuelle Azure, la création d’un point de récupération cohérent signifie que l’extension de sauvegarde a pu appeler le flux de travail VSS et se terminer avant la prise de l’instantané de la machine virtuelle. Pour que l’instantané de machine virtuelle Azure soit précis, les enregistreurs VSS de toutes les applications de machine virtuelle Azure doivent également se terminer.<br><br>(Découvrez les [principes de base du service VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) et son [fonctionnement](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx)). |
| Cohérence du système de fichiers |Oui : pour les ordinateurs Windows |Il existe deux scénarios où le point de récupération peut être *cohérent avec le système de fichiers* :<ul><li>Les sauvegardes de machines virtuelles Linux dans Azure, car l’équivalent de la plateforme VSS n’existe pas sous Linux.<li>Échec de VSS lors de la sauvegarde de machines virtuelles Windows dans Azure.</li></ul> Dans les deux cas, la meilleure solution consiste à s’assurer que les conditions suivantes sont satisfaites : <ol><li> la machine virtuelle *démarre*. <li>Les données *ne sont pas endommagées*.<li>Il n’y a *aucune perte de données*.</ol> Les applications doivent implémenter leur propre mécanisme de « correctif » sur les données restaurées. |
| Cohérence en cas d’incident |Non |Cette situation est la même que lorsqu’une machine virtuelle rencontre un « incident » (via une réinitialisation matérielle ou logicielle). La cohérence en cas d’incident se produit généralement lorsque la machine virtuelle Azure est arrêtée au moment de la sauvegarde. Un point de récupération cohérent ne fournit aucune garantie de cohérence des données sur le support de stockage, que ce soit au niveau du système d’exploitation ou de l’application. Seules les données déjà présentes sur le disque au moment de la sauvegarde sont capturées et sauvegardées. <br/> <br/> Même s’il n’existe aucune garantie, le démarrage a généralement lieu et est suivi d’une procédure de vérification du disque comme chkdsk permettant de résoudre les erreurs d’endommagement. Les données ou les écritures en mémoire qui n’ont pas été transférées sur le disque sont perdues. Si une restauration de données est nécessaire, l’application suit généralement son propre mécanisme de vérification. <br><br>Par exemple, cette situation peut survenir si le journal des transactions comporte des entrées qui n’existent pas dans la base de données. Le logiciel de base de données effectue alors une restauration jusqu’à ce que les données soient cohérentes. Lorsque des données sont réparties sur plusieurs disques virtuels (comme des volumes fractionnés), un point de récupération cohérent après incident ne fournit aucune garantie quant à l’exactitude des données. |

## <a name="performance-and-resource-utilization"></a>Performance et utilisation des ressources
Au même titre qu’un logiciel de sauvegarde déployé sur site, il est recommandé de planifier les besoins en capacité et en consommation de ressources lors d’une sauvegarde de machines virtuelles dans Azure. Les [limites d’Azure Storage](../azure-subscription-service-limits.md#storage-limits) définissent la structuration des déploiements de machine virtuelle pour obtenir des performances maximales avec un impact minimal sur l’exécution des charges de travail.

Tenez compte des limites de stockage Azure indiquées ci-dessous lorsque vous planifiez vos performances de sauvegarde :

* Nombre maximal de sorties par compte de stockage
* Taux de requête total par compte de stockage

### <a name="storage-account-limits"></a>Limites de compte de stockage
À chaque fois que des données de sauvegarde sont copiées à partir d’un compte de stockage, l’opération est comptabilisée dans les mesures de l’IOPS (entrées/sorties par seconde) et des sorties (débit) du compte de stockage. Dans le même temps, les machines virtuelles consomment des IOPS et débit. L’objectif est de vous assurer que le trafic de sauvegarde et de machine virtuelle ne dépasse pas les limites du compte de stockage.

### <a name="number-of-disks"></a>Nombre de disques
Le processus de sauvegarde tente d’exécuter un travail de sauvegarde aussi rapidement que possible. Autrement dit, il consomme un maximum de ressources. Cependant, toutes les opérations d’E/S sont limitées par le *débit cible d’un seul objet Blob*, qui est limité à 60 Mo par seconde. Afin de tenter d’accélérer l’opération, le processus de sauvegarde tente de sauvegarder chacun des disques de la machine virtuelle *en parallèle*. Si une machine virtuelle comprend quatre disques, le service tente de sauvegarder les quatre disques simultanément. Le facteur le plus important pour déterminer le trafic de sauvegarde sortant d’un compte de stockage client est le **nombre de disques** sauvegardés.

### <a name="backup-schedule"></a>Planification de sauvegarde
Autre facteur ayant un impact sur les performances : la **planification de sauvegarde**. Configurer vos stratégies de manière à sauvegarder simultanément toutes les machines virtuelles revient à planifier un embouteillage. Le processus de sauvegarde tentera de sauvegarder tous les disques en parallèle. Il est possible de réduire le trafic de sauvegarde depuis un compte de stockage en s’assurant que les différentes machines virtuelles sont sauvegardées à différents moments de la journée, sans chevauchement.

## <a name="capacity-planning"></a>Planification de la capacité
En associant les facteurs précédents, vous devez planifier les besoins d’utilisation du compte de stockage. Téléchargez la [feuille de calcul Excel de planification des capacités des machines virtuelles](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) pour voir l’impact de vos choix en matière de planification de disques et de sauvegardes.

### <a name="backup-throughput"></a>Débit de sauvegarde
Pour chaque disque en cours de sauvegarde, Azure Backup lit les blocs sur le disque et stocke uniquement les données modifiées (sauvegarde incrémentielle). Le tableau suivant montre les valeurs moyennes de débit du service de sauvegarde. Utilisez les données suivantes pour estimer le temps nécessaire à la sauvegarde d’un disque d’une taille donnée.

| Opération de sauvegarde | Meilleur débit |
| --- | --- |
| Sauvegarde initiale |160 Mbits/s |
| Sauvegarde incrémentielle (DR) |640 Mbits/s  <br><br> Le débit diminue de manière significative si les données modifiées (qui doivent être sauvegardées) sont dispersées sur le disque.|

## <a name="total-vm-backup-time"></a>Durée de sauvegarde de machine virtuelle totale
La majeure partie du temps de sauvegarde est consacrée à la lecture et la copie des données, mais il existe d’autres opérations qui contribuent à la durée totale nécessaire à la sauvegarde d’une machine virtuelle :

* Délai nécessaire à l’ [installation ou à la mise à jour de l’extension de sauvegarde](backup-azure-vms.md).
* Heure de l'instantané, qui est la durée nécessaire au déclenchement d’un instantané. Les instantanés sont déclenchés peu avant l’heure de sauvegarde planifiée.
* Délai d’attente de file d’attente. Comme le service Backup traite les sauvegardes de plusieurs clients, il se peut que la copie de données de sauvegarde de l’instantané vers l’archivage Backup ou Recovery Services ne démarre pas immédiatement. Lors des pics de charge, les temps d’attente peuvent durer jusqu’à huit heures en raison du nombre de sauvegardes à traiter. Toutefois, la durée de sauvegarde totale d’un ordinateur virtuel est inférieure à 24 heures pour des stratégies de sauvegarde quotidiennes.
* Le temps de transfert de données, le temps nécessaire pour que le service de sauvegarde calcule les modifications incrémentielles à partir d’une sauvegarde précédente de calcul et de transfère ces modifications dans l’archivage.

### <a name="why-am-i-observing-longer15-hours-backup-time"></a>Pourquoi les temps de sauvegarde (>15 heures) semblent-ils plus longs ?
La sauvegarde consiste en deux phases : la prise d’instantané et le transfert de l’instantané vers l’archivage. Le service de sauvegarde optimise pour le stockage. Lorsque vous transférez des données d’instantané dans un coffre, le service transfère uniquement les modifications incrémentielles à partir de l’instantané précédent.  Pour déterminer les modifications incrémentielles, le service calcule la somme de contrôle des blocs. Si un bloc est modifié, le bloc est identifié comme un bloc à envoyer au coffre. Le service se penche ensuite sur chacun des blocs identifiés, à la recherche d’opportunités de réduire les données à transférer. Après avoir évalué tous les blocs modifiés, le service fusionne les modifications et les envoie au coffre. Dans le cas de certaines applications héritées, les écritures de petite taille et fragmentées ne sont pas optimales pour le stockage. Si l’instantané contient un grand nombre d’écritures de petite taille et fragmentées, le service consacre plus de temps à traiter les données écrites par les applications. La taille de bloc d’écriture d’application recommandée depuis Azure pour les applications exécutées dans la machine virtuelle est de huit ko au minimum. Si votre application utilise un bloc de moins de huit ko, les performances de sauvegarde sont affectées. Nous vous invitons à consulter [Paramétrage des applications pour optimiser les performances du stockage Azure](../storage/storage-premium-storage-performance.md) pour vous aider à configurer votre application et améliorer les performances de sauvegarde. Bien que l’article sur les performances de sauvegarde utilise des exemples de stockage Premium, il est aussi applicable aux disques de stockage Standard.

## <a name="total-restore-time"></a>Temps de restauration total
Une opération de restauration se compose de deux sous-tâches principales : copie de données depuis un coffre vers le compte de stockage choisi et création de la machine virtuelle. La copie de données depuis un coffre dépend de l’endroit où les sauvegardes sont stockées en interne dans Azure et de l’endroit où le compte de stockage client est stocké. Le temps nécessaire pour copier des données dépend des paramètres suivants :
* Délai d’attente de la file : étant donné que le service traite des restaurations à partir de plusieurs clients en même temps, les demandes de restauration sont mises en file d’attente.
* Heure de copie des données - si nécessaire, les données sont d’abord copiées du coffre vers le compte de stockage client. Si les données doivent être copiées vers le compte de stockage avant que le service de sauvegarde écrive les données depuis le coffre, le temps de copie augmente. Pour réduire le temps de copie pendant le processus de restauration, sélectionnez un compte de stockage non chargé avec d’autres lectures et écritures d’application.

## <a name="best-practices"></a>Meilleures pratiques
Nous vous suggérons de suivre les meilleures pratiques ci-dessous lors de la configuration de sauvegardes de machines virtuelles :

* Ne planifiez pas la sauvegarde de plus de 10 machines virtuelles classiques à partir du même service cloud en même temps. Nous vous suggérons d’échelonner les planifications de sauvegarde d’une heure si vous souhaitez sauvegarder davantage de machines virtuelles à partir du même service cloud.
* Ne planifiez pas la sauvegarde simultanée de plus de 40 machines virtuelles simultanément.
* Planifiez les sauvegardes des machines virtuelles pendant les heures creuses afin que le service Backup utilise le nombre maximal d’opérations d’E/S par seconde pour transférer des données à partir du compte de stockage client vers l’archivage Backup ou Recovery Services.
* Veillez à appliquer une stratégie aux machines virtuelles réparties entre les différents comptes de stockage. Nous vous suggérons de limiter à 20 le nombre total de disques d’un même compte de stockage protégés par la même planification de sauvegarde. Si votre compte de stockage contient plus de 20 disques, répartissez ces machines virtuelles entre plusieurs stratégies afin d’obtenir les IOPS requises pendant la phase de transfert du processus de sauvegarde.
* Évitez de restaurer une machine virtuelle exécutée sur le stockage Premium vers le même compte de stockage. Si le processus de restauration coïncide avec l’opération de sauvegarde, cela réduira les IOPS disponibles pour la sauvegarde.
* Nous vous recommandons d’exécuter chaque machine virtuelle Premium sur un compte de stockage Premium distinct afin de garantir des performances de sauvegarde optimales.

## <a name="data-encryption"></a>Chiffrement des données
Azure Backup ne chiffre pas les données dans le cadre du processus de sauvegarde. Toutefois, vous pouvez chiffrer les données dans la machine virtuelle et sauvegarder les données protégées en toute transparence (pour en savoir plus, lire [sauvegarde des données chiffrées](backup-azure-vms-encryption.md)).

## <a name="calculating-the-cost-of-protected-instances"></a>Calcul du coût des instances protégées
Les machines virtuelles Azure sauvegardées par le biais d’Azure Backup sont soumises à la [Tarification d’Azure Backup](https://azure.microsoft.com/pricing/details/backup/). Le calcul des instances protégées est basé sur la taille *réelle* de la machine virtuelle, qui est la somme de toutes les données de la machine virtuelle, à l’exception du « disque de ressources ».

La facturation pour la sauvegarde des machines virtuelles n’est *pas* basée sur la taille maximale prise en charge pour chaque disque de données joint à la machine virtuelle. La facturation est basée sur les données réelles stockées sur le disque de données. De même, la facture du stockage de sauvegarde est basée sur la quantité de données stockées avec Azure Backup, qui est la somme des données réelles de chaque point de récupération.

Prenons l’exemple d’une machine virtuelle de taille standard A2 avec deux disques de données supplémentaires d’une taille maximale de 1 To. Le tableau suivant indique les données réelles stockées sur chacun de ces disques :

| Type de disque | Taille maximale | Données réelles présentes |
| --- | --- | --- |
| Disque de système d’exploitation |1 023 Go |17 Go |
| Disque local/Disque de ressources |135 Go |5 Go (non inclus pour la sauvegarde) |
| Disque de données 1 |1 023 Go |30 Go |
| Disque de données 2 |1 023 Go |0 Go |

La taille *réelle* de la machine virtuelle est dans ce cas 17 Go + 30 Go + 0 Go= 47 Go. Cette taille d’instance protégée (47 Go) devient la base de la facture mensuelle. La taille de l’instance protégée utilisée pour la facturation augmente proportionnellement à la quantité de données issues de la machine virtuelle.

La facturation ne commence pas avant la fin de la première sauvegarde réussie. À partir de ce moment, la facturation du stockage et des instances protégées commence. La facturation continue tant que des *données de sauvegarde sont stockées avec Azure Backup* pour la machine virtuelle. L’opération Arrêter la protection n’arrête pas la facturation si les données de sauvegarde sont conservées.

La facturation pour une machine virtuelle spécifiée est interrompue uniquement si la protection est arrêtée *et* que les données de sauvegarde sont supprimées. Si aucun travail de sauvegarde n’est actif (et que la protection a été arrêtée), la taille de la machine virtuelle au moment de la dernière sauvegarde réussie devient la nouvelle taille d’instance protégée utilisée pour calculer la facture mensuelle.

## <a name="questions"></a>Des questions ?
Si vous avez des questions ou si vous souhaitez que certaines fonctionnalités soient incluses, [envoyez-nous vos commentaires](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Étapes suivantes
* [Sauvegarde de machines virtuelles](backup-azure-vms.md)
* [Gestion de la sauvegarde de machine virtuelle](backup-azure-manage-vms.md)
* [Restauration des machines virtuelles](backup-azure-restore-vms.md)
* [Résoudre les problèmes de sauvegarde de machines virtuelles](backup-azure-vms-troubleshoot.md)

