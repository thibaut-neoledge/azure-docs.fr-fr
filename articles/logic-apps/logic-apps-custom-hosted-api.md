---
title: "Appeler des API personnalisées dans Azure Logic Apps | Microsoft Docs"
description: "Appelez vos propres API personnalisées hébergées sur Azure App Service avec Azure Logic Apps"
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2016
ms.author: stepsic
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 9d169bcc0b1e89866d04cced474ee9c0e6ba6952
ms.lasthandoff: 04/06/2017


---
# <a name="call-custom-apis-hosted-on-azure-app-service-with-azure-logic-apps"></a>Appeler des API personnalisées hébergées sur Azure App Service avec Azure Logic Apps

Bien qu’Azure Logic Apps offre plus de 40 connecteurs pour un large éventail de services, vous souhaiterez peut-être appeler votre propre API personnalisée pour exécuter votre propre code. Azure App Service offre l’un des moyens les plus simples et les plus évolutifs d’héberger vos propres API web personnalisées. Cet article explique comment appeler une API web hébergée dans une application API App Service, une application web ou une application mobile.
Pour savoir comment générer des API sous forme de déclencheurs ou d’actions dans des applications logiques, consultez [cet article](../logic-apps/logic-apps-create-api-app.md).

## <a name="deploy-your-web-app"></a>Déployez votre application web

Tout d’abord, vous devez déployer votre API sous forme d’application web dans Azure App Service. Pour obtenir des informations sur le déploiement de base lors de la création d’une application web ASP.NET, consultez [cet article](../app-service-web/app-service-web-get-started-dotnet.md). Bien qu’il soit possible d’appeler une API à partir d’une application logique, nous vous recommandons d’ajouter des métadonnées Swagger pour une intégration aisée avec les actions des applications logiques. La procédure d’ajout de métadonnées Swagger est présentée [ici](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui).

### <a name="api-settings"></a>API settings

Pour que le Concepteur d’application logique analyse votre Swagger, vous devez activer CORS et définir les propriétés de la définition de l’API pour votre application web. 

1.    Dans le portail Azure, sélectionnez votre application web.
2.    Dans le panneau qui s’ouvre, recherchez **API**, puis sélectionnez **Définition de l’API**. Définissez l’**emplacement de la définition de l’API** sur l’URL de votre fichier swagger.json.

    En général, cette URL est https://{nom}.azurewebsites.net/swagger/docs/v1).

3.    Pour autoriser les demandes à partir du Concepteur d’application logique, sous **API**, sélectionnez **CORS** et définissez une stratégie CORS pour « * ».

## <a name="call-into-your-custom-api"></a>Appeler votre API personnalisée

Si vous avez défini CORS et les propriétés de la définition de l’API, vous devriez être en mesure d’ajouter facilement des actions API personnalisées à votre flux dans le portail Logic Apps. Dans le Concepteur d’application logique, vous pouvez parcourir vos sites web d’abonnement pour répertorier les sites web qui ont une URL de Swagger définie. Vous pouvez également pointer vers un Swagger et répertorier les entrées et actions disponibles à l’aide de l’action HTTP + Swagger. Enfin, vous pouvez aussi créer une demande à l’aide de l’action HTTP pour appeler n’importe quelle API, y compris celles qui n’ont pas ou n’exposent pas de document Swagger.

Pour sécuriser votre API, plusieurs options s’offrent à vous :

*    Aucune modification de code requise : vous pouvez utiliser Azure Active Directory pour protéger votre API sans devoir effectuer de modification du code ou de redéploiement.
*    Dans le code de votre API, implémentez l’authentification de base, l’authentification Azure Active Directory ou l’authentification par certificat.

## <a name="secure-calls-to-your-api-without-changing-code"></a>Sécuriser les appels à votre API sans modifier le code

Dans cette section, vous allez créer deux applications Azure Active Directory : une pour votre application logique, et l’autre pour votre application web. Authentifiez les appels à votre application web à l’aide du principal de service (ID client et phrase secrète) associé à l’application Azure Active Directory de votre application logique. Enfin, incluez les ID d’application dans votre définition d’application logique.

### <a name="part-1-set-up-an-application-identity-for-your-logic-app"></a>Partie 1 : Configurer une identité d’application pour votre application logique

Votre application logique utilise cette identité d’application pour s’authentifier auprès d’Azure Active Directory. Vous n’avez besoin de configurer cette identité qu’une seule fois. Par exemple, vous pouvez choisir d’utiliser la même identité pour toutes vos applications logiques, même si vous pouvez également créer des identités uniques pour chaque application logique. Vous pouvez configurer ces identités dans le portail Azure ou à l’aide de PowerShell.

#### <a name="create-the-application-identity-in-the-azure-classic-portal"></a>Créer l’identité d’application dans le portail Azure Classic

1. Dans le portail Azure Classic, accédez à votre répertoire [Azure Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory). 
2. Sélectionnez le répertoire à utiliser pour votre application web.
3. Choisissez l’onglet **Applications**. Dans la barre de commandes en bas de la page, sélectionnez **Ajouter**.
5. Nommez votre identité d’application, puis cliquez sur la flèche Suivant.
6. Sous **Propriétés de l’application**, insérez une chaîne unique formatée comme un domaine, puis cliquez sur la coche.
7. Choisissez l’onglet **Configurer**. Accédez à **ID client**, puis copiez l’ID client à utiliser dans votre application logique.
8. Sous **Clés**, ouvrez la liste **Sélectionner une durée**, puis choisissez la durée pour votre clé.
9. En bas de la page, cliquez sur **Enregistrer**. Vous devrez peut-être patienter quelques secondes.
10. Prenez soin de copier la clé qui apparaît désormais sous **Clés** afin de pouvoir l’utiliser dans votre application logique.

#### <a name="create-the-application-identity-using-powershell"></a>Créer l’identité de l’application à l’aide de PowerShell

1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. Prenez soin de copier l’**ID de locataire**, l’**ID d’application** et le mot de passe que vous avez utilisé.

### <a name="part-2-protect-your-web-app-with-an-azure-active-directory-app-identity"></a>Partie 2: Protéger votre application web avec une identité d’application Azure Active Directory

Si votre application web est déjà déployée, vous pouvez activer l’autorisation dans le portail Azure. Autrement, vous pouvez activer l’autorisation dans le cadre de votre déploiement Azure Resource Manager.

#### <a name="enable-authorization-in-the-azure-portal"></a>Activer l’autorisation dans le portail Azure

1. Recherchez et sélectionnez votre application web. Sous **Paramètres**, choisissez **Authentification/Autorisation**.
2. Sous **Authentification App Service**, **activez** l’authentification, puis choisissez **Enregistrer**.

À ce stade, une application est automatiquement créée pour vous. Vous aurez besoin de l’ID client de cette application pour la partie 3. Vous devez donc procéder comme suit :

1. Dans le portail Azure Classic, accédez à votre répertoire [Azure Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory).
2.    Sélectionnez votre annuaire.
3. Dans la zone de recherche, recherchez votre application.
4. Dans la liste, sélectionnez votre application.
5. Choisissez l’onglet **Configurer**, dans lequel l’**ID client** devrait être indiqué.

#### <a name="deploy-your-web-app-using-an-azure-resource-manager-template"></a>Déployer votre application web à l’aide d’un modèle Azure Resource Manager

Tout d’abord, vous devez créer pour votre application web une application différente de celle utilisée pour votre application logique. Commencez par suivre les étapes de la partie 1, mais pour **HomePage** et **IdentifierUris**, utilisez l’**URL** https:// réelle de votre application web.

> [!NOTE]
> Lorsque vous créez l’application pour votre application web, vous devez utiliser le [portail Azure Classic](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory). L’applet de commande PowerShell ne configure pas les autorisations requises pour connecter les utilisateurs à un site web.

Une fois que vous disposez l’ID client et de l’ID de locataire, incluez cette partie en tant que sous-ressource de votre application web dans votre modèle de déploiement :

```
"resources": [
    {
        "apiVersion": "2015-08-01",
        "name": "web",
        "type": "config",
        "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
        "properties": {
            "siteAuthEnabled": true,
            "siteAuthSettings": {
              "clientId": "<<clientID>>",
              "issuer": "https://sts.windows.net/<<tenantID>>/",
            }
        }
    }
]
```

Pour exécuter automatiquement un déploiement qui déploie ensemble une application web et une application logique vides utilisant Azure Active Directory, cliquez sur **Deploy to Azure** (Déployer sur Azure) :

[![Déploiement sur Azure](media/logic-apps-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

Pour accéder au modèle complet, consultez [Appels de Logic App dans une API personnalisée hébergée sur App Service et protégée par Azure Active Directory](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json).

### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Partie 3 : Remplir la section Autorisation dans votre application logique

Dans la section **Autorisation** de l’action **HTTP** :

`{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| Élément | Description |
| ------- | ----------- |
| type |Type d'authentification. Pour l’authentification ActiveDirectoryOAuth, la valeur est `ActiveDirectoryOAuth`. |
| locataire |L'identifiant du locataire est utilisé pour identifier le locataire Active Directory. |
| audience |Obligatoire. Ressource à laquelle vous vous connectez. |
| clientID |L'identifiant client pour l'application Azure AD. |
| secret |Obligatoire. Secret du client qui demande le jeton. |

Cette section d’autorisation est déjà configurée dans le modèle précédent, mais si vous créez l’application logique directement, vous devez inclure la section d’autorisation complète.

## <a name="secure-your-api-in-code"></a>Sécuriser votre API dans le code

### <a name="certificate-authentication"></a>Authentification par certificat

Vous pouvez utiliser des certificats clients pour valider les demandes entrantes parvenant à votre application web. Pour savoir comment configurer votre code, consultez [Configuration de l’authentification mutuelle TLS pour une application web](../app-service-web/app-service-web-configure-tls-mutual-auth.md).

Dans la section **Autorisation**, vous devez inclure : 

`{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`

| Élément | Description |
| ------- | ----------- |
| type |Obligatoire. Type d'authentification. Pour les certificats client SSL, la valeur doit être `ClientCertificate`. |
| pfx |Obligatoire. Contenu codé en base64 du fichier PFX. |
| password |Obligatoire. Mot de passe pour accéder au fichier PFX. |

### <a name="basic-authentication"></a>Authentification de base

Pour valider les demandes entrantes, vous pouvez utiliser l’authentification de base avec un nom d’utilisateur et un mot de passe. L’authentification de base est une méthode courante que vous pouvez utiliser dans n’importe quel langage utilisé pour générer votre application.

Dans la section **Autorisation**, vous devez inclure :

`{"type": "basic","username": "test","password": "test"}`.

| Élément | Description |
| --- | --- |
| type |Obligatoire. Type d'authentification. Pour l'authentification de base, la valeur doit être `Basic`. |
| username |Obligatoire. Nom d'utilisateur à authentifier. |
| password |Obligatoire. Mot de passe à authentifier. |

### <a name="handle-azure-active-directory-authentication-in-code"></a>Gérer l’authentification Azure Active Directory dans le code

Par défaut, l’authentification Azure Active Directory que vous activez dans le portail Azure ne fournit pas une autorisation affinée. Par exemple, cette authentification ne verrouille pas votre API sur un utilisateur ou une application spécifique, mais uniquement sur un locataire particulier.

Pour limiter votre API à votre application logique, par exemple, extrayez l’en-tête qui possède le jeton JWT dans le code. Vérifiez l’identité de l’appelant et rejetez les demandes qui ne correspondent pas.

Pour aller plus loin, si vous souhaitez implémenter cette authentification en totalité dans votre code et ne pas utiliser la fonctionnalité du portail Azure, consultez [Authentification avec Active Directory en local dans votre application Azure](../app-service-web/web-sites-authentication-authorization.md).
Pour créer une identité d’application pour votre application logique, puis utiliser cette application pour appeler votre API, vous devez suivre les étapes précédentes.
