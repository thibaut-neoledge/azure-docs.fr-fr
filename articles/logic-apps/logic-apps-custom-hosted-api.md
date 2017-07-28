---
title: "Déployer, appeler et authentifier des API web et REST pour Azure Logic Apps | Microsoft Docs"
description: "Déployer, authentifier et appeler des API web et REST dans les flux de travail d’intégration système avec Azure Logic Apps"
keywords: "API web, API REST, connecteurs, flux de travail, intégration système, authentifier"
services: logic-apps
author: stepsic-microsoft-com
manager: anneta
editor: 
documentationcenter: 
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: LADocs; stepsic
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: 88c62d5ab046d8cf4bce5d23b776e517bb0e1d87
ms.contentlocale: fr-fr
ms.lasthandoff: 06/08/2017

---

# <a name="deploy-call-and-authenticate-custom-apis-as-connectors-for-logic-apps"></a>Déployer, appeler et authentifier des API personnalisées en tant que connecteurs pour les applications logiques

Après avoir [créé des API personnalisées](./logic-apps-create-api-app.md) qui fournissent des actions ou des déclencheurs à utiliser dans les flux de travail d’application logique, vous devez déployer vos API avant de pouvoir les appeler. Même si vous pouvez appeler n’importe quelle API à partir d’une application logique, vous bénéficierez d’une expérience optimale si vous ajoutez des [métadonnées Swagger](http://swagger.io/specification/) décrivant les opérations et paramètres de votre API. Ce fichier Swagger améliore le fonctionnent de votre API et simplifie son intégration aux applications logiques.

Vous pouvez déployer vos API en tant [qu’applications web](../app-service-web/app-service-web-overview.md). Cependant, envisagez de les déployer en tant [qu’applications API](../app-service-api/app-service-api-apps-why-best-platform.md) pour simplifier votre travail lorsque vous générez, hébergez et consommez des API dans le cloud et en local. Vous n’êtes pas obligé de modifier le code dans vos API. Il vous suffit de déployer votre code dans une application API. Vous pouvez héberger vos API sur [Azure App Service](../app-service/app-service-value-prop-what-is.md), une offre PaaS (Platform-as-a-Service) qui fournit l’une des méthodes les plus efficaces, les plus simples et les plus évolutives pour héberger des API.

Pour authentifier des appels à partir d’applications logiques vers vos API, vous pouvez configurer Azure Active Directory dans le Portail Azure, ce qui vous évite de mettre à jour votre code. Vous pouvez également exiger et appliquer une authentification par le biais du code de votre API.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Déployer votre API en tant qu’application web ou application API

Pour pouvoir appeler votre API personnalisée à partir d’une application logique, déployez votre API en tant qu’application web ou application API sur Azure App Service. Par ailleurs, pour vous assurer de la lisibilité du document Swagger par le concepteur d’application logique, définissez les propriétés de définition d’API et activez le [partage des ressources Cross-Origin (CORS)](../app-service-api/app-service-api-cors-consume-javascript.md#corsconfig) pour votre application web ou votre application API.

1. Dans le portail Azure, sélectionnez votre application web ou votre application API.

2. Dans le panneau qui s’ouvre, sous **API**, choisissez **Définition de l’API**. Sous **Emplacement de la définition de l’API**, spécifiez l’URL de votre fichier swagger.json.

   En règle générale, l’URL se présente sous le format suivant :`https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Lien vers le fichier Swagger pour votre API personnalisée](media/logic-apps-custom-hosted-api/custom-api-swagger-url.png)

3. Sous **API**, choisissez **CORS**. Dans le cadre de la stratégie CORS, sous **Origines autorisées**, spécifiez **'*'** (tout autoriser).

   Ce paramètre autorise les demandes à partir du concepteur d’application logique.

   ![Autoriser les demandes du concepteur d’application logique à votre API personnalisée](media/logic-apps-custom-hosted-api/custom-api-cors.png)

Pour plus d’informations, voir les articles suivants :

* [Utiliser l’interface utilisateur et les métadonnées d’API Swagger](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui)
* [Prise en main d’API Apps, d’ASP.NET et de Swagger dans Azure App Service](../app-service-api/app-service-api-dotnet-get-started.md)

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Appeler votre API personnalisée à partir de flux de travail d’application logique

Une fois les propriétés de définition d’API et le partage CORS configurés, les déclencheurs et actions de votre API personnalisée doivent devenir disponibles, de sorte que vous puissiez les inclure dans votre flux de travail d’application logique. 

*  Pour afficher les sites web possédant des URL Swagger, vous pouvez parcourir vos sites web d’abonnement dans le concepteur d’application logique.

*  Pour afficher les actions et entrées disponibles en pointant sur un document Swagger, utilisez [l’action HTTP + Swagger](../connectors/connectors-native-http-swagger.md).

*  Pour appeler n’importe quelle API, y compris celles qui n’ont pas ou n’exposent pas de document Swagger, vous pouvez aussi créer une requête à l’aide de [l’action HTTP](../connectors/connectors-native-http.md).

## <a name="authenticate-calls-to-your-custom-api"></a>Authentifier les appels à votre API personnalisée

Vous pouvez sécuriser les appels à votre API personnalisée en appliquant les méthodes suivantes :

* [Aucune modification de code](#no-code) : protégez votre API avec [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) par le biais du Portail Azure, ce qui vous évite de mettre à jour votre code ou de redéployer votre API.

  > [!NOTE]
  > Par défaut, l’authentification Azure AD que vous activez dans le Portail Azure ne fournit pas une autorisation affinée. Par exemple, cette authentification verrouille votre API vis-à-vis d’un locataire spécifique et non d’un utilisateur ou d’une application spécifique. 

* [Mise à jour du code de votre API](#update-code) : protégez votre API en appliquant [l’authentification par certificat](#certificate), [l’authentification de base](#basic) ou [l’authentification Azure AD](#azure-ad-code) par le biais du code.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Authentifier les appels à votre API sans modifier le code

Voici les étapes générales de cette méthode :

1. Créez deux [identités d’application Azure Active Directory (Azure AD)](../app-service-api/app-service-api-dotnet-service-principal-auth.md) : une pour votre application logique et l’autre pour votre application web (ou application API).

2. Pour authentifier les appels à votre API, utilisez les informations d’identification (ID et clé secrète client) pour le [principal de service](../app-service-api/app-service-api-dotnet-service-principal-auth.md) associé à l’identité d’application Azure AD pour votre application logique.

3. Incluez les ID d’application dans votre définition d’application logique.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>Partie 1 : Créer une identité d’application Azure AD pour votre application logique

Votre application logique utilise cette identité d’application Azure AD pour s’authentifier auprès d’Azure AD. Vous n’avez besoin de configurer cette identité qu’une seule fois pour votre répertoire. Par exemple, vous pouvez choisir d’utiliser la même identité pour toutes vos applications logiques, même si vous pouvez créer des identités uniques pour chaque application logique. Vous pouvez configurer ces identités dans le Portail Azure, dans le [Portail Azure Classic](#app-identity-logic-classic) ou à l’aide de [PowerShell](#powershell).

**Créer l’identité d’application pour votre application logique dans le Portail Azure**

1. Dans le [Portail Azure](https://portal.azure.com "https://portal.azure.com"), choisissez **Azure Active Directory**. 

2. Vérifiez que vous vous trouvez dans le même répertoire que votre application web ou votre application API.

   > [!TIP]
   > Pour changer de répertoire, cliquez sur votre profil et sélectionnez un autre répertoire. Vous pouvez également sélectionner **Présentation** > **Changer de répertoire**.

3. Dans le menu du répertoire, sous **Gérer**, choisissez **Inscriptions des applications** > **Nouvelle inscription d’application**.

   > [!TIP]
   > Par défaut, la liste des inscriptions d’application affiche toutes les inscriptions d’application de votre répertoire. Pour afficher uniquement vos inscriptions d’application, sélectionnez **Mes applications** en regard de la zone de recherche. 

   ![Créer une inscription d’application](./media/logic-apps-custom-hosted-api/new-app-registration-azure-portal.png)

4. Donnez un nom à votre identité d’application, laissez le champ **Type d’application** défini sur **Application/API web**, indiquez une chaîne unique sous forme de domaine sous **URL de connexion**, puis cliquez sur **Créer**.

   ![Indication d’un nom et d’une URL de connexion pour l’identité de l’application](./media/logic-apps-custom-hosted-api/logic-app-identity-azure-portal.png)

   L’identité d’application que vous avez créée pour votre application logique s’affiche maintenant dans la liste des inscriptions d’application.

   ![Identité d’application pour votre application logique](./media/logic-apps-custom-hosted-api/logic-app-identity-created.png)

5. Dans la liste des inscriptions d’application, sélectionnez votre nouvelle identité d’application. Copiez et enregistrez **l’ID de l’application** à utiliser en tant qu’ID client pour votre application logique dans la partie 3.

   ![Copie et enregistrement de l’ID de l’application pour l’application logique](./media/logic-apps-custom-hosted-api/logic-app-application-id.png)

6. Si vos paramètres d’identité d’application ne sont pas visibles, choisissez **Paramètres** ou **Tous les paramètres**.

7. Sous **Accès d’API**, choisissez **Clés**. Sous **Description**, indiquez le nom de votre clé. Sous **Date d’expiration**, sélectionnez la durée de votre clé.

   La clé que vous créez joue le rôle de clé secrète ou de mot de passe de l’identité de l’application pour votre application logique.

   ![Création d’une clé pour l’identité de l’application logique](./media/logic-apps-custom-hosted-api/create-logic-app-identity-key-secret-password.png)

8. Dans la barre d’outils, choisissez **Enregistrer**. Votre clé apparaît maintenant sous **Valeur**. 
**Veillez à copier et enregistrer votre clé** en vue d’une utilisation ultérieure. En effet, la clé est masquée lorsque vous quittez le panneau correspondant.

   Lorsque vous configurez votre application logique dans la partie 3, vous spécifiez cette clé comme clé secrète ou mot de passe.

   ![Copie et enregistrement de la clé pour une utilisation ultérieure](./media/logic-apps-custom-hosted-api/logic-app-copy-key-secret-password.png)

<a name="app-identity-logic-classic"></a>

**Créer l’identité d’application pour votre application logique dans le Portail Azure Classic**

1. Dans le Portail Azure Classic, choisissez [**Active Directory**](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory).

2. Sélectionnez le répertoire utilisé pour votre application web ou votre application API.

3. Dans l’onglet **Applications**, cliquez sur **Ajouter** au bas de la page.

4. Donnez un nom à votre identité d’application, puis cliquez sur **Suivant** (flèche droite).

5. Sous **Propriétés de l’application**, indiquez une chaîne unique sous forme de domaine sous **URL de connexion** et **URI ID d’application**, puis choisissez **Terminer** (coche).

6. Dans l’onglet **Configurer**, copiez et enregistrez **l’ID client** pour votre application logique. Vous l’utiliserez dans la partie 3.

7. Sous **Clés**, ouvrez la liste **Sélectionner une durée**. Sélectionnez la durée de votre clé.

   La clé que vous créez joue le rôle de clé secrète ou de mot de passe de l’identité d’application pour votre application logique.

8. En bas de la page, cliquez sur **Enregistrer**. Vous devrez peut-être patienter quelques secondes.

9. Sous **Clés**, veillez à copier et enregistrer la clé qui s’affiche à présent. 

   Lorsque vous configurez votre application logique dans la partie 3, vous spécifiez cette clé comme clé secrète ou mot de passe.

Pour plus d’informations, voir [Configurer votre application App Service pour utiliser la connexion Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

<a name="powershell"></a>

**Créer l’identité d’application pour votre application logique dans PowerShell**

Vous pouvez effectuer cette tâche par le biais d’Azure Resource Manager avec PowerShell. Dans PowerShell, exécutez ces commandes :

1. `Switch-AzureMode AzureResourceManager`

2. `Add-AzureAccount`

3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password "identity-password"`

4. Veillez à copier **l’ID de locataire** (GUID pour votre locataire Azure AD), **l’ID d’application** et le mot de passe que vous avez utilisé.

Pour plus d’informations, consultez la page [Créer un principal du service pour accéder aux ressources à l’aide d’Azure PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md).

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>Partie 2 : Créer une identité d’application Azure AD pour votre application web ou votre application API

Si votre application web ou votre application API est déjà déployée, vous pouvez activer l’authentification et créer l’identité de l’application dans le Portail Azure. Sinon, vous pouvez [activer l’authentification lorsque vous effectuez un déploiement avec un modèle Azure Resource Manager](#authen-deploy). 

**Créer l’identité d’application et activer l’authentification dans le Portail Azure pour les applications déployées**

1. Dans le [Portail Azure](https://portal.azure.com "https://portal.azure.com"), recherchez et sélectionnez votre application web ou votre application API. 

2. Sous **Paramètres**, choisissez **Authentification/Autorisation**. Sous **Authentification App Service**, activez **l’authentification**. Sous **Fournisseurs d’authentification**, sélectionnez **Azure Active Directory**.

   ![Activer l’authentification](./media/logic-apps-custom-hosted-api/custom-web-api-app-authentication.png)

3. À présent, créez une identité d’application pour votre application web ou votre application API comme indiqué ici. Dans le panneau **Paramètres Azure Active Directory**, définissez le **Mode d’administration** sur **Express**. Choisissez **Créer une application AD**. Donnez un nom à votre identité d’application, puis cliquez sur **OK**. 

   ![Créer une identité d’application pour votre application web ou votre application API](./media/logic-apps-custom-hosted-api/custom-api-application-identity.png)

4. Dans le panneau **Authentification/Autorisation**, cliquez sur **Enregistrer**.

Vous devez à présent rechercher l’ID client et l’ID de locataire pour l’identité d’application associée à votre application web ou votre application API. Vous utiliserez ces ID dans la partie 3. Poursuivez cette procédure avec le Portail Azure ou le [Portail Azure Classic](#find-id-classic).

**Rechercher l’ID client et l’ID de locataire de l’identité de l’application pour votre application web ou votre application API dans le Portail Azure**

1. Sous **Fournisseurs d’authentification**, sélectionnez **Azure Active Directory**. 

   ![Sélectionner « Azure Active Directory »](./media/logic-apps-custom-hosted-api/custom-api-app-identity-client-id-tenant-id.png)

2. Dans le panneau **Paramètres Azure Active Directory**, définissez le **Mode d’administration** sur **Avancé**.

3. Copiez **l’ID client** et enregistrez ce GUID en vue de son utilisation dans la partie 3.

   > [!TIP] 
   > Si **l’ID client** et **l’URL de l’émetteur** ne s’affichent, essayez d’actualiser le Portail Azure et répétez l’étape 1.

4. Sous **URL de l’émetteur**, copiez et enregistrez simplement le GUID pour la partie 3. Vous pouvez également utiliser ce GUID dans le modèle de déploiement de votre application web ou de votre application API si nécessaire.

   Ce GUID est celui de votre locataire spécifique (« ID de locataire ») et doit apparaître dans cette URL :`https://sts.windows.net/{GUID}`

5. Sans enregistrer vos modifications, fermez le panneau **Paramètres Azure Active Directory**.

<a name="find-id-classic"></a>

**Rechercher l’ID client et l’ID de locataire de l’identité d’application pour votre application web ou votre application API dans le Portail Azure Classic**

1. Dans le Portail Azure Classic, choisissez [**Active Directory**](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory).

2.  Sélectionnez le répertoire utilisé pour votre application web ou votre application API.

3. Dans la zone **Rechercher**, recherchez et sélectionnez l’identité d’application pour votre application web ou votre application API.

4. Dans l’onglet **Configurer**, copiez **l’ID client** et enregistrez ce GUID en vue de son utilisation dans la partie 3.

5. Après avoir obtenu l’ID client, au bas de l’onglet **Configurer**, choisissez **Points de terminaison**.

6. Copiez l’URL du **Document de métadonnées de fédération**, puis accédez à cette URL.

7. Dans le document de métadonnées qui s’ouvre, recherchez l’élément racine **EntityDescriptor ID**, qui possède un attribut **entityID** sous cette forme :`https://sts.windows.net/{GUID}` 

      Le GUID de cet attribut est celui de votre locataire spécifique (ID de locataire).

8. Copiez l’ID de locataire et enregistrez-le en vue de son utilisation dans la partie 3. Vous l’utiliserez également dans le modèle de déploiement de votre application web ou de votre application API si nécessaire.

Pour plus d’informations, consultez les rubriques suivantes :

* [Authentification utilisateur pour les applications API dans Azure App Service](../app-service-api/app-service-api-dotnet-user-principal-auth.md)
* [Authentification et autorisation dans Azure App Service](../app-service/app-service-authentication-overview.md)

<a name="authen-deploy"></a>

**Activer l’authentification lorsque vous effectuez un déploiement avec un modèle Azure Resource Manager**

Vous devez toujours créer une identité d’application Azure AD pour votre application web ou votre application API, différente de l’identité d’application pour votre application logique. Pour créer l’identité d’application, suivez les étapes précédentes de la partie 2 avec le Portail Azure. Vous pouvez également suivre les étapes décrites dans la partie 1. Cependant, veillez à utiliser l’adresse `https://{URL}` réelle de votre application web ou de votre application API sous **URL de connexion** et **URI ID d’application**. À partir de ces étapes, vous devez enregistrer l’ID client et l’ID de locataire en vue de leur utilisation dans le modèle de déploiement de votre application. Vous les utiliserez également dans la partie 3.

> [!NOTE]
> Lorsque vous créez l’identité d’application Azure AD pour votre application web ou votre application API, vous devez utiliser le Portail Azure ou le Portail Azure Classic plutôt que PowerShell. L’applet de commande PowerShell ne configure pas les autorisations requises pour connecter les utilisateurs à un site web.

Une fois que vous disposez de l’ID client et de l’ID de locataire, incluez-les en tant que sous-ressource de votre application web ou de votre application API dans votre modèle de déploiement :

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
                 "clientId": "{client-ID}",
                 "issuer": "https://sts.windows.net/{tenant-ID}/",
               }
           }
       }
   ]
   ```

Pour déployer automatiquement une application web et une application logique vides avec l’authentification Azure Active Directory, [consultez le modèle complet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json) ou cliquez sur **Déploiement sur Azure** ici :

[![Déploiement sur Azure](media/logic-apps-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Partie 3 : Remplir la section Autorisation dans votre application logique

Cette section d’autorisation est déjà configurée dans le modèle précédent, mais si vous créez l’application logique directement, vous devez inclure la section d’autorisation complète.

Ouvrez votre définition d’application logique en mode code, accédez à la section d’action **HTTP**, recherchez la section **Autorisation** et ajoutez cette ligne :

`{"tenant": "{tenant-ID}", "audience": "{client-ID-from-Part-2-web-app-or-API app}", "clientId": "{client-ID-from-Part-1-logic-app}", "secret": "{key-from-Part-1-logic-app}", "type": "ActiveDirectoryOAuth" }`

| Élément | Description |
| ------- | ----------- |
| locataire |GUID du locataire Azure AD |
| audience |Obligatoire. GUID de la ressource cible à laquelle vous souhaitez accéder - l’ID client de l’identité d’application pour votre application web ou votre application API |
| clientId |GUID pour le client demandant l’accès - l’ID client de l’identité d’application pour votre application logique |
| secret |Obligatoire. Clé ou mot de passe de l’identité d’application pour le client qui demande le jeton d’accès |
| Type |Type d’authentification. Pour l’authentification ActiveDirectoryOAuth, la valeur est `ActiveDirectoryOAuth`. |

Par exemple :

```
{
   ...
   "actions": {
      "some-action": {
         "conditions": [],
         "inputs": {
            "method": "post",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         },
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>Sécuriser les appels à l’API avec le code

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Authentification par certificat

Vous pouvez utiliser des certificats clients pour valider les demandes entrantes de votre application logique parvenant à votre application web ou votre application API. Pour configurer votre code, consultez [Configuration de l’authentification mutuelle TLS pour une application web](../app-service-web/app-service-web-configure-tls-mutual-auth.md).

Dans la section **Autorisation**, ajoutez cette ligne : 

`{"type": "clientcertificate", "password": "password", "pfx": "long-pfx-key"}`

| Élément | Description |
| ------- | ----------- |
| type |Obligatoire. Type d’authentification. Pour les certificats client SSL, la valeur doit être `ClientCertificate`. |
| password |Obligatoire. Mot de passe pour l’accès au certificat client (fichier PFX) |
| pfx |Obligatoire. Contenu codé en base64 pour le certificat client (fichier PFX) |

<a name="basic"></a>

#### <a name="basic-authentication"></a>Authentification de base

Pour valider les demandes entrantes de votre application logique à l’attention de votre application web ou de votre application API, vous pouvez utiliser l’authentification de base, par exemple, un nom d’utilisateur et un mot de passe. L’authentification de base est une méthode courante que vous pouvez utiliser dans n’importe quel langage utilisé pour générer votre application web ou votre application API.

Dans la section **Autorisation**, ajoutez cette ligne :

`{"type": "basic", "username": "username", "password": "password"}`.

| Élément | Description |
| --- | --- |
| type |Obligatoire. Type d’authentification. Pour l’authentification de base, la valeur doit être `Basic`. |
| username |Obligatoire. Nom d’utilisateur pour l’authentification |
| password |Obligatoire. Mot de passe pour l’authentification |

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Authentification Azure Active Directory avec le code

Par défaut, l’authentification Azure AD que vous activez dans le Portail Azure ne fournit pas une autorisation affinée. Par exemple, cette authentification verrouille votre API vis-à-vis d’un locataire spécifique et non d’un utilisateur ou d’une application spécifique. 

Pour restreindre l’accès des API à votre application logique à l’aide du code, extrayez l’en-tête contenant le jeton JWT (JSON Web Token). Vérifiez l’identité de l’appelant et rejetez les demandes qui ne correspondent pas.

Pour aller plus loin, si vous souhaitez implémenter cette authentification en totalité dans votre code et ne pas utiliser le Portail Azure, consultez [Authentification avec Active Directory en local dans votre application Azure](../app-service-web/web-sites-authentication-authorization.md).

Pour créer une identité d’application pour votre application logique, puis utiliser cette identité pour appeler votre API, vous devez suivre les étapes précédentes.

## <a name="next-steps"></a>Étapes suivantes

* [Vérifier les performances de l’application logique avec des alertes et des journaux de diagnostic](logic-apps-monitor-your-logic-apps.md)
