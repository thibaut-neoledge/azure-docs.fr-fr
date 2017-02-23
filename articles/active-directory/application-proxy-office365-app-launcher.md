---
title: "Définition d’une page d’accueil personnalisée pour votre application publiée à l’aide du proxy d’application Azure AD | Microsoft Docs"
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
ms.date: 01/25/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: d543849ccdc3cd93845756954b063ae169b066d5
ms.openlocfilehash: 9f642b03b6c10f6312b2c31bbc810cb6701352e3


---

# <a name="set-a-custom-home-page-for-your-published-application-using-azure-ad-app-proxy"></a>Définition d’une page d’accueil personnalisée pour votre application publiée à l’aide du proxy d’application Azure AD

Cet article explique comment vous pouvez configurer votre application pour diriger les utilisateurs vers une page d’accueil personnalisée lorsque les utilisateurs accèdent à votre application à partir du panneau d’accès Azure AD et le lanceur d’applications Office 365.

>[!NOTE]
>Le proxy d’application est une fonctionnalité qui n’est disponible que si vous effectuez une mise à niveau vers l’édition Premium ou De base d’Azure Active Directory. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).
> 
 
Avec le module Azure AD Powershell, vous pouvez définir des URL de page d’accueil personnalisées pour ces instances lorsque vous souhaitez que les utilisateurs arrivent sur une page spécifique dans votre application ; par exemple https://expenseApp-contoso.msappproxy.net/login/login.aspx.

>[!NOTE]
>Lorsque vous fournissez aux utilisateurs l’accès à vos applications publiées, nous affichons vos applications dans le [panneau d’accès Azure AD](active-directory-saas-access-panel-introduction.md) et le [lanceur d’applications Office 365](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher). 
>

Lorsque les utilisateurs lancent vos applications, ils sont dirigés par défaut vers l’URL du domaine racine de l’application publiée. La page d’arrivée est généralement définie sur l’URL de la page d’accueil. Par exemple, pour cette application principale, http://ExpenseApp, elle est publiée en tant que https://expenseApp-contoso.msappproxy.net. Par défaut, l’URL de la page d’accueil est définie sur https://expenseApp-contoso.msappproxy.net.

## <a name="determine-the-home-page-url"></a>Déterminer l’URL de la page d’accueil

Il y a plusieurs points que vous devez connaître avant de définir l’URL de la page d’accueil :

* Vous devez vous assurer que le chemin d’accès que vous spécifiez est un chemin d’accès de sous-domaine de l’URL du domaine racine.

 Par exemple, si votre application publiée est accessible à partir d’une URL de page d’accueil https://intranet-contoso.msappproxy.net/, l’URL de page d’accueil que vous configurez doit commencer par https://intranet-contoso.msappproxy.net/. 
 
* Si l’URL de la page d’accueil est https://apps.contoso.com/app1/, l’URL de la page d’accueil doit commencer par https://apps.contoso.com/app1/.

* Si vous apportez une modification à l’application publiée, cela peut réinitialiser la valeur de l’URL de la page d’accueil. Par conséquent, lorsque vous décidez de mettre à jour votre application, vous devez revérifier et éventuellement mettre à jour l’URL de la page d’accueil.


Dans la section suivante, vous découvrirez comment configurer une URL de page d’accueil personnalisée pour les applications publiées. 

## <a name="install-the-azure-ad-powershell-module"></a>Installer le module Azure AD PowerShell

Avant de définir une URL de page d’accueil personnalisée à l’aide de PowerShell, vous devez d’abord installer un package non standard du module Azure AD PowerShell.  Vous pouvez télécharger ce package à partir de [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureAD/1.1.23.0), qui utilise le point de terminaison de l’API Graph. 

**Pour installer le package en utilisant Powershell :**

1. Ouvrez PowerShell standard.
2. Exécutez la commande suivante :

```
 Install-Module -Name AzureAD -RequiredVersion 1.1.23.0
 ```
 Si vous l’utilisez en tant que non administrateur, vous devez utiliser l’option _-scope currentuser_.
3. Pendant l’installation, sélectionnez « Y » pour installer les deux packages depuis Nuget.org.  Les deux sont nécessaires pour utiliser le package. 

##<a name="set-a-custom-home-page-url-using-the-azure-ad-powershell-module"></a>Définition d’une URL de page d’accueil personnalisée à l’aide du module Azure AD PowerShell

Maintenant que vous avez le module Azure AD PowerShell installé, vous êtes prêt à définir l’URL de la page d’accueil en suivant deux étapes simples.

1. Recherchez l’application que vous souhaitez mettre à jour.
2. Mettez à jour l’URL de la page d’accueil de l’application.

###<a name="step-1--find-the-objectid-of-the-application"></a>Étape 1 : trouver l’ObjectID de l’application

Vous devez tout d’abord obtenir l’ObjectID de l’application, puis rechercher l’application par page d’accueil.

1. Ouvrez PowerShell.
2. Importez le module Azure AD.
  
 ```
 Import-Module AzureAD
 ```
3. Connectez-vous au module Azure AD.  Utilisez l’applet de commande ci-dessous et suivez les instructions à l’écran. Assurez-vous de vous connecter avec le bon identifiant de client.
 
 ```
 Connect-AzureAD
 ```
4. L’applet de commande ci-dessous recherche les applications basées sur la page d’accueil contenant _sharepoint-iddemo_. Il s’agit de l’application que vous souhaitez modifier. Vous devez remplacer cette valeur par la valeur adéquate pour votre application.
  
 ```
 Get-AzureADApplications | where { $_.Homepage -like “*sharepoint-iddemo*” } | fl DisplayName, Homepage, ObjectID
 ```
5. Vous devriez voir un résultat semblable à la réponse ci-dessous. Le GUID (la valeur ObjectID ci-dessous) est l’élément que vous devez copier.
 
 ```
 DisplayName : SharePoint
 Homepage    : https://sharepoint-iddemo.msappproxy.net/
 ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
 ```
6. Copiez la valeur GUID (ObjectID). Vous aurez besoin de cela dans l’étape suivante.


###<a name="step-2--update-the-homepage-url"></a>Étape 2 : Mise à jour de l’URL de la page d’accueil

Vous utilisez le même module PowerShell pour mettre à jour l’URL de la page d’accueil comme vous l’avez fait pour trouver l’ID d’application. Après la connexion à PowerShell, procédez comme suit :

1. Vérifiez que vous avez la bonne application et remplacez _8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4_ par la valeur d’ObjectID de votre application que vous avez copiée à l’étape 1 ci-dessus. 
  
 ```
 Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
 ```
 
 Maintenant que vous avez confirmé l’application, vous êtes prêt à mettre à jour la page d’accueil comme suit.
 
2. Créez un objet d’application vide pour conserver les modifications souhaitées. Il s’agit uniquement d’une variable pour contenir les valeurs que vous souhaitez mettre à jour, donc rien n’a réellement été créé.
  
 ```
 $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
 ```
3. Définissez la page d’accueil avec la valeur que vous souhaitez. N’oubliez pas qu’il doit s’agir d’un chemin d’accès de sous-domaine de l’application publiée. Par exemple, si vous modifiez la page d’accueil de _https://sharepoint-iddemo.msappproxy.net/_ à _https://sharepoint-iddemo.msappproxy.net/hybrid/_, vos utilisateurs accèderont directement à la page d’accueil personnalisée.
  
 ```
 $appnew.Homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
 ```
4. La dernière chose à faire est d’effectuer la mise à jour. Pensez à utiliser le GUID que vous avez copié à l’étape 1 ci-dessus.
  
 ```
 Set-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 - Application $appnew
 ```
5. Vous devez maintenant confirmer la page d’accueil personnalisée en démarrant l’application, pour vérifier que la modification a réussi.
  
 ```
 Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
 ```

>[!NOTE]
>N’oubliez pas que les modifications que vous apportez à votre application peuvent réinitialiser l’URL de la page d’accueil. Dans ce cas, vous devrez également répéter cette procédure.

##<a name="next-steps"></a>Étapes suivantes

[Activer l’accès distant pour SharePoint avec le proxy d’application Azure AD](application-proxy-enable-remote-access-sharepoint.md)<br>
[Activer le proxy d’application dans le Portail Azure](https://github.com/Microsoft/azure-docs-pr/blob/master/articles/active-directory/active-directory-application-proxy-enable.md)



<!--HONumber=Feb17_HO1-->


