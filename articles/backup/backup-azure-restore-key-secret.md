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
ms.date: 08/28/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f2db3449187d655248b13198b268841052570626
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Restaurer la clé et le secret du Key Vault pour les machines virtuelles chiffrées à l’aide d’Azure Backup
Cet article décrit l’utilisation de Sauvegarde de VM Azure pour restaurer des machines virtuelles Azure chiffrées si vos clé et secret n’existent pas dans le Key Vault. Vous pouvez également suivre cette procédure si vous souhaitez conserver une copie distincte de la clé (clé de chiffrement à clé) et du secret (clé de chiffrement BitLocker) pour la machine Virtuelle restaurée.

## <a name="prerequisites"></a>Composants requis
* **Sauvegarder les machines virtuelles chiffrées** : les machines virtuelles Azure chiffrées ont été sauvegardées à l’aide d’Azure Backup. Pour plus d’informations sur la sauvegarde de machines virtuelles Azure chiffrées, voir [Gérer la sauvegarde et la restauration de machines virtuelles Azure à l’aide de PowerShell](backup-azure-vms-automation.md).
* **Configurer Azure Key Vault**: assurez-vous que le Key Vault dans lequel les clés et secrets doivent être restaurés est déjà présent. Pour plus d’informations sur la gestion du Key Vault, voir [Prise en main d’Azure Key Vault](../key-vault/key-vault-get-started.md).
* **Restauration du disque** : vérifiez que vous avez déclenché des travaux de restauration pour restaurer les disques d’une machine virtuelle chiffrée à l’aide des [étapes PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm). Ceci parce que ce travail génère un fichier JSON dans votre compte de stockage contenant les clés et les secrets de la machine virtuelle chiffrée à restaurer.

## <a name="get-key-and-secret-from-azure-backup"></a>Obtenir la clé et le secret à partir d’Azure Backup

> [!NOTE]
> Une fois le disque restauré pour la machine virtuelle chiffrée, vérifiez que :
> 1. $details est alimenté avec les détails du travail du disque de restauration, comme indiqué dans [étapes PowerShell dans la section Restaurer les disques](backup-azure-vms-automation.md#restore-an-azure-vm)
> 2. La machine virtuelle doit être créée à partir de disques restaurées uniquement **après la restauration de la clé et du secret selon le coffre de clés**.
>
>

Interrogez les propriétés des disques restaurés pour obtenir les détails du travail.

```
PS C:\> $properties = $details.properties
PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
PS C:\> $containerName = $properties["Config Blob Container Name"]
PS C:\> $encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Définissez le contexte de stockage Azure et restaurez le fichier de configuration JSON contenant les détails de la clé et du secret pour la machine virtuelle chiffrée.

```
PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
PS C:\> $destination_path = 'C:\vmencryption_config.json'
PS C:\> Get-AzureStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
PS C:\> $encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Restaurer la clé
Une fois le fichier JSON généré dans le chemin d’accès de destination mentionné ci-dessus, générez un fichier blob clé à partir de JSON et alimentez-le pour restaurer l’applet de commande clé pour replacer la clé (KEK) dans le coffre de clés.

```
PS C:\> $keyDestination = 'C:\keyDetails.blob'
PS C:\> [io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Restaurer le secret
Utilisez le fichier JSON généré ci-dessus pour obtenir le nom et la valeur secrets et alimentez-le pour définir l’applet de commande secrète pour replacer le secret (BEK) dans le coffre de clés. **Utilisez ces applets de commande si votre machine virtuelle est chiffrée à l’aide de BEK et KEK.**

```
PS C:\> $secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Si votre machine virtuelle est **chiffrée à l’aide de BEK uniquement**, générez un fichier d’objets blob de secret à partir de l’objet JSON, et envoyez-le à l’applet de commande de restauration du secret pour remettre le secret (BEK) dans le coffre de clés.

```
PS C:\> $secretDestination = 'C:\secret.blob'
PS C:\> [io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
PS C:\> Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
```

> [!NOTE]
> 1. La valeur de $secretname peut être obtenue en faisant référence à la sortie de $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl et à l’aide du texte après secrets/ par exemple, l’URL de secret de sortie est https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 et nom secret est B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> 2. La valeur de la balise DiskEncryptionKeyFileName est identique au nom du secret.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Créer une machine virtuelle à partir du disque restauré
Si vous avez sauvegardé une machine virtuelle chiffrée à l’aide de la Sauvegarde de VM Azure, les applets de commande PowerShell mentionnées plus haut peuvent vous aider à restaurer la clé et le secret dans le coffre de clés. Après leur restauration, consultez l’article [Gérer la sauvegarde et la restauration de machines virtuelles Azure à l’aide de PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) pour créer des machines virtuelles chiffrées à partir du disque, de la clé et du secret restaurés.

## <a name="legacy-approach"></a>Approche héritée
La méthode ci-dessus fonctionne pour tous les points de récupération. Toutefois, l’ancienne méthode d’obtention des informations relatives à la clé et au secret à partir du point de récupération est toujours valide pour les points de récupération antérieurs au 11 juillet 2017, sur les machines virtuelles chiffrées à l’aide de BEK et KEK. Une fois le travail de restauration du disque terminé pour la machine virtuelle chiffrée à l’aide des [étapes PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm), assurez-vous que $rp est alimenté avec une valeur valide.

### <a name="restore-key"></a>Restaurer la clé
Utilisez les applets de commande suivantes pour obtenir des informations de clés (KEK) à partir du point de récupération et alimentez-les pour restaurer l’applet de commande clé pour la replacer dans le coffre de clés.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Restaurer le secret
Utilisez les applets de commande suivantes pour obtenir des informations de secrets (KEK) à partir du point de récupération et alimentez-les pour restaurer l’applet de commande secrète pour la replacer dans le coffre de clés.

```
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
> 1. La valeur de $secretname peut être obtenue en faisant référence à la sortie de $rp1.KeyAndSecretDetails.SecretUrl et en utilisant le texte après secrets/ par exemple l’URL du secret de sortie est https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 et le nom du secret est B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> 2. La valeur de la balise DiskEncryptionKeyFileName est identique au nom du secret.
> 3. Vous pouvez obtenir la valeur de DiskEncryptionKeyEncryptionKeyURL à partir du Key Vault après la restauration des clés, et en utilisant l’applet de commande [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx).
>
>

## <a name="next-steps"></a>Étapes suivantes
Après la restauration de la clé et du secret dans le coffre de clés, consultez l’article [Gérer la sauvegarde et la restauration de machines virtuelles Azure à l’aide de PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) pour créer des machines virtuelles chiffrées à partir du disque, de la clé et du secret restaurés.
