---
title: "Accorder à plusieurs applications l&quot;autorisation d&quot;accéder à un coffre de clés Azure | Microsoft Docs"
description: "Découvrez comment accorder à plusieurs applications l&quot;autorisation d&quot;accéder à un coffre de clés"
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 785d4e40-fb7b-485a-8cbc-d9c8c87708e6
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: ambapat
translationtype: Human Translation
ms.sourcegitcommit: f1a03a0553bc1acd013918f527458603d08cb0f7
ms.openlocfilehash: 8d213455ad9c771d54c2334e4d31eb4f84c9ec15


---
# <a name="grant-permission-to-many-applications-to-access-a-key-vault"></a>Accorder à plusieurs applications l'autorisation d'accéder à un coffre de clés

## <a name="q-i-have-several-over-16-applications-that-need-to-access-a-key-vault-since-key-vault-only-allows-16-access-control-entries-how-can-i-achieve-that"></a>Q : Je possède plusieurs applications (plus de 16) qui nécessitent un accès à un coffre de clés. Étant donné que le coffre de clés Key Vault n'autorise que 16 entrées de contrôle d’accès, comment dois-je procéder ?

La stratégie de contrôle d’accès Key Vault prend uniquement en charge 16 entrées. Mais vous pouvez créer un groupe de sécurité Azure Active Directory. Ajoutez tous les principaux du service associés à ce groupe de sécurité et accordez à ce groupe de sécurité l'accès à Key Vault.

Voici les conditions préalables requises :
* [Installez le module Azure Active Directory V2 PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.0.30).
* [Installez Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Pour exécuter les commandes suivantes, vous avez besoin d’autorisations pour créer/modifier des groupes dans le locataire Azure Active Directory. Si vous ne disposez pas des autorisations, vous devrez peut-être contacter votre administrateur Active Directory Azure.

Exécutez à présent les commandes suivantes dans PowerShell.

```powershell
# Connect to Azure AD 
Connect-AzureAD 
 
# Create Azure Active Directory Security Group 
$aadGroup = New-AzureADGroup -Description "Contoso App Group" -DisplayName "ContosoAppGroup" -MailEnabled 0 -MailNickName none -SecurityEnabled 1 
 
# Find and add your applications (ServicePrincipal ObjectID) as members to this group 
$spn = Get-AzureADServicePrincipal –SearchString "ContosoApp1" 
Add-AzureADGroupMember –ObjectId $aadGroup.ObjectId -RefObjectId $spn.ObjectId 
 
# You can add several members to this group, in this fashion. 
 
# Set the Key Vault ACLs 
Set-AzureRmKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId -PermissionToKeys all –PermissionToSecrets all –PermissionToCertificates all 
 
# Of course you can adjust the permissions as required 
```

Si vous avez besoin d’accorder un autre jeu d’autorisations à un groupe d’applications, créez un groupe de sécurité Active Directory Azure distinct pour ces applications.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur comment [sécuriser votre coffre de clés](key-vault-secure-your-key-vault.md).



<!--HONumber=Dec16_HO2-->


