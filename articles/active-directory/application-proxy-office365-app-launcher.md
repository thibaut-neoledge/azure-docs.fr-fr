---
title: "Définir une page d’accueil personnalisée pour les applications publiées à l’aide du proxy d’application Azure AD | Microsoft Docs"
description: "Couvre les bases sur les connecteurs de proxy d’application Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 194367028c3c2c571dd8645a794f67a0c3a21d4c
ms.contentlocale: fr-fr
ms.lasthandoff: 07/08/2017

---

# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Définir une page d’accueil personnalisée pour les applications publiées à l’aide du proxy d’application Azure AD

Cet article explique comment configurer des applications pour diriger les utilisateurs vers une page d’accueil personnalisée lorsqu’ils accèdent aux applications à partir du volet d’accès Azure Active Directory (Azure AD) et du lanceur d’applications Office 365.

Lorsque les utilisateurs lancent l’application, ils sont dirigés par défaut vers l’URL du domaine racine de l’application publiée. La page d’arrivée est généralement définie comme URL de page d’accueil. Par exemple, pour l’application principale, http://ExpenseApp, l’URL publiée est la suivante : *https://expenseApp-contoso.msappproxy.net*. Par défaut, l’URL de page d’accueil est définie comme suit : *https://expenseApp-contoso.msappproxy.net*.

Avec le module Azure AD Powershell, vous pouvez définir des URL de page d’accueil personnalisées pour des instances lorsque vous souhaitez que les utilisateurs de l’application arrivent sur l’une de ses pages spécifique (par exemple, *https://expenseApp-contoso.msappproxy.net/login/login.aspx*).

>[!NOTE]
>Si vous octroyez aux utilisateurs un accès aux applications publiées, celles-ci sont affichées dans le [volet d’accès Azure AD](active-directory-saas-access-panel-introduction.md) et le [lanceur d’applications Office 365](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher).

## <a name="before-you-start"></a>Avant de commencer

Avant de définir l’URL de page d’accueil, n’oubliez pas les éléments suivants :

* Vérifiez que le chemin d’accès que vous spécifiez est un chemin d’accès de sous-domaine de l’URL du domaine racine.

  Si l’URL de domaine racine est https://apps.contoso.com/app1/ par exemple, l’URL de page d’accueil que vous configurez doit commencer par https://apps.contoso.com/app1/.

* Si vous apportez une modification à l’application publiée, ce changement peut réinitialiser la valeur de l’URL de page d’accueil. Ultérieurement, lorsque vous mettez à jour l’application, vous devez revérifier et, si nécessaire, mettre à jour l’URL de page d’accueil.

## <a name="change-the-home-page-in-the-azure-portal"></a>Changer la page d’accueil du portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Accédez à **Azure Active Directory** > **Inscriptions des applications** et choisissez votre application dans la liste. 
3. Dans les paramètres, sélectionnez **Propriétés**.
4. Mettez à jour le champ **URL de la page d’accueil** avec votre nouveau chemin d’accès. 
5. Sélectionnez **Enregistrer**.

## <a name="change-the-home-page-with-powershell"></a>Changer la page d’accueil à l’aide de PowerShell

### <a name="install-the-azure-ad-powershell-module"></a>Installer le module Azure AD PowerShell

Avant de définir une URL de page d’accueil personnalisée à l’aide de PowerShell, installez un package non standard du module Azure AD PowerShell. Vous pouvez télécharger le package à partir de [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureAD/1.1.23.0), qui utilise le point de terminaison de l’API Graph. 

Pour installer le package, procédez comme suit :

1. Ouvrez une fenêtre PowerShell standard, puis exécutez la commande suivante :

    ```
     Install-Module -Name AzureAD -RequiredVersion 1.1.23.0
    ```
    Si vous l’exécutez en tant que non-administrateur, utilisez l’option `-scope currentuser`.
2. Pendant l’installation, sélectionnez **Y** pour installer deux packages depuis Nuget.org. Les deux packages sont requis. 

### <a name="find-the-objectid-of-the-app"></a>Rechercher l’ObjectID de l’application

Obtenez l’ObjectID de l’application, puis recherchez l’application en fonction de sa page d’accueil.

1. Ouvrez PowerShell et importez le module Azure AD.

    ```
    Import-Module AzureAD
    ```

2. Connectez-vous au module Azure AD en tant qu’administrateur client.

    ```
    Connect-AzureAD
    ```
3. Recherchez l’application en fonction de son URL de page d’accueil. Vous pouvez trouver l’URL dans le portail en accédant à **Azure Active Directory** > **Applications d’entreprise** > **Toutes les applications**. Cet exemple utilise *sharepoint-iddemo*.

    ```
    Get-AzureADApplication | where { $_.Homepage -like “sharepoint-iddemo” } | fl DisplayName, Homepage, ObjectID
    ```
4. Vous devez obtenir un résultat semblable à celui qui est illustré ici. Copiez le GUID ObjectID à utiliser dans la section suivante.

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

### <a name="update-the-home-page-url"></a>Mettre à jour l’URL de la page d’accueil

Dans le même module PowerShell que celui utilisé à l’étape 1, procédez comme suit :

1. Vérifiez que vous avez la bonne application et remplacez *8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4* par le GUID (ObjectID) que vous avez copié à l’étape précédente.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

 Maintenant que vous avez vérifié l’application, vous êtes prêt à mettre à jour la page d’accueil comme suit.

2. Créez un objet d’application vide pour conserver les modifications que vous souhaitez apporter.  

 >[!NOTE]
 >Il s’agit uniquement d’une variable pour contenir les valeurs que vous souhaitez mettre à jour, donc rien n’a réellement été créé.

    ```
    $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
    ```

3. Définissez l’URL de page d’accueil sur la valeur souhaitée. La valeur doit correspondre à un chemin d’accès de sous-domaine de l’application publiée. Par exemple, si vous modifiez l’URL de page d’accueil *https://sharepoint-iddemo.msappproxy.net/* et la remplacez par *https://sharepoint-iddemo.msappproxy.net/hybrid/*, les utilisateurs de l’application accèderont directement à la page d’accueil personnalisée.

    ```
    $homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
    ```
4. Procédez à la mise à jour à l’aide du GUID (ObjectID) que vous avez copié dans « Étape 1 : rechercher l’ObjectID de l’application ».

    ```
    Set-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 -Homepage $homepage
    ```
5. Pour vérifier la réussite de la modification, redémarrez l’application.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>Les modifications que vous apportez à l’application peuvent réinitialiser l’URL de page d’accueil. Si cela se produit, répétez l’étape 2.

## <a name="next-steps"></a>Étapes suivantes

- [Activer l’accès distant pour SharePoint avec le proxy d’application Azure AD](application-proxy-enable-remote-access-sharepoint.md)
- [Activer le proxy d’application dans le portail Azure](active-directory-application-proxy-enable.md)

