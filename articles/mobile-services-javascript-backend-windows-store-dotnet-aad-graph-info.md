<properties 
	pageTitle="Accès aux informations de graphique Azure Active Directory (Windows Store) | Centre de développement mobile" 
	description="Découvrez comment accéder aux informations Azure Active Directory à l'aide de l'API Graph dans votre application Windows Store." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/29/2014" 
	ms.author="wesmc"/>

# Accès aux informations Azure Active Directory Graph

[AZURE.INCLUDE [mobile-services-selector-aad-graph](../includes/mobile-services-selector-aad-graph.md)]


Comme tous les autres fournisseurs d'identité proposés avec Mobile Services, le fournisseur Azure Active Directory (AAD) prend en charge une [API Graph] enrichie pouvant être utilisée pour l'accès par programme à l'annuaire. Dans ce didacticiel, vous allez mettre à jour l'application ToDoList pour personnaliser l'expérience des utilisateurs authentifiés d'après les informations utilisateur supplémentaires collectées à partir de l'annuaire à l'aide de l'[API Graph].

>[AZURE.NOTE] L'objectif de ce didacticiel est d'approfondir vos connaissances sur l'authentification à l'aide d'Azure Active Directory. Cette rubrique suppose que vous avez suivi le didacticiel [Prise en main de l'authentification] avec le fournisseur d'authentification Azure Active Directory. Ce didacticiel continue la mise à jour de l'application TodoItem utilisée dans le didacticiel [Prise en main de l'authentification].



Ce didacticiel vous familiarise avec la procédure suivante :


1. [Génération d'une clé d'accès pour l'inscription de l'application dans AAD]
2. [Création d'une API personnalisée GetUserInfo]
3. [Mise à jour de l'application pour utiliser l'API personnalisée]
4. [Test de l'application]

##Conditions préalables 

Avant de commencer ce didacticiel, vous devez suivre les didacticiels Mobile Services suivants :

+ [Prise en main de l'authentification]<br/>Ajoute une exigence de connexion à l'exemple d'application TodoList.

+ [Didacticiel de l'API personnalisée]<br/>Explique comment appeler une API personnalisée. 



## <a name="generate-key"></a>Génération d'une clé d'accès pour l'inscription de l'application dans AAD


Dans le didacticiel [Prise en main de l'authentification], vous avez créé une inscription pour l'application intégrée lorsque vous avez effectué l'étape [Inscription pour l'utilisation d'une connexion Azure Active Directory]. Dans cette section, vous allez générer la clé devant être utilisée pour lire les informations d'annuaire avec l'ID client de cette application intégrée. 

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]



## <a name="create-api"></a>Création d'une API personnalisée GetUserInfo

Dans cette section, vous allez créer l'API personnalisée GetUserInfo devant utiliser l'[API REST Graph] pour extraire des informations utilisateur supplémentaires à partir d'AAD.

Si vous n'avez encore jamais utilisé d'API personnalisées avec Mobile Services, suivez le [didacticiel relatif aux API] personnalisées avant de poursuivre.

1. Dans le [portail de gestion Azure], créez l'API personnalisée GetUserInfo pour votre service mobile et définissez les autorisations de la méthode get sur **Seuls les utilisateurs authentifiés**.

    ![][0]

2. Ouvrez l'éditeur de script pour la nouvelle API GetUserInfo et collez les variables suivantes en haut du script.

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;



3. Ajoutez la définition suivante à la fonction  `getAADToken`.

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

    Étant donné *tenant_domain*,  *client id* application intégrée et  *key* application, cette fonction fournit un jeton d'accès Graph permettant de lire les informations de l'annuaire.

4. Ajoutez la fonction `getUser` suivante qui utilise l'API Graph pour renvoyer les informations de l'utilisateur.

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

    Cette fonction est un wrapper fin autour du point de terminaison [Get User] de l'API REST Graph. Elle utilise le jeton d'accès Graph pour obtenir les informations de l'utilisateur à l'aide de l'ID d'objet de ce dernier.

5. Mettez à jour la méthode `get` exportée comme suit pour renvoyer des informations utilisateur à l'aide des autres fonctions.

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


Dans cette section, vous allez mettre à jour la méthode  `AuthenticateAsync` que vous avez mise en place dans le didacticiel [Prise en main de l'authentification] pour appeler l'API personnalisée et renvoyer des informations utilisateur supplémentaires depuis AAD. 

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../includes/mobile-services-aad-graph-info-update-app.md)]


 


## <a name="test-app"></a>Test de l'application

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../includes/mobile-services-aad-graph-info-test-app.md)]




##<a name="next-steps"></a>Étapes suivantes

Dans le prochain didacticiel, [Contrôle d'accès basé sur les rôles avec Azure ﻿Active Directory dans Mobile Services], vous allez utiliser le contrôle d'accès basé sur les rôles avec Azure Active Directory (AAD) pour vérifier l'appartenance aux groupes avant d'autoriser l'accès. 


<!-- Anchors. -->
[Génération d'une clé d'accès pour l'inscription de l'application dans AAD]: #generate-key
[Création d'une API personnalisée GetUserInfo]: #create-api
[Mise à jour de l'application pour utiliser l'API personnalisée]: #update-app
[Test de l'application]: #test-app
[Étapes suivantes]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/create-getuserinfo.png


<!-- URLs. -->
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[Inscription auprès de l'annuaire Azure Active Directory]: /fr-fr/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Portail de gestion Azure]: https://manage.windowsazure.com/
[Didacticiel de l'API personnalisée]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
[Stockage des scripts serveur]: /fr-fr/documentation/articles/mobile-services-store-scripts-source-control/
[Inscription à des fins d'utilisation d'une connexion Azure Active Directory]: /fr-fr/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[API Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[API REST Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Get User]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[Contrôle d'accès en fonction du rôle avec AAD dans Mobile Services]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/


<!--HONumber=42-->
