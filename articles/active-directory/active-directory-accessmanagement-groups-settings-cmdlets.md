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
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: c18ef38661e31e16114b88bdfad36320776ef12b
ms.lasthandoff: 12/28/2016


---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Configuration des paramètres de groupe avec les applets de commande Azure Active Directory
Dans votre répertoire, vous pouvez configurer les paramètres suivants des groupes unifiés :

1. Classifications (Classifications) : la liste de classifications séparées par une virgule pouvant être définies par les utilisateurs sur un groupe. Par exemple, il peut s’agir de « Classique », « Secret » et « Top Secret ».
2. Usage Guidelines URL (URL d’instructions d’utilisation) : URL dirigeant les utilisateurs vers les conditions d’utilisation des groupes unifiés, telles que définies par votre organisation. Cette URL s’affichera dans l’interface utilisateur où les utilisateurs utilisent des groupes.
3. Group creation enabled (Création de groupe activée) : spécifie si aucun, certains ou tous les utilisateurs sont autorisés à créer des groupes unifiés. Lorsque ce paramètre est activé, tous les utilisateurs peuvent créer des groupes. Lorsque ce paramètre est désactivé, aucun utilisateur ne peut créer des groupes. S’il est désactivé, vous pouvez aussi spécifier un groupe de sécurité dont les utilisateurs sont toujours autorisés à créer des groupes.

Ces paramètres sont configurés à l’aide d’objets Settings (Paramètres) et SettingsTemplate (Modèle Paramètres). Initialement, vous ne verrez aucun objet Settings dans votre répertoire. Cela signifie que votre répertoire est configuré avec les paramètres par défaut. Pour changer les paramètres par défaut, vous allez créer un nouvel objet Settings à l’aide d’un modèle de paramètres. Les modèles de paramètres sont définis par Microsoft.

Vous pouvez télécharger le module contenant les applets de commande utilisées pour ces opérations sur le [site Microsoft Connect](http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185).

## <a name="create-settings-at-the-directory-level"></a>Créer des paramètres au niveau du répertoire
Les étapes suivantes permettent de créer des paramètres au niveau du répertoire qui s’appliquent à tous les groupes Office du répertoire.

1. Si vous ne savez pas quel objet SettingsTemplate utiliser, cette applet de commande renvoie la liste des modèles de paramètres :

    `Get-MsolAllSettingTemplate`

    ![Liste des modèles de paramètres](./media/active-directory-accessmanagement-groups-settings-cmdlets/list-of-templates.png)
2. Pour ajouter une URL d’instructions d’utilisation, vous devez d’abord obtenir l’objet SettingsTemplate qui définit la valeur de l’URL d’instructions d’utilisation ; autrement dit, le modèle Group.Unified :

    `$template = Get-MsolSettingTemplate –TemplateId 62375ab9-6b52-47ed-826b-58e47e0e304b`
3. Ensuite, créez un nouvel objet Settings basé sur ce modèle :

    `$setting = $template.CreateSettingsObject()`
4. Puis mettez à jour la valeur des instructions d’utilisation :

    `$setting["UsageGuidelinesUrl"] = "<https://guideline.com>"`
5. Enfin, appliquez les paramètres :

    `New-MsolSettings –SettingsObject $setting`

    ![Ajouter une URL d’instructions d’utilisation](./media/active-directory-accessmanagement-groups-settings-cmdlets/add-usage-guideline-url.png)

Voici les paramètres définis dans l’objet SettingsTemplate Group.Unified.

| **Paramètre** | **Description** |
| --- | --- |
|  <ul><li>ClassificationList<li>Type : string<li>Valeur par défaut : “” |Liste des valeurs de classification valides séparées par des virgules pouvant être appliquées à des groupes unifiés. |
|  <ul><li>EnableGroupCreation<li>Type : booléen<li> Par défaut : True |Indicateur spécifiant si la création de groupes unifiés est autorisée dans le répertoire. |
|  <ul><li>GroupCreationAllowedGroupId<li>Type : string<li>Valeur par défaut : “” |GUID du groupe de sécurité autorisé à créer des groupes unifiés même lorsque EnableGroupCreation == false. |
|  <ul><li>UsageGuidelinesUrl<li>Type : string<li>Valeur par défaut : “” |Lien vers les instructions d’utilisation du groupe. |

## <a name="read-settings-at-the-directory-level"></a>Lire les paramètres au niveau du répertoire
Les étapes suivantes permettent de lire les paramètres au niveau du répertoire qui s’appliquent à tous les groupes Office du répertoire.

1. Lisez tous les paramètres du répertoire existant :

    `Get-MsolAllSettings`
2. Lisez tous les paramètres d’un groupe spécifique :

    `Get-MsolAllSettings -TargetType Groups -TargetObjectId <groupObjectId>`
3. Lisez les paramètres d’un répertoire spécifique, à l’aide du GUID SettingId :

    `Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

    ![GUID de l’ID de paramètres](./media/active-directory-accessmanagement-groups-settings-cmdlets/settings-id-guid.png)

## <a name="update-settings-at-the-directory-level"></a>Mettre à jour des paramètres au niveau du répertoire
Les étapes suivantes permettent de mettre à jour les paramètres au niveau du répertoire qui s’appliquent à tous les groupes Office du répertoire.

1. Obtenez l’objet Settings existant :

    `$setting = Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`
2. Obtenez la valeur que vous souhaitez mettre à jour :

    `$value = $Setting.GetSettingsValue()`
3. Mettez à jour la valeur :

    `$value["AllowToAddGuests"] = "false"`
4. Mettez à jour le paramètre :

    `Set-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c –SettingsValue $value`

## <a name="remove-settings-at-the-directory-level"></a>Supprimer des paramètres au niveau du répertoire
Les étapes suivantes permettent de supprimer les paramètres au niveau du répertoire qui s’appliquent à tous les groupes Office du répertoire.

    `Remove-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

## <a name="cmdlet-syntax-reference"></a>Informations de référence sur la syntaxe des applets de commande
Vous trouverez plus d’informations sur Azure Active Directory PowerShell dans la page dédiée aux [applets de commande Azure Active Directory](http://go.microsoft.com/fwlink/p/?LinkId=808260).

## <a name="settingstemplate-object-reference-groupunified-settingstemplate-object"></a>Référence d’objet SettingsTemplate (OBJE SettingsTemplate Group.Unified)
* "name": "EnableGroupCreation", "type": "System.Boolean", "defaultValue": "true", "description": "Indicateur booléen spécifiant si la fonctionnalité de création de groupes unifiés est activée."
* "name": "GroupCreationAllowedGroupId", "type": "System.Guid", "defaultValue": "", "description": "GUID du groupe de sécurité qui se trouve dans la liste approuvée pour créer des groupes unifiés."
* "name": "ClassificationList", "type": "System.String", "defaultValue": "", "description": "Liste de valeurs de classification valides séparées par une virgule pouvant être appliquées à des groupes unifiés."
* "name": "UsageGuidelinesUrl", "type": "System.String", "defaultValue": "", "description": "Lien vers les instructions d’utilisation du groupe."

| name | type | defaultValue | Description |
| --- | --- | --- | --- |
| "EnableGroupCreation" |"System.Boolean" |"true" |"Indicateur booléen spécifiant si la fonctionnalité de création de groupes unifiés est activée." |
| "GroupCreationAllowedGroupId" |"System.Guid" |"" |"GUID du groupe de sécurité qui se trouve dans la liste approuvée pour créer des groupes unifiés." |
| "ClassificationList" |"System.String" |"" |"Liste de valeurs de classification valides séparées par des virgules pouvant être appliquées à des groupes unifiés." |
| "UsageGuidelinesUrl" |"System.String" |"" |"Lien vers les instructions d’utilisation du groupe." |

## <a name="next-steps"></a>Étapes suivantes
Vous trouverez plus d’informations sur Azure Active Directory PowerShell dans la page dédiée aux [applets de commande Azure Active Directory](http://go.microsoft.com/fwlink/p/?LinkId=808260).

Pour des instructions supplémentaires fournies par le responsable de programme Microsoft Rob de Jong, consultez le site [Rob’s Groups Blog](http://robsgroupsblog.com/blog/configuring-settings-for-office-365-groups-in-azure-ad)(Blog de Rob dédié aux groupes).

* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

