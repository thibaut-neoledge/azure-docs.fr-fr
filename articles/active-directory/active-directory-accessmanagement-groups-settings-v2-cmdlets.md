---
title: Applets de commande de la version préliminaire d’Azure Active Directory PowerShell pour la gestion des groupes dans Azure AD | Microsoft Docs
description: Cette page fournit des exemples PowerShell pour vous aider à gérer vos groupes dans Azure Active Directory
keywords: Azure AD, Azure Active Directory, PowerShell, Groupes, Gestion des groupes
services: active-directory
documentationcenter: ''
author: curtand
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: curtand

---
# <a name="azure-active-directory-preview-cmdlets-for-group-management"></a>Applets de commande de la version préliminaire d’Azure Active Directory pour la gestion de groupe
> [!div class="op_single_selector"]
> * [Portail Azure](active-directory-groups-create-azure-portal.md)
> * [Portail Azure Classic](active-directory-accessmanagement-manage-groups.md)
> * [PowerShell](active-directory-accessmanagement-groups-v2-cmdlets.md)
> 
> 

Le document suivant fournit des exemples montrant comment utiliser PowerShell pour gérer des groupes dans Azure Active Directory (Azure AD).  Il fournit également des informations sur la configuration du module de la version préliminaire d’Azure AD PowerShell. Vous devez tout d’abord [télécharger le module Azure AD PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=828627).

## <a name="installing-the-azure-ad-powershell-module"></a>Installation du module Azure AD PowerShell
Pour installer le module de la version préliminaire d’Azure AD PowerShell, utilisez les commandes suivantes :

    PS C:\Windows\system32> install-module azureadpreview

Pour vérifier que le module de la version préliminaire a été installé, utilisez la commande suivante :

    PS C:\Windows\system32> get-module azureadpreview

    ModuleType Version    Name                                ExportedCommands
    ---------- -------    ----                                ----------------
    Binary     1.1.146.0  azureadpreview                      {Add-AzureADAdministrati...}

Vous pouvez désormais utiliser les applets de commande dans le module. Pour obtenir une description complète des applets de commande dans le module de la version préliminaire d’Azure AD, consultez la [documentation de référence en ligne](https://msdn.microsoft.com/library/azure/mt757216.aspx).

## <a name="connecting-to-the-directory"></a>Connexion au répertoire
Avant de pouvoir gérer des groupes à l’aide des applets de commande de la version préliminaire d’Azure AD PowerShell, vous devez connecter votre session PowerShell au répertoire que vous souhaitez gérer. Pour ce faire, utilisez la commande suivante :

    PS C:\Windows\system32> Connect-AzureAD -Force

L’applet de commande vous demande de fournir les informations d’identification à utiliser pour accéder à votre répertoire. Dans cet exemple, nous utilisons karen@drumkit.onmicrosoft.com pour accéder au répertoire de démonstration. L’applet de commande renvoie une confirmation pour indiquer que la session a été correctement connectée à votre répertoire :

    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…

À présent, vous pouvez commencer à utiliser les applets de commande de la version préliminaire d’Azure AD pour gérer des groupes dans votre répertoire.

## <a name="retrieving-groups"></a>Récupération des groupes
Pour récupérer des groupes existants à partir de votre répertoire, vous pouvez utiliser l’applet de commande Get-AzureADGroups. Pour récupérer tous les groupes dans le répertoire, utilisez l’applet de commande sans paramètres :

    PS C:\Windows\system32> get-azureadgroup

L’applet de commande renvoie tous les groupes dans le répertoire connecté.

Vous pouvez utiliser le paramètre -objectID pour récupérer un groupe spécifique pour lequel vous spécifiez le paramètre objectID du groupe :

    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b

L’applet de commande renvoie à présent le groupe dont le paramètre objectID correspond à la valeur du paramètre que vous avez entré :

    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Vous pouvez rechercher un groupe spécifique en utilisant le paramètre -filter. Ce paramètre utilise une clause de filtre ODATA et renvoie tous les groupes qui correspondent au filtre, comme dans l’exemple suivant :

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Notez que les applets de commande Azure AD PowerShell implémentent la norme de requête OData. Vous trouverez plus d’informations [ici](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="creating-groups"></a>Création de groupes
Pour créer un nouveau groupe dans votre répertoire, utilisez l’applet de commande New-AzureADGroup. Cette applet de commande crée un nouveau groupe de sécurité appelé « Marketing » :

    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"

## <a name="updating-groups"></a>Mise à jour des groupes
Pour mettre à jour un groupe existant, utilisez l’applet de commande Set-AzureADGroup. Dans cet exemple, nous modifions la propriété DisplayName du groupe « Administrateurs Intune ». Tout d’abord, nous recherchons le groupe à l’aide de l’applet de commande Get-AzureADGroup et filtrons à l’aide de l’attribut DisplayName :

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Ensuite, nous attribuons à la propriété Description la nouvelle valeur « Administrateurs d’appareil Intune » :

    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"

À présent, si nous recherchons à nouveau le groupe, nous constatons que la propriété Description est mise à jour pour refléter la nouvelle valeur :

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

## <a name="deleting-groups"></a>Suppression de groupes
Pour supprimer des groupes de votre répertoire, utilisez l’applet de commande Remove-AzureADGroup comme suit :

    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b

## <a name="managing-members-of-groups"></a>Gestion des membres des groupes
Si vous devez ajouter de nouveaux membres à un groupe, utilisez l’applet de commande Add-AzureADGroupMember. Cette commande ajoute un membre au groupe Administrateurs Intune que nous avons utilisé dans l’exemple précédent :

    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Le paramètre -ObjectId est l’ObjectID du groupe auquel nous souhaitons ajouter un membre et le paramètre -RefObjectId est l’ObjectID de l’utilisateur que nous souhaitons ajouter au groupe en tant que membre.

Pour obtenir les membres existants d’un groupe, utilisez l’applet de commande Get-AzureADGroupMember, comme dans cet exemple :

    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                        72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                        8120cc36-64b4-4080-a9e8-23aa98e8b34f User

Pour supprimer le membre, que nous avons ajouté précédemment au groupe, utilisez l’applet de commande Remove-AzureADGroupMember, comme illustré ici :

    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Pour vérifier l’appartenance d’un utilisateur à un groupe, utilisez l’applet de commande Select-AzureADGroupIdsUserIsMemberOf. Cette applet de commande utilise comme paramètres l’ObjectId de l’utilisateur pour lequel vérifier l’appartenance au groupe et une liste de groupes pour lesquels vérifier les appartenances. La liste des groupes doit être spécifiée sous la forme d’une variable complexe de type « Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck », donc nous devons tout d’abord créer une variable de ce type :

    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck

Ensuite, nous fournissons des valeurs pour les groupIds à vérifier dans l’attribut « GroupIds » de cette variable complexe :

    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"

À présent, pour vérifier les appartenances aux groupes d’un utilisateur avec l’ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea sur les groupes dans $g, nous devons utiliser :

    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                               Value
    -------------                                                                                               -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}


La valeur retournée est une liste de groupes dont cet utilisateur est membre. Vous pouvez également appliquer cette méthode pour vérifier l’appartenance aux Contacts, Groupes ou Principaux du service pour une liste donnée de groupes, à l’aide de Select-AzureADGroupIdsContactIsMemberOf, Select-AzureADGroupIdsGroupIsMemberOf ou Select-AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="managing-owners-of-groups"></a>Gestion des propriétaires de groupes
Pour ajouter des propriétaires à un groupe, utilisez l’applet de commande Add-AzureADGroupOwner :

    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Le paramètre -ObjectId est l’ObjectID du groupe auquel nous souhaitons ajouter un propriétaire et le paramètre -RefObjectId est l’ObjectID de l’utilisateur que nous souhaitons ajouter au groupe en tant que propriétaire.

Pour récupérer les propriétaires d’un groupe, utilisez l’applet de commande Get-AzureADGroupOwner :

    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

L’applet de commande renvoie la liste des propriétaires du groupe spécifié :

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                        e831b3fd-77c9-49c7-9fca-de43e109ef67 User

Si vous souhaitez supprimer un propriétaire d’un groupe, utilisez Remove-AzureADGroupOwner :

    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67

## <a name="next-steps"></a>Étapes suivantes
Vous trouverez plus d’informations sur Azure Active Directory PowerShell dans la page dédiée aux [applets de commande Azure Active Directory](http://go.microsoft.com/fwlink/p/?LinkId=808260).

* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

<!--HONumber=Oct16_HO2-->


