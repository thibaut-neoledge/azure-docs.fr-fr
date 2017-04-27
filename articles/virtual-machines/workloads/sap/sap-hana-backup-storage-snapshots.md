---
title: "Sauvegarde SAP HANA sur Azure à partir de captures instantanées de stockage | Microsoft Docs"
description: "Il existe deux grandes méthodes permettant d’effectuer des sauvegardes SAP HANA sur des machines virtuelles Azure. Cet article aborde la sauvegarde SAP HANA à partir de captures instantanées de stockage"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 3/13/2017
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 5d395da0779d84e414341d6d73151fd7e77e3e8e
ms.lasthandoff: 04/03/2017


---

# <a name="sap-hana-backup-based-on-storage-snapshots"></a>Sauvegarde SAP HANA à partir de captures instantanées de stockage

## <a name="introduction"></a>Introduction

Ce document fait partie d’une série d’articles connexes en trois parties dédiés à la sauvegarde SAP HANA. L’article [Backup guide for SAP HANA on Azure Virtual Machines](sap-hana-backup-guide.md) (Guide de sauvegarde pour SAP HANA sur les machines virtuelles Azure) fournit une vue d’ensemble et des informations sur la mise en route, et l’article [SAP HANA Azure Backup on file level](sap-hana-backup-file-level.md) (Sauvegarde SAP HANA sur Azure au niveau fichier) couvre l’option de sauvegarde à partir de fichiers.

Lorsque vous utilisez une fonctionnalité de sauvegarde de machine virtuelle pour un système démo tout-en-un à instance unique, vous devez envisager d’effectuer une sauvegarde de machine virtuelle plutôt que de gérer les sauvegardes HANA au niveau du système d’exploitation. Une autre solution consiste à prendre des captures instantanées d’objets blob Azure pour créer des copies individuelles de disques virtuels associés à une machine virtuelle, et à conserver les fichiers de données HANA. Mais lorsque vous créez une sauvegarde de machine virtuelle ou une capture instantanés de disque alors que le système est en cours d’exécution, vous devez veiller à préserver la cohérence des applications. Consultez la section relative à _la cohérence des données SAP HANA lors de la création de captures instantanées de stockage_ dans l’article [Backup guide for SAP HANA on Azure Virtual Machines](sap-hana-backup-guide.md) (Guide de sauvegarde pour SAP HANA sur les machines virtuelles Azure). SAP HANA dispose d’une fonction qui prend en charge ces types de captures instantanées de stockage.

## <a name="sap-hana-snapshots"></a>Captures instantanées de SAP HANA

Il existe dans SAP HANA une fonctionnalité qui prend en charge la création de captures instantanées de stockage. Mais depuis décembre 2016, cette fonction est limitée aux systèmes à conteneur unique. Les configurations de conteneur multitenant ne gèrent pas ce type de capture instantanée de la base de données (voir [Create a Storage Snapshot (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) [Créer une capture instantanée de stockage (SAP HANA Studio)]).

Elle fonctionne de la manière suivante :

- Préparez une capture instantanée du stockage en lançant la capture instantané SAP HANA
- Exécutez la capture instantanée de stockage (capture instantanée d’objets blob Azure, par exemple)
- Confirmer la capture instantanée SAP HANA

![Cette capture d’écran montre qu’une capture instantanée des données SAP HANA peut être créée via une instruction SQL](media/sap-hana-backup-storage-snapshots/image011.png)

Cette capture d’écran montre qu’une capture instantanée des données SAP HANA peut être créée via une instruction SQL.

![La capture instantanée apparaît également dans le catalogue de sauvegardes dans SAP HANA Studio](media/sap-hana-backup-storage-snapshots/image012.png)

La capture instantanée apparaît également dans le catalogue de sauvegardes dans SAP HANA Studio.

![Sur le disque, la capture instantanée apparaît dans le répertoire de données SAP HANA](media/sap-hana-backup-storage-snapshots/image013.png)

Sur le disque, la capture instantanée apparaît dans le répertoire de données SAP HANA.

Vous devez veiller à garantir également la cohérence du système de fichiers avant d’exécuter la capture instantanée du stockage lorsque SAP HANA est en mode de préparation de captures instantanées. Consultez la section relative à _la cohérence des données SAP HANA lors de la création de captures instantanées de stockage_ dans l’article [Backup guide for SAP HANA on Azure Virtual Machines](sap-hana-backup-guide.md) (Guide de sauvegarde pour SAP HANA sur les machines virtuelles Azure).

Une fois la capture instantanée du stockage effectuée, il est essentiel de confirmer la capture instantanée SAP HANA. Il existe une instruction SQL correspondante à exécuter : BACKUP DATA CLOSE SNAPSHOT (voir [BACKUP DATA CLOSE SNAPSHOT Statement (Backup and Recovery)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm) [Instruction BACKUP DATA CLOSE SNAPSHOT (sauvegarde et restauration]).

> [!IMPORTANT]
> Confirmez la capture instantanée HANA. En raison de la &quot;copie sur écriture&quot;, SAP HANA peuvent avoir besoin d’espace disque supplémentaire en mode de préparation de captures instantanées ; en outre, il n’est pas possible de démarrer de nouvelles sauvegardes tant que la capture instantanée SAP HANA est confirmée.

## <a name="hana-vm-backup-via-azure-backup-service"></a>Sauvegarde de machines virtuelles HANA via le service Azure Backup

À compter de décembre 2016, l’agent de sauvegarde du service Azure Backup n’est pas disponible pour les machines virtuelles Linux. Pour pouvoir utiliser la sauvegarde Azure au niveau du fichier/répertoire, vous devez copier les fichiers de sauvegarde SAP HANA sur une machine virtuelle Windows, puis utiliser l’agent de sauvegarde. Sinon, vous ne pouvez qu’effectuer une sauvegarde Linux complète à l’aide du service Azure Backup. Pour plus d’informations, consultez [Vue d’ensemble des fonctionnalités de sauvegarde Azure](../../../backup/backup-introduction-to-azure-backup.md).

Le service Azure Backup offre une option permettant de sauvegarder et restaurer une machine virtuelle. Pour plus d’informations sur ce service et sur son fonctionnement, consultez l’article [Planification de votre infrastructure de sauvegarde de machines virtuelles dans Azure](../../../backup/backup-azure-vms-introduction.md).

Cet article souligne deux principaux aspects à prendre en compte :

_&quot;Pour les machines virtuelles Linux, seule les sauvegardes fichiers compatibles sont possibles, car l’équivalent de la plateforme VSS n’existe pas sous Linux.&quot;_

_&quot;Les applications doivent implémenter leur propre mécanisme de &quot;correctif&quot; sur les données restaurées.&quot;_

Par conséquent, vérifiez la cohérence de SAP HANA sur le disque au moment du démarrage de la sauvegarde. Consultez la section _Captures instantanées de SAP HANA_ décrite précédemment dans ce document. Mais le fait que SAP HANA reste dans ce mode de préparation de captures instantanées soulève un problème potentiel. Pour plus d’informations, consultez la page [Create a Storage Snapshot (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) [Créer une capture instantanée de stockage (SAP HANA Studio]).

Cet article indique :

_&quot;Il est fortement recommandé de confirmer ou abandonner une capture instantanée de stockage dès que possible après sa création. Tant que la capture instantanée de stockage est en cours de préparation ou de création, les données associées à la capture instantanée sont figées. Tant que ces données sont figées, vous pouvez toujours effectuer des modifications dans la base de données. Ces modifications n’ont aucun effet sur les données figées associées à la capture instantanée. Les modifications sont en fait écrites dans la zone de données, à des emplacements distincts de la capture instantanée de stockage. Les modifications sont également écrites dans le journal. Cependant, plus les données de la capture instantanée restent figées, plus le volume de données peut augmenter.&quot;_

Azure Backup prend en charge la cohérence du système de fichiers via des extensions de machine virtuelle Azure. Ces extensions ne sont pas disponibles sous une forme autonome et fonctionnent uniquement en association avec le service Azure Backup. Cependant, il est toujours nécessaire de gérer un instantané SAP HANA pour garantir la cohérence de l’application.

Le service Azure Backup se décompose en deux grandes phases :

- Création d’une capture instantanée
- Transfert des données vers le coffre

Vous devez donc confirmer la capture instantanée SAP HANA dès la fin de la phase de création de capture instantanée du service Azure Backup. Vous devrez peut-être attendre plusieurs minutes avant de voir la capture dans le portail Azure.

![Cette illustration montre une partie de la liste des travaux de sauvegarde d’un service Azure Backup](media/sap-hana-backup-storage-snapshots/image014.png)

Cette illustration montre une partie de la liste des travaux de sauvegarde d’un service Azure Backup, qui a été utilisé pour sauvegarder la machine virtuelle test HANA.

![Pour afficher les détails du travail, cliquez sur le travail de sauvegarde dans le portail Azure](media/sap-hana-backup-storage-snapshots/image015.png)

Pour afficher les détails du travail, cliquez sur le travail de sauvegarde dans le portail Azure. On peut voir ici les deux phases. Vous devrez peut-être patienter quelques minutes pour que la phase de capture instantanée apparaisse comme terminée. Le plus long demeure la phase de transfert des données.

## <a name="hana-vm-backup-automation-via-azure-backup-service"></a>Automatisation des sauvegardes de machines virtuelles HANA via le service Azure Backup

Comme décrit précédemment, il est possible de confirmer manuellement la capture instantané SAP HANA une fois la phase de création de capture instantanée terminée dans Azure Backup, mais il est utile d’envisager une automatisation, car un administrateur peut ne pas analyser la liste des travaux de sauvegarde dans le portail Azure.

Voici comment effectuer cette automatisation via des applets de commande Azure PowerShell.

![Un service Azure Backup a été créé sous le nom hana-backup-vault](media/sap-hana-backup-storage-snapshots/image016.png)

Un service Azure Backup a été créé sous le nom &quot;hana-backup-vault.&quot; La commande PS **Get-AzureRmRecoveryServicesVault -Name hana-backup-vault** récupère l’objet correspondant. Cet objet est ensuite utilisé pour définir le contexte de sauvegarde comme illustré sur la figure suivante.

![On peut vérifier le travail de sauvegarde en cours d’exécution](media/sap-hana-backup-storage-snapshots/image017.png)

Après avoir défini le contexte approprié, on peut vérifier le travail de sauvegarde en cours puis rechercher les détails du travail. La liste des sous-tâches indique si la phase de capture instantanée du travail de sauvegarde Azure est déjà terminée :

```
$ars = Get-AzureRmRecoveryServicesVault -Name hana-backup-vault
Set-AzureRmRecoveryServicesVaultContext -Vault $ars
$jid = Get-AzureRmRecoveryServicesBackupJob -Status InProgress | select -ExpandProperty jobid
Get-AzureRmRecoveryServicesBackupJobDetails -Jobid $jid | select -ExpandProperty subtasks
```

![Interroger la valeur dans une boucle jusqu’à ce qu’elle apparaisse comme terminée](media/sap-hana-backup-storage-snapshots/image018.png)

Une fois que les détails du travail sont stockés dans une variable, il suffit d’utiliser la syntaxe PS pour accéder à la première entrée du tableau et récupérer la valeur d’état. Pour exécuter le script d’automatisation, interrogez la valeur dans une boucle jusqu’à ce qu’elle apparaisse comme &quot;terminée.&quot;

```
$st = Get-AzureRmRecoveryServicesBackupJobDetails -Jobid $jid | select -ExpandProperty subtasks
$st[0] | select -ExpandProperty status
```

## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>Clé de licence HANA et restauration de machine virtuelle via le service Azure Backup

Le service Azure Backup est conçu pour créer une nouvelle machine virtuelle pendant la restauration. Il n’existe aucun moyen actuellement d’effectuer une restauration &quot;in situ&quot; d’une machine virtuelle Azure existante.

![Cette illustration montre l’option de restauration du service Azure dans le portail Azure](media/sap-hana-backup-storage-snapshots/image019.png)

Cette illustration montre l’option de restauration du service Azure dans le portail Azure. Vous pouvez soit créer une machine virtuelle pendant la restauration, soit restaurer des disques. Après avoir restauré les disques, il est toujours nécessaire de créer une machine virtuelle en amont. Chaque fois qu’une machine virtuelle est créée sur Azure, l’ID unique de la machine virtuelle change (voir [Accessing and Using Azure VM Unique ID](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) Consultation et utilisation de l’ID unique d’une machine virtuelle Azure).

![Cette illustration montre l’ID unique d’une machine virtuelle Azure avant et après la restauration via le service Azure Backup](media/sap-hana-backup-storage-snapshots/image020.png)

Cette illustration montre l’ID unique d’une machine virtuelle Azure avant et après la restauration via le service Azure Backup. La clé de matériel SAP, qui est utilisée pour les licences SAP, utilise cet ID de machine virtuelle unique. En conséquence, une nouvelle licence SAP doit être installée après la restauration d’une machine virtuelle.

Une nouvelle fonctionnalité Azure Backup a été présentée en version préliminaire lors de la création du présent guide de sauvegarde. Elle permet d’effectuer une restauration au niveau fichier à partir de la capture instantanée de machine virtuelle créée dans le cadre de la sauvegarde de machine virtuelle. Cela évite d’avoir à déployer une nouvelle machine virtuelle ; par conséquent, l’ID unique de la machine virtuelle reste le même et aucune nouvelle clé de licence SAP HANA n’est requise. Vous trouverez plus d’informations sur cette fonctionnalité une fois qu’elle sera entièrement testée.

À terme, Azure Backup permettra de sauvegarder différents disques virtuels Azure, ainsi que des fichiers et des répertoires depuis la machine virtuelle elle-même. Azure Backup présente l’avantage de gérer toutes les sauvegardes, ce qui évite au client d’avoir à le faire. Si une restauration s’avère nécessaire, Azure Backup sélectionne la sauvegarde correcte à utiliser.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>Sauvegarde de machine virtuelle SAP HANA via une capture instantanée manuelle du disque

Au lieu d’utiliser le service Azure Backup, on peut configurer une solution de sauvegarde individuelle en créant manuellement des captures instantanées d’objet blob des disques durs virtuels Azure via PowerShell. Pour obtenir une description des étapes à suivre, consultez la page [Using blob snapshots with PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) (Utilisation des captures instantanées d’objets blob avec PowerShell).

Cette approche offre plus de souplesse, mais elle ne résout pas les problèmes décrits plus haut dans ce document :

- Il faut toujours veiller à ce que SAP HANA reste à l’état cohérent
- Le disque du système d’exploitation ne peut être remplacé, même si la machine virtuelle est désallouée en raison d’une erreur indiquant l’existence d’un bail. Il fonctionne uniquement après la suppression de la machine virtuelle, ce qui créerait un nouvel ID unique de machine virtuelle et supposerait d’installer une nouvelle licence SAP.

![Il est possible de restaurer uniquement les disques de données d’une machine virtuelle Azure](media/sap-hana-backup-storage-snapshots/image021.png)

Il est possible de restaurer uniquement les disques de données d’une machine virtuelle Azure, en évitant d’avoir à obtenir un nouvel ID unique de machine virtuelle et, par conséquent, d’invalider la licence SAP :

- Pour le test, deux disques de données Azure ont été attachés à une machine virtuelle et un RAID logiciel a été défini sur ces disques 
- La fonction de capture instantanée SAP HANA a permis de confirmer l’état cohérent de SAP HANA
- Blocage du système de fichiers (voir la section relative à _la cohérence des données SAP HANA lors de la création de captures instantanées de stockage_ dans l’article [Backup guide for SAP HANA on Azure Virtual Machines](sap-hana-backup-guide.md) (Guide de sauvegarde pour SAP HANA sur les machines virtuelles Azure)
- Des captures instantanées d’objets blob ont été créées à partir des deux disques de données
- Déblocage du système de fichiers
- Confirmation de la capture instantanée SAP HANA
- Pour restaurer les disques de données, la machine virtuelle a été arrêtée et les deux disques détachés
- Après le détachement de disques, ils ont été remplacés par les anciennes captures instantanées d’objets blob
- Les disques virtuels restaurés ont ensuite été rattachés à la machine virtuelle
- Après le démarrage de la machine virtuelle, tout ce qui se trouvait sur le RAID logiciel fonctionnait correctement et a été rétabli à l’heure de création de la capture instantanée d’objets blob
- HANA a été rétabli à l’heure de création de la capture instantanée HANA

S’il était possible d’arrêter SAP HANA avant les captures instantanées d’objets blob, la procédure serait moins complexe. Dans ce cas, on pourrait ignorer la capture instantanée HANA et, s’il ne se passe rien de plus dans le système, ignorer également le blocage du système de fichiers. Tout devient plus complexe dès lors qu’il est nécessaire d’effectuer des captures instantanées alors que tout est en ligne. Consultez la section relative à _la cohérence des données SAP HANA lors de la création de captures instantanées de stockage_ dans l’article [Backup guide for SAP HANA on Azure Virtual Machines](sap-hana-backup-guide.md) (Guide de sauvegarde pour SAP HANA sur les machines virtuelles Azure).

## <a name="next-steps"></a>Étapes suivantes
* L’article [Backup guide for SAP HANA on Azure Virtual Machines](sap-hana-backup-guide.md) (Guide de sauvegarde pour SAP HANA sur les machines virtuelles Azure) fournit une vue d’ensemble et des informations sur la mise en route.
* L’article [SAP HANA Azure Backup on file level](sap-hana-backup-file-level.md) (Sauvegarde SAP HANA sur Azure au niveau fichier) décrit l’option de sauvegarde à partir d’instantanés de stockage.
* Pour savoir comment établir une haute disponibilité et planifier la récupération d’urgence de SAP HANA sur Azure (instances de grande taille), consultez [Haute disponibilité et récupération d’urgence de SAP HANA (grandes instances) sur Azure](hana-overview-high-availability-disaster-recovery.md).

