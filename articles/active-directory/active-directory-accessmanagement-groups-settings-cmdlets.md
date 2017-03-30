---
title: "Configuration des paramètres de groupe avec les applets de commande Azure Active Directory | Microsoft Docs"
description: "Comment gérer les paramètres des groupes à l’aide des applets de commande Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 9f2090e6-3af4-4f07-bbb2-1d18dae89b73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 5c30f459d9fed71fede2da71306a9b48892566f3
ms.lasthandoff: 03/22/2017


---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Configuration des paramètres de groupe avec les applets de commande Azure Active Directory

Remarque : ce contenu s’applique uniquement aux groupes unifiés, également appelés groupes Office365.

Les paramètres des groupes Office365 sont configurés à l’aide d’un objet Settings (Paramètres) et d’un objet SettingsTemplate (Modèle Paramètres). Initialement, vous ne verrez aucun objet Settings dans votre répertoire. Cela signifie que votre répertoire est configuré avec les paramètres par défaut. Pour changer les paramètres par défaut, vous devrez créer un nouvel objet Settings à l’aide d’un modèle de paramètres. Les modèles de paramètres sont définis par Microsoft. Il existe différents modèles de paramètres. Pour configurer les paramètres de groupe pour votre annuaire, vous utiliserez le modèle nommé « Group.Unified ». Pour configurer les paramètres de groupe d’un groupe unique, vous utiliserez le modèle nommé « Group.Unified.Guest ». Ce modèle est utilisé pour gérer l’accès invité à un groupe. 

Les applets de commande font partie du module Azure Active Directory PowerShell V2. Pour plus d’informations sur ce module et pour savoir comment télécharger et installer le module sur votre ordinateur, reportez-vous à [Azure Active Directory PowerShell Version 2](https://docs.microsoft.com/en-us/powershell/azuread/).

## <a name="create-settings-at-the-directory-level"></a>Créer des paramètres au niveau du répertoire
Les étapes suivantes permettent de créer des paramètres au niveau du répertoire qui s’appliquent à tous les groupes unifiés du répertoire.

1. Dans les applets de commande DirectorySettings, vous devez spécifier l’ID du modèle SettingsTemplate que vous souhaitez utiliser. Si vous ne connaissez pas cet ID, cette applet de commande renvoie la liste de tous les modèles de paramètres :

    PS C:> Get-AzureADDirectorySettingTemplate

Cet appel d’applet de commande retourne tous les modèles disponibles :

```
Id                                   DisplayName         Description
--                                   -----------         -----------
62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Unified Group
16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
```
2. Pour ajouter une URL d’instructions d’utilisation, vous devez d’abord obtenir l’objet SettingsTemplate qui définit la valeur de l’URL d’instructions d’utilisation ; autrement dit, le modèle Group.Unified :

    $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b

3. Ensuite, créez un nouvel objet Settings basé sur ce modèle :

    $Setting = $template.CreateDirectorySetting()
    
4. Puis mettez à jour la valeur des instructions d’utilisation :

    $setting["UsageGuidelinesUrl"] = "<https://guideline.com>"
    
5. Enfin, appliquez les paramètres :

    New-AzureADDirectorySetting -DirectorySetting $settings

En cas de réussite, l’applet de commande renvoie l’ID du nouvel objet settings :


    Id                                   DisplayName TemplateId                           Values
    --                                   ----------- ----------                           ------
    c391b57d-5783-4c53-9236-cefb5c6ef323             62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...


Voici les paramètres définis dans l’objet SettingsTemplate Group.Unified.

| **Paramètre** | **Description** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Type : booléen<li>Par défaut : True |Indicateur spécifiant si la création de groupes unifiés est autorisée dans le répertoire. |
|  <ul><li>GroupCreationAllowedGroupId<li>Type : string<li>Valeur par défaut : “” |GUID du groupe de sécurité pour lequel les membres sont autorisés à créer des groupes unifiés même lorsque EnableGroupCreation == false. |
|  <ul><li>UsageGuidelinesUrl<li>Type : string<li>Valeur par défaut : “” |Lien vers les instructions d’utilisation du groupe. |
|  <ul><li>ClassificationDescriptions<li>Type : string<li>Valeur par défaut : “” | Liste séparée par des virgules des descriptions de classification. |
|  <ul><li>DefaultClassification<li>Type : string<li>Valeur par défaut : “” | Classification qui doit être utilisée en tant que classement par défaut pour un groupe si aucune classification n’a été spécifiée.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Type : string<li>Valeur par défaut : “” |Pas encore implémenté
|  <ul><li>AllowGuestsToBeGroupOwner<li>Type : booléen<li>Par défaut : False | Valeur booléenne indiquant si un utilisateur invité peut être ou non un propriétaire de groupes. |
|  <ul><li>AllowGuestsToAccessGroups<li>Type : booléen<li>Par défaut : True | Valeur booléenne indiquant si un utilisateur invité peut avoir ou non accès au contenu des groupes unifiés. |
|  <ul><li>GuestUsageGuidelinesUrl<li>Type : string<li>Valeur par défaut : “” | URL d’un lien vers les instructions d’utilisation de l’invité. |
|  <ul><li>AllowToAddGuests<li>Type : booléen<li>Par défaut : True | Valeur booléenne indiquant si l’utilisateur est autorisé ou non à ajouter des invités à ce répertoire.|
|  <ul><li>ClassificationList<li>Type : string<li>Valeur par défaut : “” |Liste des valeurs de classification valides séparées par des virgules pouvant être appliquées à des groupes unifiés. |
|  <ul><li>EnableGroupCreation<li>Type : booléen<li>Par défaut : True | Valeur booléenne indiquant si les utilisateurs non-administrateurs peuvent ou non créer des groupes unifiés. |
'

## <a name="read-settings-at-the-directory-level"></a>Lire les paramètres au niveau du répertoire
Les étapes suivantes permettent de lire les paramètres au niveau du répertoire qui s’appliquent à tous les groupes Office du répertoire.

1. Lisez tous les paramètres du répertoire existant :

    `Get-AzureADDirectorySetting -All $True'

Cette applet de commande renvoie une liste de tous les paramètres du répertoire : ' Id                                   DisplayName   TemplateId                           Values
--                                   -----------   ----------                           ------
c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...`


2. Lisez tous les paramètres d’un groupe spécifique :

    `Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups`

3. Lisez toutes les valeurs de paramètres de répertoire d’un objet settings de répertoire spécifique, à l’aide du GUID de l’ID de paramètres :

    `(Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values'

Cette applet de commande renvoie les noms et valeurs dans cet objet settings pour ce groupe spécifique : ' Name                          Value
----                          -----
ClassificationDescriptions DefaultClassification PrefixSuffixNamingRequirement AllowGuestsToBeGroupOwner     False AllowGuestsToAccessGroups     True GuestUsageGuidelinesUrl GroupCreationAllowedGroupId AllowToAddGuests              True UsageGuidelinesUrl            <https://guideline.com> ClassificationList EnableGroupCreation           True` '

## <a name="update-settings-for-a-specific-group"></a>Mettre à jour les paramètres d’un groupe spécifique

1. Recherchez le modèle de paramètres nommé « Groups.Unified.Guest »

    Get-AzureADDirectorySettingTemplate

    Id                                   DisplayName            Description
    --                                   -----------            -----------
    62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Paramètres d’un groupe unifié spécifique  4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...  898f1161-d651-43d1-805c-3b0b388a9fc2 Paramètres de stratégie personnalisés ...  5cf42378-d67d-4f36-ba46-e8b86229381d paramètres de règle de mot de passe ...

2. Récupérez l’objet de modèle pour le modèle Groups.Unified.Guest :

    $Template = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9

3. Créez un nouvel objet settings à partir du modèle :


    $Setting = $Template.CreateDirectorySetting()


4. Définissez le paramètre sur la valeur requise :


    $Setting["AllowToAddGuests"]=$False


6. Créez un nouveau paramètre pour le groupe requis dans le répertoire :


    New-AzureADObjectSetting -TargetType Groups -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -DirectorySetting $Setting


    Id                                   DisplayName TemplateId                           Values
    --                                   ----------- ----------                           ------
    25651479-a26e-4181-afce-ce24111b2cb5             08d542b9-071f-4e16-94b0-74abb372e3d9 {class SettingValue {...


## <a name="update-settings-at-the-directory-level"></a>Mettre à jour des paramètres au niveau du répertoire

Les étapes suivantes mettent à jour les paramètres au niveau du répertoire qui s’appliquent à tous les groupes unifiés du répertoire. Ces exemples supposent qu’un objet Settings existe déjà dans votre répertoire.

1. Recherchez l’objet Settings existant :

    Get-AzureADDirectorySetting | Where-object -Property Displayname -Value "Group.Unified" -EQ`

    Id                                   DisplayName   TemplateId                           Values
    --                                   -----------   ----------                           ------
    c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...

    $setting = Get-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323`

3. Mettez à jour la valeur :

    $Setting["AllowToAddGuests"] = "false"`

4. Mettez à jour le paramètre :

    Set-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323 -DirectorySetting $Setting`

## <a name="remove-settings-at-the-directory-level"></a>Supprimer des paramètres au niveau du répertoire
Les étapes suivantes permettent de supprimer les paramètres au niveau du répertoire qui s’appliquent à tous les groupes Office du répertoire.

    Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c`

## <a name="cmdlet-syntax-reference"></a>Informations de référence sur la syntaxe des applets de commande
Vous trouverez plus d’informations sur Azure Active Directory PowerShell dans la page dédiée aux [applets de commande Azure Active Directory](https://docs.microsoft.com/en-us/powershell/azuread/).

## <a name="additional-reading"></a>Documentation supplémentaire

* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

