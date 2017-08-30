---
title: "Résolution des problèmes Azure Disk Encryption | Microsoft Docs"
description: "Cet article contient des conseils de dépannage concernant Microsoft Azure Disk Encryption pour les machines virtuelles IaaS Windows et Linux."
services: security
documentationcenter: na
author: deventiwari
manager: avibm
editor: yuridio
ms.assetid: ce0e23bd-07eb-43af-a56c-aa1a73bdb747
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: devtiw
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 5f482a92b8fcd71a1b767fcc5741bc57605997ea
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Guide de dépannage Azure Disk Encryption

Ce guide s’adresse aux informaticiens professionnels, aux analystes de la sécurité des informations et aux administrateurs de cloud dont les organisations utilisent Azure Disk Encryption et qui ont besoin de conseils pour résoudre les problèmes liés au chiffrement de disque.

## <a name="troubleshooting-linux-os-disk-encryption"></a>Résolution des problèmes de chiffrement de disque de système d’exploitation Linux

Pour chiffrer le disque du système d’exploitation Linux, il faut démonter le lecteur du système d’exploitation avant l’exécution du chiffrement de disque complet.   Si le service ne parvient pas à démonter ce lecteur, un message d’erreur du type « Échec du démontage après... » peut s’afficher.

Cela se produit souvent lors d’une tentative de chiffrement de disque dans un environnement de machines virtuelles cible, quand l’image de ce dernier a été modifiée par rapport à celle de la galerie d’images stock (officielles) prises en charge.  Voici quelques exemples de modifications de l’image officielle qui peuvent interférer avec la capacité de l’extension à démonter le lecteur du système d’exploitation :
- Images personnalisées qui ne correspondent plus au système de fichiers et/ou au schéma de partitionnement pris en charge.
- Images personnalisées avec des applications telles qu’un antivirus, Docker, SAP, MongoDB ou Apache Cassandra s’exécutant dans le système d’exploitation avant le chiffrement.  Ces applications sont difficiles à terminer et lorsqu’elles conservent des descripteurs de fichiers ouverts sur le lecteur du système d’exploitation, le lecteur ne peut pas être démonté, à l’origine de défaillances.
- Les scripts personnalisés qui s’exécutent à proximité de l’étape de chiffrement permettent d’interférer et de provoquer cette défaillance. Cela peut se produire lorsqu’un modèle Resource Manager définit plusieurs extensions à exécuter simultanément, ou lorsqu’une extension de script personnalisé ou une autre action est exécutée en même temps que le chiffrement de disque.   L’exécution de ces étapes de manière sérialisée et isolée peut résoudre le problème.
- Si SELinux n’est pas désactivé avant l’activation du chiffrement, l’étape de démontage échoue.  SELinux peut être réactivé à la fin du chiffrement.
- Lorsque le disque du système d’exploitation utilise un schéma LVM (même si une prise en charge limitée des disques de données LVM existe, ce n’est pas le cas pour le disque du système d’exploitation LVM).
- Lorsque les conditions requises en matière de capacité de mémoire minimale ne sont pas remplies (7 Go sont recommandés pour le chiffrement de disque de système d’exploitation).
- Lorsque les lecteurs de données ont été montés de manière récursive sous le répertoire /mnt/ou les uns sous les autres (par exemple /mnt/data1, /mnt/data2, /data3 + /data3/data4, etc.).
- Lorsque les autres [conditions préalables](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption) pour l’utilisation d’Azure Disk Encryption pour Linux ne sont pas satisfaites.

## <a name="unable-to-encrypt"></a>Chiffrement impossible

Dans certains cas, le chiffrement de disque Linux semble être bloqué à l’étape « OS disk encryption started » (le chiffrement du disque du système d’exploitation a démarré) et SSH est désactivé. Ce processus peut prendre entre 3 et 16 heures sur une image de galerie de stock.  Si des disques de données de plusieurs To sont ajoutés, le processus peut prendre des jours. La séquence de chiffrement du disque du système d’exploitation Linux démonte temporairement le lecteur du système d’exploitation et effectue un chiffrement bloc par bloc de la totalité du disque du système d’exploitation, avant de le remonter dans son état chiffré.   Contrairement au chiffrement de disque Azure sous Windows, le chiffrement de disque Linux n’autorise pas l’utilisation simultanée de la machine virtuelle pendant le chiffrement.  Les caractéristiques de performances de la machine virtuelle, y compris la taille du disque et le type de compte de stockage utilisé (SSD Standard ou Premium), peuvent avoir un effet important sur la durée du chiffrement.

Pour vérifier l’état d’avancement du chiffrement, vous pouvez interroger le champ ProgressMessage renvoyé via la commande [Get-AzureRmVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus).   Pendant le chiffrement du lecteur du système d’exploitation, la machine virtuelle passe en état de maintenance et SSH est également désactivé pour éviter toute interruption du processus en cours.  Le plus souvent, l’état EncryptionInProgress est renvoyé pendant que le chiffrement est en cours, et il est suivi quelques heures plus tard par le message VMRestartPending invitant à redémarrer la machine virtuelle.  Par exemple :


```
PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

Après avoir été invité à redémarrer la machine virtuelle et laissé 2 à 3 minutes à cette dernière pour le redémarrage et la finalisation des dernières étapes sur la cible, le message d’état indique que le chiffrement est terminé.   Lorsque ce message s’affiche, le lecteur de système d’exploitation chiffré est prêt à être utilisé et la machine virtuelle est de nouveau disponible.

Lorsque cette séquence ne se déroule pas comme indiqué, ou si les informations de démarrage, le message de progression ou d’autres messages d’erreur indiquent que le chiffrement du système d’exploitation a échoué en cours de processus (par exemple si l’erreur de type « Échec du démontage » décrite plus tôt dans ce guide s’affiche), nous vous recommandons de restaurer la machine virtuelle à partir de l’instantané ou de la sauvegarde effectué(e) immédiatement avant le chiffrement.  Avant d’effectuer une autre tentative, il est recommandé de réévaluer les caractéristiques de la machine virtuelle et de vous assurer que toutes les conditions préalables sont satisfaites.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Résolution des problèmes Azure Disk Encryption derrière un pare-feu
Lorsque la connectivité est limitée par un pare-feu, une exigence de proxy ou des paramètres de groupes de sécurité réseau (NSG), cela peut interférer avec la capacité de l’extension à effectuer les tâches nécessaires.   Des messages d’état de type « État de l’extension non disponible sur la machine virtuelle » peuvent s’afficher, et les scénarios attendus peuvent échouer.  Les sections qui suivent décrivent certains problèmes courants au niveau du pare-feu qui valent la peine d’être examinés.

### <a name="network-security-groups"></a>groupes de sécurité réseau ;
Les paramètres de groupes de sécurité réseau appliqués doivent permettre au point de terminaison de remplir les [conditions préalables](https://docs.microsoft.com/azure/security/azure-security-disk-encryption#prerequisites) de configuration réseau indiquées pour le chiffrement de disque.

### <a name="azure-keyvault-behind-firewall"></a>Azure Key Vault derrière un pare-feu
La machine virtuelle doit pouvoir accéder au coffre de clés. Reportez-vous au guide d’accès au coffre de clés derrière un pare-feu géré par l’équipe de [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall).

### <a name="linux-package-management-behind-firewall"></a>Gestion des packages Linux derrière un pare-feu
Au moment de l’exécution, Azure Disk Encryption pour Linux s’appuie sur le système de gestion de package de la distribution cible pour installer les composants prérequis nécessaires à l’activation du chiffrement.  Si les paramètres de pare-feu empêchent la machine virtuelle de télécharger et d’installer ces composants, des échecs sont à prévoir.    Les étapes de configuration spécifiques peuvent varier selon la distribution.  Sous Red Hat, lorsqu’un proxy est requis, vous devez absolument vous assurer que subscription-manager et yum sont configurés correctement.  Consultez [l’article](https://access.redhat.com/solutions/189533) de support Red Hat à ce propos.  

## <a name="troubleshooting-windows-server-2016-server-core"></a>Résolution des problèmes Windows Server 2016 Server Core

Sur Windows Server 2016 Server Core, le composant bdehdcfg n’est pas disponible par défaut. Ce composant est requis par Azure Disk Encryption.

Pour contourner ce problème, copiez les 4 fichiers suivants à partir d’une machine virtuelle Windows Server 2016 Data Center dans le dossier c:\windows\system32 de l’image de Server Core :

```
bdehdcfg.exe
bdehdcfglib.dll
bdehdcfglib.dll.mui
bdehdcfg.exe.mui
```

Exécutez ensuite la commande suivante :

```
bdehdcfg.exe -target default
```

Cette opération permet de créer une partition du système de 550 Mo, puis après un redémarrage, vous pouvez utiliser Diskpart pour vérifier les volumes et continuer.  

Par exemple :

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
## <a name="see-also"></a>Voir aussi
Ce document vous a fait découvrir certains problèmes couramment rencontrés dans Azure Disk Encryption, et en a décrit la résolution. Pour plus d’informations sur ce service et ses fonctionnalités, consultez ces pages :

- [Appliquer le chiffrement de disque dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Chiffrement d’une machine virtuelle Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Azure Data Encryption-at-Rest](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest) (Chiffrement des données Azure au repos)

