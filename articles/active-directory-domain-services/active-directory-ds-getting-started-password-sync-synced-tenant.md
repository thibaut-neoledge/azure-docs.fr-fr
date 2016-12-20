---
title: 'Services de domaine Azure AD : Activer la synchronisation de mot de passe | Microsoft Docs'
description: Prise en main des services de domaine Azure Active Directory
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 8731f2b2-661c-4f3d-adba-2c9e06344537
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/20/2016
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 9c43a831768684c6458d5f62557c8a06b2c5ca6b


---
# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Activer la synchronisation de mot de passe pour les services de domaine Azure AD
Dans les tâches précédentes, vous avez activé Services de domaine Azure AD pour votre client Azure AD. La tâche suivante consiste à activer la synchronisation des mots de passe pour les services de domaine Azure AD. Une fois la synchronisation des informations d’identification configurée, les utilisateurs peuvent se connecter au domaine géré à l’aide des informations d’identification d’entreprise.

Les étapes sont différentes selon que votre organisation a un client Azure AD uniquement dans le cloud ou est paramétrée de manière à se synchroniser avec votre annuaire local à l’aide d’Azure AD Connect.

<br>

> [!div class="op_single_selector"]
> * [Client Azure AD dans le cloud uniquement](active-directory-ds-getting-started-password-sync.md)
> * [Client Azure AD synchronisé](active-directory-ds-getting-started-password-sync-synced-tenant.md)
> 
> 

<br>

## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-synced-azure-ad-tenant"></a>Tâche 5 : activer la synchronisation de mot de passe pour les services de domaine AAD pour un client Azure AD synchronisé
Un client Azure AD connecté est défini pour se synchroniser avec le répertoire local de votre organisation à l’aide d’Azure AD Connect. Azure AD Connect ne synchronise pas les hachages des informations d’identification NTLM et Kerberos avec Azure AD par défaut. Pour utiliser les Services de domaine Azure AD, vous devez configurer Azure AD Connect pour synchroniser les hachages d’informations d’identification requis pour l’authentification NTLM et Kerberos. Les étapes suivantes permettent la synchronisation des hachages d’informations d’identification avec votre client Azure AD.

### <a name="install-or-update-azure-ad-connect"></a>Installer ou mettre à jour Azure AD Connect
Installer la dernière version recommandée d’Azure AD Connect sur un ordinateur joint à un domaine. Si une instance d’Azure AD Connect est déjà configurée, vous devez la mettre à jour pour utiliser la dernière version d’Azure AD Connect. Veillez à utiliser la dernière version d’Azure AD Connect afin d’éviter les problèmes/bogues connus déjà corrigés.

**[Télécharger Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

Version minimale recommandée : **1.1.281.0** - publiée le 7 septembre 2016.

> [!WARNING]
> Vous DEVEZ installer la dernière version recommandée d’Azure AD Connect pour que les informations d’identification de mot de passe héritées (nécessaires pour l’authentification NTLM et Kerberos) puissent être synchronisées avec votre client Azure AD. Cette fonctionnalité n’est pas disponible dans les versions antérieures d’Azure AD Connect ou avec l’outil DirSync hérité.
> 
> 

Les instructions d’installation d’Azure AD Connect sont disponibles dans l’article suivant : [Prise en main d’Azure AD Connect](../active-directory/active-directory-aadconnect.md)

### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>Activez la synchronisation des hachages des informations d’identification NTLM et Kerberos avec Azure AD
Exécutez le script PowerShell suivant sur chaque forêt AD, pour forcer la synchronisation de mot de passe complète, et permettre à tous les hachages d’informations d’identification des utilisateurs locaux de se synchroniser avec votre client Azure AD. Ce script permet les hachages d’informations d’identification requis pour que l’authentification NTLM/Kerberos soit synchronisée avec votre client Azure AD.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

En fonction de la taille de votre répertoire (nombre d’utilisateurs, de groupes, etc.), la synchronisation des hachages d’informations d’identification avec Azure AD peut prendre du temps. Les mots de passe seront utilisables sur le domaine géré de Services de domaine Azure AD peu après la synchronisation des hachages d'informations d'identification avec Azure AD.

<br>

## <a name="related-content"></a>Contenu connexe
* [Activer la synchronisation de mot de passe pour les services de domaine AAD pour un répertoire Azure AD uniquement dans le cloud](active-directory-ds-getting-started-password-sync.md)
* [Administrer un domaine géré par les services de domaine Azure Active Directory](active-directory-ds-admin-guide-administer-domain.md)
* [Joindre une machine virtuelle Windows à un domaine géré par les services de domaine Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Joindre une machine virtuelle Linux Red Hat Enterprise à un domaine géré par les services de domaine Azure AD](active-directory-ds-admin-guide-join-rhel-linux-vm.md)




<!--HONumber=Dec16_HO2-->


