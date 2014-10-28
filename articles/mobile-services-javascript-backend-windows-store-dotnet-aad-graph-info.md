<properties linkid="develop-mobile-tutorials-javascript-aad-graph-info" urlDisplayName="Accessing Azure Active Directory Graph Information" pageTitle="Accessing Azure Active Directory Graph Information (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to access Azure Active Directory information using the Graph API in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Accessing Azure Active Directory Graph Information" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/20/2014" ms.author="wesmc"></tags>

# Accès aux informations Azure Active Directory Graph

<div class="dev-center-tutorial-selector sublanding">
    <a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-aad-graph-info/" title="Windows Store&nbsp;C#" class="current">Windows Store&nbsp;C#</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info/" title=".NET backend">Serveur principal .NET</a> |
    <a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/" title="JavaScript backend" class="current">JavaScript backend</a>
</div>

Comme tous les autres fournisseurs d'identité proposés avec Mobile Services, le fournisseur Azure Active Directory (AAD) prend en charge une [API Graph][API Graph] enrichie pouvant être utilisée pour l'accès par programme à l'annuaire. Dans ce didacticiel, vous allez mettre à jour l'application ToDoList pour personnaliser l'expérience des utilisateurs authentifiés d'après les informations utilisateur supplémentaires collectées à partir de l'annuaire à l'aide de l'[API Graph][API Graph].

> [WACOM.NOTE] L'objectif de ce didacticiel est d'approfondir vos connaissances sur l'authentification à l'aide d'Azure Active Directory. Vous devez donc au préalable avoir suivi le didacticiel [Prise en main de l'authentification][Prise en main de l'authentification] avec le fournisseur d'authentification Azure Active Directory. Ce didacticiel poursuit la mise à jour de l'application TodoItem utilisée dans le didacticiel [Prise en main de l'authentification][Prise en main de l'authentification].

Ce didacticiel vous familiarise avec la procédure suivante :

1.  [Génération d'une clé d'accès pour l'inscription de l'application dans AAD][Génération d'une clé d'accès pour l'inscription de l'application dans AAD]
2.  [Création d'une API personnalisée GetUserInfo][Création d'une API personnalisée GetUserInfo]
3.  [Mise à jour de l'application pour utiliser l'API personnalisée][Mise à jour de l'application pour utiliser l'API personnalisée]
4.  [Test de l'application][Test de l'application]

## Configuration requise

Avant de commencer ce didacticiel, vous devez suivre les didacticiels Mobile Services suivants :

-   [Prise en main de l'authentification][Prise en main de l'authentification]
    Ajoute une exigence de connexion à l'exemple d'application TodoList.

-   [Didacticiel relatif aux API personnalisées][Didacticiel relatif aux API personnalisées]
    Explique comment appeler une API personnalisée.

## <a name="generate-key"></a>Génération d'une clé d'accès pour l'inscription de l'application dans AAD

Avec le didacticiel [Prise en main de l'authentification][Prise en main de l'authentification], vous avez créé une inscription pour l'application intégrée lors de l'étape [Inscription à des fins d'utilisation d'une connexion Azure Active Directory][Inscription à des fins d'utilisation d'une connexion Azure Active Directory]. Dans cette section, vous allez générer la clé devant être utilisée pour lire les informations d'annuaire avec l'ID client de cette application intégrée.

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key][mobile-services-generate-aad-app-registration-access-key]]

## <a name="create-api"></a>Création d'une API personnalisée GetUserInfo

Dans cette section, vous allez créer l'API personnalisée GetUserInfo devant utiliser l'[API REST Graph][API REST Graph] pour extraire des informations utilisateur supplémentaires à partir d'AAD.

Si vous n'avez encore jamais utilisé d'API personnalisées avec Mobile Services, suivez le [didacticiel relatif aux API personnalisées][Didacticiel relatif aux API personnalisées] avant de poursuivre.

1.  Dans le [portail de gestion Azure][portail de gestion Azure], créez l'API personnalisée GetUserInfo pour votre service mobile et définissez les autorisations de la méthode get sur **Seuls les utilisateurs authentifiés**.

    ![][]

2.  Ouvrez l'éditeur de script pour la nouvelle API GetUserInfo et collez les variables suivantes en haut du script.

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;

3.  Ajoutez la définition suivante à la fonction `getAADToken`.

        function getAADToken(callback) {
            var req = require("request");
            var options = {
                url: "https://login.windows.net/" + tenant_domain + "/oauth2/token?api-version=1.0",
                method: 'POST',
                form: {
                    grant_type: "client_credentials",
                    resource: "https://graph.windows.net",
                    client_id: clientID,
                    client_secret: key
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).access_token);
            });
        }

    Étant donné le *domaine du locataire*, l'*ID client* de l'application intégrée et la *clé* d'application, cette fonction fournit un jeton d'accès Graph permettant de lire les informations de l'annuaire.

4.  Ajoutez la fonction `getUser` suivante qui utilise l'API Graph pour renvoyer les informations de l'utilisateur.

        function getUser(access_token, objectId, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/users/" + objectId + "?api-version=1.0",
                method: 'GET',
                headers: {
                    "Authorization": "Bearer " + access_token
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body));
            });
        };

    Cette fonction est un wrapper fin autour du point de terminaison [Get User][Get User] de l'API REST Graph. Elle utilise le jeton d'accès Graph pour obtenir les informations de l'utilisateur à l'aide de l'ID d'objet de ce dernier.

5.  Mettez à jour la méthode `get` exportée comme suit pour renvoyer des informations utilisateur à l'aide des autres fonctions.

        exports.get = function (request, response) {
            if (request.user.level == 'anonymous') {
                response.send(statusCodes.UNAUTHORIZED, null);
                return;
            }
            var errorHandler = function (err) {
                console.log(err);
                response.send(statusCodes.INTERNAL_SERVER_ERROR, err);
            };
            request.user.getIdentities({
                success: function (identities) {
                    var objectId = identities.aad.oid;
                    getAADToken(function (err, access_token) {
                        if (err) errorHandler(err);
                        else getUser(access_token, objectId, function (err, user_info) {
                            if (err) errorHandler(err);
                            else {
                                console.log('GetUserInfo: ' + JSON.stringify(user_info, null, 4));
                                response.send(statusCodes.OK, user_info);
                              }
                            });
                    });
                },
                error: errorHandler
            });
        };

## <a name="update-app"></a>Mise à jour de l'application pour utiliser l'API personnalisée GetUserInfo

Dans cette section, vous allez mettre à jour la méthode `AuthenticateAsync` implémentée dans le didacticiel [Prise en main de l'authentification][Prise en main de l'authentification] pour appeler l'API personnalisée et renvoyer des informations supplémentaires sur l'utilisateur à partir d'AAD.

[WACOM.INCLUDE [mobile-services-aad-graph-info-update-app][mobile-services-aad-graph-info-update-app]]

## <a name="test-app"></a>Test de l'application

[WACOM.INCLUDE [mobile-services-aad-graph-info-test-app][mobile-services-aad-graph-info-test-app]]

## <a name="next-steps"></a>Étapes suivantes

Dans le prochain didacticiel, [Contrôle d'accès en fonction du rôle avec AAD dans Mobile Services][Contrôle d'accès en fonction du rôle avec AAD dans Mobile Services], vous utiliserez le contrôle d'accès en fonction du rôle avec Azure Active Directory (AAD) pour contrôler l'appartenance aux groupes avant d'autoriser l'accès.

<!-- Anchors. -->
<!-- Images -->
<!-- URLs. -->

  [Windows Store C#]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-aad-graph-info/ "Windows Store C#"
  [Serveur principal .NET]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info/ ".NET backend"
  [JavaScript backend]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/ "JavaScript backend"
  [API Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [Génération d'une clé d'accès pour l'inscription de l'application dans AAD]: #generate-key
  [Création d'une API personnalisée GetUserInfo]: #create-api
  [Mise à jour de l'application pour utiliser l'API personnalisée]: #update-app
  [Test de l'application]: #test-app
  [Didacticiel relatif aux API personnalisées]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
  [Inscription à des fins d'utilisation d'une connexion Azure Active Directory]: /fr-fr/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [mobile-services-generate-aad-app-registration-access-key]: ../includes/mobile-services-generate-aad-app-registration-access-key.md
  [API REST Graph]: http://msdn.microsoft.com/fr-fr/library/azure/hh974478.aspx
  [portail de gestion Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/create-getuserinfo.png
  [Get User]: http://msdn.microsoft.com/fr-fr/library/azure/dn151678.aspx
  [mobile-services-aad-graph-info-update-app]: ../includes/mobile-services-aad-graph-info-update-app.md
  [mobile-services-aad-graph-info-test-app]: ../includes/mobile-services-aad-graph-info-test-app.md
  [Contrôle d'accès en fonction du rôle avec AAD dans Mobile Services]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/
