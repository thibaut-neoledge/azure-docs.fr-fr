---
title: "Installer sans assistance le connecteur du proxy d’application Azure AD | Microsoft Docs"
description: "Explique comment effectuer une installation silencieuse du connecteur du Proxy d’application Azure AD pour offrir un accès à distance sécurisé à vos applications locales."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: 3aa1c7f2-fb2a-4693-abd5-95bb53700cbb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: f4d72d4d11ee64e3431879f6ad1b5d8d091a0c87
ms.contentlocale: fr-fr
ms.lasthandoff: 05/15/2017

---
<a id="silently-install-the-azure-ad-application-proxy-connector" class="xliff"></a>

# Installer silencieusement le connecteur du Proxy d'application Azure AD
Vous souhaitez pouvoir envoyer un script d’installation vers plusieurs serveurs Windows ou vers des serveurs Windows sans interface utilisateur. Cette rubrique vous aide à créer un script Windows PowerShell qui active l’installation sans assistance pour l’installation et l’inscription de votre connecteur de Proxy d’application Azure AD.

Cette fonctionnalité est utile lorsque vous souhaitez :

* Installer le connecteur sur des machines sans couche d’interface utilisateur ou quand vous ne pouvez pas vous connecter à l’ordinateur via RDP.
* Installer et inscrire beaucoup de connecteurs à la fois.
* Intégrer l’installation et l’inscription du connecteur dans le cadre d’une autre procédure.
* Créer une image de serveur standard qui contient les exécutables du connecteur, mais qui n’est pas inscrite.

Le Proxy d’application fonctionne avec un service Windows Server léger appelé connecteur, à l’intérieur de votre réseau. Pour que le connecteur du Proxy d'application puisse fonctionner, il doit être inscrit auprès de votre annuaire Azure AD à l'aide d'un identifiant d’administrateur global et d’un mot de passe. Généralement, ces informations sont saisies pendant l'installation du connecteur dans une boîte de dialogue contextuelle. Vous pouvez néanmoins utiliser Windows PowerShell pour créer un objet d’informations d’identification afin d’entrer vos informations d’inscription, ou vous pouvez créer votre propre jeton et l’utiliser pour communiquer vos informations d’inscription.

<a id="install-the-connector" class="xliff"></a>

## Installation du connecteur
Pour installer les MSI du connecteur sans inscrire le connecteur, procédez comme suit :

1. Ouvrez une invite de commandes.
2. Exécutez la commande suivante dans laquelle /q signifie une installation silencieuse ; l’installation ne vous demande pas d’accepter le Contrat de Licence Utilisateur Final.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

<a id="register-the-connector-with-azure-ad" class="xliff"></a>

## Inscription du connecteur sur Azure Active Directory
Deux méthodes permettent d’inscrire le connecteur :

* Inscription du connecteur à l’aide d’un objet d’informations d’identification Windows PowerShell
* Inscription du connecteur à l’aide d’un jeton créé hors connexion

<a id="register-the-connector-using-a-windows-powershell-credential-object" class="xliff"></a>

### Inscription du connecteur à l’aide d’un objet d’informations d’identification Windows PowerShell
1. Créez l’objet d’informations d’identification de Windows PowerShell en exécutant la commande suivante. Remplacez *\<username\>* et *\<password\>* par le nom d’utilisateur et le mot de passe de votre répertoire :
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Accédez à **C:\Program Files\Microsoft AAD App Proxy Connector** et exécutez le script en utilisant l’objet d’informations d’identification PowerShell que vous avez créé. Remplacez *$cred* par le nom de l’objet d’informations d’identification PowerShell que vous avez créé :
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred

<a id="register-the-connector-using-a-token-created-offline" class="xliff"></a>

### Inscription du connecteur à l’aide d’un jeton créé hors connexion
1. Créez un jeton hors connexion à l’aide de la classe AuthenticationContext, en utilisant les valeurs de l’extrait de code :

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.microsoftonline.com/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }


2. Une fois que vous disposez du jeton, créez une chaîne SecureString en utilisant le jeton :

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Exécuter la commande Windows PowerShell suivante, en remplaçant \<locataire GUID\> avec votre ID de répertoire :

   `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`

<a id="next-steps" class="xliff"></a>

## Étapes suivantes 
* [Publier des applications avec votre propre nom de domaine](active-directory-application-proxy-custom-domains.md)
* [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
* [Résoudre les problèmes rencontrés avec le proxy d’application](active-directory-application-proxy-troubleshoot.md)



