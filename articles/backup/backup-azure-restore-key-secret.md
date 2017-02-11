---
title: "Restaurer la clé et le secret du Key Vault pour les machines virtuelles chiffrées à l’aide d’Azure Backup | Microsoft Docs"
description: "Découvrez comment restaurer la clé et la clé secrète du Key Vault dans Azure Backup à l’aide de PowerShell"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 45214083-d5fc-4eb3-a367-0239dc59e0f6
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: pajosh
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0c5b2969ddc943b2b15826003f5a9e686e84e1c4


---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Restaurer la clé et le secret du Key Vault pour les machines virtuelles chiffrées à l’aide d’Azure Backup
Cet article décrit l’utilisation de Sauvegarde de VM Azure pour restaurer des machines virtuelles Azure chiffrées si vos clé et secret n’existent pas dans le Key Vault. Vous pouvez également suivre cette procédure si vous souhaitez conserver une copie distincte de la clé (clé de chiffrement à clé) et du secret (clé de chiffrement BitLocker) pour la machine Virtuelle restaurée.

## <a name="pre-requisites"></a>Conditions préalables
1. **Sauvegarder les machines virtuelles chiffrées** : les machines virtuelles Azure chiffrées ont été sauvegardées à l’aide d’Azure Backup. Pour plus d’informations sur la sauvegarde de machines virtuelles Azure chiffrées, voir [Gérer la sauvegarde et la restauration de machines virtuelles Azure à l’aide de PowerShell](backup-azure-vms-automation.md).
2. **Configurer Azure Key Vault**: assurez-vous que le Key Vault dans lequel les clés et secrets doivent être restaurés est déjà présent. Pour plus d’informations sur la gestion du Key Vault, voir [Prise en main d’Azure Key Vault](../key-vault/key-vault-get-started.md).

## <a name="setup-recovery-services-vault"></a>Configurer le Coffre Recovery Services
Procédez comme suit pour vous connecter à PowerShell et définir le contexte du coffre Recovery Services.

### <a name="log-in-to-azure-powershell"></a>Connexion à Azure PowerShell
Connectez-vous à votre compte Azure à l’aide de l’applet de commande suivante.

```
PS C:\> Login-AzureRmAccount
```

### <a name="set-recovery-services-vault-context"></a>Définir le contexte du coffre Recovery Services
Une fois connecté, utilisez l’applet de commande suivante pour obtenir la liste de vos abonnements disponibles.

```
PS C:\> Get-AzureRmSubscription
```

Sélectionnez l’abonnement dans lequel des ressources sont disponibles.

```
PS C:\> Set-AzureRmContext -SubscriptionId "<subscription-id>"
```

Définissez le contexte du coffre à l’aide du coffre Recovery Services où la sauvegarde a été activée pour les machines virtuelles chiffrées.

```
PS C:\> Get-AzureRmRecoveryServicesVault -ResourceGroupName "<rg-name>" -Name "<rs-vault-name>" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="get-recovery-point"></a>Obtenir un point de récupération
Sélectionnez le conteneur dans le coffre qui représente la machine virtuelle chiffrée.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "<vm-name>"
```

À l’aide de ce conteneur, revenez à l’élément pour la machine virtuelle correspondante.

```
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
```

Obtenez un tableau de points de récupération pour l’élément de sauvegarde sélectionné dans la variable rp.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

## <a name="restore-encrypted-virtual-machine"></a>Restaurer la machine virtuelle chiffrée
Procédez comme suit pour restaurer les la machine virtuelle chiffrée, sa clé et son secret.

### <a name="restore-key"></a>Restaurer la clé
Le tableau $rp ci-dessus est trié dans l’ordre chronologique inverse, le point de récupération le plus récent étant associé à l’index 0. Par exemple : $rp[0] sélectionne le dernier point de récupération.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation "C:\Users\downloads"
```

> [!NOTE]
> Après exécution de cette applet de commande, un fichier blob est généré dans le dossier spécifié sur la machine sur laquelle il est exécuté. Ce fichier blob représente la clé chiffrée sous forme chiffrée.
> 
> 

Restaurez la clé dans le Key Vault à l’aide de l’applet de commande suivante. 

```
PS C:\> Restore-AzureKeyVaultKey -VaultName "contosokeyvault" -InputFile "C:\Users\downloads\key.blob"
```

### <a name="restore-secret"></a>Restaurer le secret
Restaurez les données du secret à partir du point de récupération obtenu ci-dessus.

```
PS C:\> $rp1.KeyAndSecretDetails.SecretUrl

https://contosokeyvault.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/20aaae9eaa99996d89d99a29990d999a
```

> [!NOTE]
> Le texte avant vault.azure.net représente le nom du Key Vault d’origine. Le texte situé après secrets/ représente le nom du secret. 
> 
> 

Obtenez le nom et la valeur du secret à partir de la sortie de l’applet de commande exécutée ci-dessus, si vous souhaitez utiliser le même nom de secret. Autrement, $secretname ci-dessous doit être mis à jour pour utiliser le nouveau nom de secret. 

```
PS C:\> $secretname = "B3284AAA-DAAA-4AAA-B393-60CAA848AAAA"
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
```

Définissez des balises pour le secret si la machine Virtuelle doit également être restaurée. Pour la balise DiskEncryptionKeyFileName, la valeur doit contenir le nom du secret que vous prévoyez d’utiliser. 

```
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://contosokeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
```

> [!NOTE]
> La valeur de DiskEncryptionKeyFileName est identique au nom de secret obtenu ci-dessus. Vous pouvez obtenir la valeur de DiskEncryptionKeyEncryptionKeyURL à partir du Key Vault après la restauration des clés, et en utilisant l’applet de commande [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx).    
> 
> 

Redéfinir le secret sur le Key Vault

```
PS C:\> Set-AzureKeyVaultSecret -VaultName "contosokeyvault" -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  "Wrapped BEK"
```

### <a name="restore-virtual-machine"></a>Restaurer une machine virtuelle
Les applets de commande PowerShell ci-dessus vous aident à restaurer la clé et le secret dans le Key Vault si vous avez sauvegardé une machine virtuelle chiffrée à l’aide de Sauvegarde de VM Azure. Après leur restauration, pour restaurer les machines virtuelles chiffrées, voir [Gérer la sauvegarde et la restauration de machines virtuelles Azure à l’aide de PowerShell](backup-azure-vms-automation.md).




<!--HONumber=Nov16_HO3-->


