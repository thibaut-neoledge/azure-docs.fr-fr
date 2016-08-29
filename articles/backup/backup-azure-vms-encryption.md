<properties
   pageTitle="Azure Backup - Sauvegarde de machines virtuelles IaaS Azure avec disques chiffrés | Microsoft Azure"
   description="Découvrez comment Azure Backup gère les données chiffrées à l'aide de BitLocker ou dmcrypt lors de la sauvegarde d'une machine virtuelle IaaS. Cet article vous présente les différences entre les expériences de sauvegarde et de restauration avec des disques chiffrés."
   services="backup"
   documentationCenter=""
   authors="pallavijoshi"
   manager="vijayts"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="08/16/2016"
   ms.author="markgal; jimpark; trinadhk"/>

# Gestion des disques chiffrés lors de la sauvegarde des machines virtuelles

Pour les entreprises qui cherchent à chiffrer leurs données de machine virtuelle dans Azure, la solution consiste à utiliser [Azure Disk Encryption](../security/azure-security-disk-encryption.md) ou Bitlocker sous Windows ou dmcrypt sur les machines Linux.

> [AZURE.NOTE]  Azure Backup prend en charge la sauvegarde et la restauration de machines virtuelles chiffrées à l’aide d'Azure Disk Encryption (ADE). <br>
1. Ces opérations sont prises en charge à l’aide de PowerShell si la machine virtuelle est chiffrée à l’aide de clés BEK et KEK. <br>
2. La sauvegarde et la restauration ne sont pas prises en charge si la machine virtuelle est chiffrée à l’aide d'une clé BEK uniquement. <br> Veuillez vous reporter à la [documentation PowerShell](backup-azure-vms-automation.md) Azure Backup pour sauvegarder et restaurer des machines virtuelles chiffrées à l’aide d'ADE.

Cet article concerne les machines virtuelles Azure chiffrées à l’aide de CloudLink.

## Fonctionnement de la sauvegarde

La solution globale est constituée de deux couches : la couche de machine virtuelle et de la couche de stockage.

1. La couche de machine virtuelle gère les données comme indiqué par le système d'exploitation invité et les applications en cours d'exécution sur la machine virtuelle. Il s'agit également de la couche qui exécute le logiciel de chiffrement (Bitlocker ou dmcrypt), qui chiffre en toute transparence les données sur les volumes avant l'écriture sur les disques.
2. La couche de stockage gère les objets Blob de pages et les disques attachés à la machine virtuelle. Elle n'a aucune connaissance des données écrites sur le disque, et si elles sont chiffrées ou non. Il s'agit de la couche où opère la fonctionnalité de sauvegarde de machine virtuelle.

![Comment le chiffrement Bitlocker et la sauvegarde de machines virtuelles Azure coexistent](./media/backup-azure-vms-encryption/how-it-works.png)

Le chiffrement total des données se produit de façon transparente et en toute simplicité dans la couche de la machine virtuelle. Les données écrites dans les objets Blob de pages associés à la machine virtuelle sont des données chiffrées. Lorsque [Azure Backup prend un instantané des disques de la machine virtuelle et transfère des données](backup-azure-vms-introduction.md#how-does-azure-back-up-virtual-machines), Azure Backup copie les données chiffrées présentes sur les objets Blob de pages.

## Composants de la solution

Cette solution est composée de nombreuses parties qui doivent être configurés et gérés correctement pour fonctionner :

| Fonction | Logiciel utilisé | Remarques supplémentaires |
| -------- | ------------- | ------- |
| Chiffrement | BitLocker ou dmcrypt | Étant donné que le chiffrement se produit dans une couche *différente* par rapport à Azure Backup, peu importe le logiciel de chiffrement utilisé. Cela dit, cette expérience a été validée avec CloudLink à l’aide de Bitlocker et dmcrypt uniquement.<br><br> Afin de chiffrer les données, une clé est nécessaire. La clé doit également être protégée pour garantir un accès autorisé aux données. |
| Gestion des clés | CloudLink SecureVM | La clé est essentielle pour chiffrer ou déchiffrer les données. Sans la clé appropriée, les données ne peuvent pas être récupérées. Cela devient *incroyablement* important avec :<br><li>les substitutions de clé<li>la rétention à long terme<br><br>Par exemple, il est possible que la clé utilisée pour sauvegarder des données il y a 7 ans ne soit pas la même clé utilisée aujourd'hui. Sans la clé d'il y a 7 ans, il est impossible d'utiliser les données restaurées à partir de ce moment-là.|
| Sauvegarde des données | Azure Backup | Utilisez Azure Backup pour sauvegarder vos machines virtuelles IaaS Azure à l'aide du [portail de gestion Azure](http://manage.windowsazure.com) ou de PowerShell. |
| Restauration des données | Azure Backup | Utilisez Azure Backup pour restaurer des disques ou une machine virtuelle entière à partir d'un point de récupération. Les données ne sont pas déchiffrées par Azure Backup dans le cadre de l'opération de restauration.|
| Déchiffrement | BitLocker ou dmcrypt | Pour lire des données sur un disque de données restauré ou une machine virtuelle restaurée, le logiciel a besoin de la clé du logiciel de gestion des clés. Sans la clé appropriée, les données ne peuvent pas être déchiffrées. |

> [AZURE.IMPORTANT]  La gestion des clés, y compris la substitution, ne fait pas partie d'Azure Backup. Cet aspect doit être géré indépendamment, mais il est très important pour l'opération de sauvegarde/restauration globale.

### Scénarios pris en charge


| &nbsp; | Archivage de sauvegarde | Coffre Recovery Services |
| :-- | :-- | :-- |
| Machines virtuelles Azure IaaS V1 | Oui | Non |
| Machines virtuelles Azure IaaS V2 | N/A | Non |


## CloudLink SecureVM

[CloudLink SecureVM](http://www.cloudlinktech.com/choose-your-cloud/microsoft-azure/) est une solution de chiffrement de machine virtuelle qui peut être utilisée pour sécuriser vos données de machine virtuelle IaaS Azure. CloudLink SecureVM est pris en charge pour une utilisation avec Azure Backup.

### Informations de support

- CloudLink SecureVM version 4.0 (version 21536.121 ou une version ultérieure)
- Azure PowerShell version 0.9.8 ou ultérieure

### Gestion des clés

Lorsque vous avez besoin de substituer ou de modifier des clés pour les machines virtuelles qui ont des sauvegardes existantes, vous devez vous assurer que les clés utilisées au moment de la sauvegarde sont disponibles. Une suggestion consiste à effectuer une sauvegarde du keystore ou de l'ensemble du système SecureVM.

### Documentation et ressources

- [Guide de déploiement - PDF](http://www.cloudlinktech.com/Azure/CL_SecureVM_4_0_DG_EMC_Azure_R2.pdf)
- [Déploiement et utilisation de SecureVM - vidéo](https://www.youtube.com/watch?v=8AIRe92UDNg)

<!---HONumber=AcomDC_0817_2016-->