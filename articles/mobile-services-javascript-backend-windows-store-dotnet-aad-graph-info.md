<properties 
	pageTitle="Accès aux informations de graphique Azure Active Directory (Windows Store) | Centre de développement mobile" 
	description="Découvrez comment accéder aux informations Azure Active Directory à l'aide de l'API Graph dans votre application Windows Store." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="wesmc"/>

# Accès aux informations Azure Active Directory Graph


[AZURE.INCLUDE [mobile-services-selector-aad-graph](../includes/mobile-services-selector-aad-graph.md)]
##Vue d'ensemble

Comme tous les autres fournisseurs d'identité proposés avec Mobile Services, le fournisseur Azure Active Directory (AAD) prend en charge une API Graph enrichie pouvant être utilisée pour l'accès par programme à l'annuaire. Dans ce didacticiel, vous allez mettre à jour l'application ToDoList pour personnaliser l'expérience des utilisateurs authentifiés en retournant les informations utilisateur supplémentaires collectées à partir de l'annuaire à l'aide de l'[API REST Graph].

Pour plus d'informations sur l'API Graph Azure AD, consultez le [blog de l'équipe Azure Active Directory Graph].

>[AZURE.NOTE]L'objectif de ce didacticiel est d'approfondir vos connaissances sur l'authentification à l'aide d'Azure Active Directory. Vous devez donc au préalable avoir suivi le didacticiel [Ajout de l'authentification à votre application] avec le fournisseur d'authentification Azure Active Directory. Ce didacticiel poursuit la mise à jour de l'application TodoItem utilisée dans le didacticiel [Ajout de l'authentification à votre application].



##Configuration requise 

Avant de commencer ce didacticiel, vous devez suivre les didacticiels Mobile Services suivants :

+ [Ajout de l'authentification à votre application]<br/>Ajoute une exigence de connexion à l'exemple d'application TodoList.

+ [Didacticiel relatif aux API personnalisées]<br/>Explique comment appeler une API personnalisée.



##Génération d'une clé d'accès pour l'inscription de l'application dans AAD


Avec le didacticiel [Ajout de l'authentification à votre application], vous avez créé une inscription pour l'application intégrée pendant l'étape [Inscription pour utiliser une connexion Azure Active Directory]. Dans cette section, vous allez générer la clé devant être utilisée pour lire les informations d'annuaire avec l'ID client de cette application intégrée.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]



##Création d'une API personnalisée GetUserInfo

Dans cette section, vous allez créer l'API personnalisée GetUserInfo devant utiliser l'[API REST Graph] pour extraire des informations utilisateur supplémentaires à partir d'AAD.

Si vous n'avez encore jamais utilisé d'API personnalisées avec Mobile Services, suivez le [didacticiel relatif aux API personnalisées] avant de poursuivre.

1. Dans le [portail de gestion Azure], créez l'API personnalisée GetUserInfo pour votre service mobile et définissez les autorisations de la méthode get sur **Seuls les utilisateurs authentifiés**.

    ![][0]

2. Ouvrez l'éditeur de script pour la nouvelle API GetUserInfo et collez les variables suivantes en haut du script.

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;



3. Ajoutez la définition suivante à la fonction `getAADToken`.

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


##Mise à jour de l'application pour utiliser l'API personnalisée GetUserInfo


Dans cette section, vous allez mettre à jour la méthode `AuthenticateAsync` implémentée dans le didacticiel [Prise en main de l'authentification] pour appeler l'API personnalisée et renvoyer des informations supplémentaires sur l'utilisateur à partir d'AAD.

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../includes/mobile-services-aad-graph-info-update-app.md)]


 


##Test de l'application

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../includes/mobile-services-aad-graph-info-test-app.md)]




##Étapes suivantes

Dans le prochain didacticiel, [Contrôle d'accès en fonction du rôle avec AAD dans Mobile Services], vous utiliserez le contrôle d'accès en fonction du rôle avec Azure Active Directory (AAD) pour contrôler l'appartenance aux groupes avant d'autoriser l'accès.



<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/create-getuserinfo.png


<!-- URLs. -->
[Ajout de l'authentification à votre application]: mobile-services-windows-store-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[portail de gestion Azure]: https://manage.windowsazure.com/
[Didacticiel relatif aux API personnalisées]: mobile-services-windows-store-dotnet-call-custom-api.md
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[Inscription pour utiliser une connexion Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Graph API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[API REST Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Get User]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[Contrôle d'accès en fonction du rôle avec AAD dans Mobile Services]: mobile-services-javascript-backend-windows-store-dotnet-aad-rbac.md
[blog de l'équipe Azure Active Directory Graph]: http://go.microsoft.com/fwlink/?LinkId=510536
<!--HONumber=54-->