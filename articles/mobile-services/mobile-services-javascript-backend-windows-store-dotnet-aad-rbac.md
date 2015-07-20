<properties 
	pageTitle="Contrôle d'accès en fonction du rôle dans Mobile Services et Azure Active Directory (Windows Store) | Centre de développement mobile" 
	description="Découvrez comment contrôler l'accès en fonction des rôles Azure Active Directory dans votre application Windows Store." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="wesmc"/>

# Contrôle d'accès en fonction du rôle dans Mobile Services et Azure Active Directory

[AZURE.INCLUDE [mobile-services-selector-rbac](../../includes/mobile-services-selector-rbac.md)]

#Vue d'ensemble

Le contrôle d'accès en fonction du rôle (RBAC) consiste à affecter des autorisations aux différents rôles assumés par vos utilisateurs, afin de définir des limites en fonction des appartenances. Ce didacticiel vous montre comment ajouter le contrôle RBAC de base à Azure Mobile Services.

Ce didacticiel présente le contrôle d'accès en fonction du rôle, qui contrôle l'appartenance de chaque utilisateur à un groupe Sales (Ventes) défini dans Azure Active Directory (AAD). Le contrôle sera effectué avec le projet de backend JavaScript à l'aide de l'[API Graph] pour Azure Active Directory. Seuls les utilisateurs appartenant au rôle Sales seront autorisés à accéder aux données.


>[AZURE.NOTE]L'objectif de ce didacticiel est d'approfondir vos connaissances sur l'authentification pour inclure des pratiques d'autorisation. Vous devez donc au préalable avoir suivi le didacticiel [Ajout de l'authentification à votre application Mobile Service] avec le fournisseur d'authentification Azure Active Directory. Ce didacticiel continue de mettre à jour l'application TodoItem utilisée dans le didacticiel [Ajout de l'authentification à votre application Mobile Service].

##Configuration requise

Ce didacticiel requiert les éléments suivants :

* Visual Studio 2013 s'exécutant sous Windows 8.1.
* Achèvement du didacticiel [Ajout de l'authentification à votre application] en utilisant le fournisseur d'authentification Azure Active Directory
* Achèvement du didacticiel [Stockage des scripts serveur] afin de maîtriser l'utilisation d'un référentiel Git pour stocker des scripts serveur
 


##Création d'un groupe Sales avec appartenance

[AZURE.INCLUDE [mobile-services-aad-rbac-create-sales-group](../../includes/mobile-services-aad-rbac-create-sales-group.md)]


##Génération d'une clé pour l'application intégrée


Dans le cadre du didacticiel [Ajout de l'authentification à votre application Mobile Service], vous avez créé une inscription pour l'application intégrée à l'étape [Inscription pour utiliser une connexion Azure Active Directory]. Dans cette section, vous allez générer la clé devant être utilisée pour lire les informations d'annuaire avec l'ID client de cette application intégrée.

Si vous êtes arrivé au terme du didacticiel [Accès aux informations Azure Active Directory Graph], vous avez déjà effectué cette étape et vous pouvez ignorer cette section.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]





##Ajout d'un script partagé vérifiant l'appartenance au service mobile

Dans cette section, vous allez utiliser Git pour déployer un fichier de script partagé nommé *rbac.js* à votre service mobile. Ce fichier de script partagé contient les fonctions qui utilisent l'[API Graph] pour vérifier l'appartenance au groupe de l'utilisateur.

Si vous débutez avec le déploiement de scripts vers votre service mobile avec Git, lisez le didacticiel [Stockage des scripts serveur] avant de suivre les procédures de cette section.

1. Créez un fichier de script nommé *rbac.js* dans le répertoire *./service/shared/* du référentiel local de votre service mobile.
2. En haut du fichier, ajoutez le script suivant qui définit la fonction `getAADToken`. Étant donné le *domaine du locataire*, l'*ID client* de l'application intégrée et la *clé* d'application, cette fonction fournit un jeton d'accès Graph permettant de lire les informations de l'annuaire.

    >[AZURE.NOTE]Vous devez mettre en cache le jeton au lieu d'en créer un nouveau à chaque vérification d'accès. Actualisez ensuite le cache lorsqu'une tentative d'utilisation du jeton renvoie une réponse 401 Authentication_ExpiredToken comme indiqué dans la page de [référence sur les erreurs de l'API Graph]. Par souci de simplicité, le code ci-après ne reflète pas cette possibilité, mais cela permet de réduire le trafic réseau supplémentaire vers Active Directory.

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;
        exports.SalesGroup = appSettings.AAD_GROUP_ID;
         
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


3. Ajoutez le code suivant à *rbac.js* pour définir la fonction `getGroupId`. Cette dernière utilise le jeton d'accès pour obtenir l'ID de groupe en fonction du nom de groupe utilisé dans un filtre.
 
    >[AZURE.NOTE]Ce code recherche le groupe Active Directory par nom. Il peut être souvent judicieux d'enregistrer l'ID du groupe en tant que paramètre d'application de service mobile et d'utiliser simplement cet ID. Le nom du groupe peut en effet changer, mais l'ID reste toujours le même. Cependant, un changement de nom de groupe implique au minimum un changement de la portée du rôle qui nécessite de mettre à jour le code du service mobile.

        function getGroupId(groupname, accessToken, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/groups" + 
                      "?$filter=displayName%20eq%20'" + groupname + "'" + 
	              "&api-version=2013-04-05" ,
                method: 'GET',
                headers: {
                    "Authorization": "Bearer " + accessToken,
                    "Content-Type": "application/json",
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).value[0].objectId);
            });
        }


4. Ajoutez le code suivant à *rbac.js* pour définir la fonction `isMemberOf` qui appelle le point de terminaison [IsMemberOf] de l'API REST Graph.

    Cette fonction est un wrapper fin autour du point de terminaison [IsMemberOf] de l'API REST Graph. Elle utilise le jeton d'accès Graph pour vérifier si l'ID d'objet d'annuaire de l'utilisateur appartient au groupe selon l'ID de groupe.

        function isMemberOf(access_token, userObjectId, groupObjectId, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/isMemberOf" + "?api-version=2013-04-05",
                method: 'POST',
                headers: {
                    "Authorization": "Bearer " + access_token,
                    "Content-Type": "application/json",
                },
                body: JSON.stringify({
                "groupId": groupObjectId,
                "memberId": userObjectId
                })
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).value);
            });
        };

    

7. Ajoutez la fonction `checkGroupMembership` exportée suivante dans *rbac.js*.

    Cette fonction encapsule l'utilisation des autres fonctions de script. Elle est exportée à partir du script partagé pour être appelée par d'autres scripts pour réaliser les véritables vérifications d'accès. Étant donné l'objet utilisateur du service mobile, et l'ID de groupe, le script extrait l'ID d'objet Azure Active Directory pour l'identité de l'utilisateur et vérifie l'appartenance au groupe.

        exports.checkGroupMembership = function (user, groupName, callback) {
            user.getIdentities({
                success: function (identities) {
                    var objectId = identities.aad.oid;
                    getAADToken(function (err, access_token) {
                        if (err) callback(err);
		        else getGroupId(groupName, access_token, function (err, groupId) { 
                            if (err) callback(err);
                            else isMemberOf(access_token, objectId, groupId, function (err, isInGroup) {
       	                        if (err) errorHandler(err);
               	                else callback(null, isInGroup);
                            });
                        });
                    });
                },
                error: callback
            });
        }

8. Enregistrez vos modifications dans *rbac.js*.

##Ajout du contrôle d'accès en fonction du rôle pour les opérations de base de données


Dans le cadre du didacticiel [Ajout de l'authentification à votre application Mobile Service], vous avez déjà défini les opérations de table pour exiger une authentification comme indiqué ci-dessous.

![][3]

Avec chaque opération de base de données nécessitant une authentification, vous pouvez ajouter des scripts qui utilisent l'objet utilisateur pour les vérifications d'accès.

La procédure suivante montre comment déployer un contrôle d'accès en fonction du rôle à l'aide de scripts dans chaque opération de table de votre service mobile. Un script qui utilise le script *rbac.js* partagé est exécuté pour chaque opération de table.

1. Ajoutez un nouveau fichier de script nommé *todoitem.insert.js* dans le répertoire *./service/table/* du référentiel Git local de votre service mobile. Collez le script suivant dans ce fichier.

        function insert(item, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

2. Ajoutez un nouveau fichier de script nommé *todoitem.read.js* dans le répertoire *./service/table/* du référentiel Git local de votre service mobile. Collez le script suivant dans ce fichier.

        function read(query, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

3. Ajoutez un nouveau fichier de script nommé *todoitem.update.js* dans le répertoire *./service/table/* du référentiel Git local de votre service mobile. Collez le script suivant dans ce fichier.

        function update(item, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

4. Ajoutez un nouveau fichier de script nommé *todoitem.delete.js* dans le répertoire *./service/table/* du référentiel Git local de votre service mobile. Collez le script suivant dans ce fichier.

        function del(id, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

5. Dans l'interface de ligne de commande de votre référentiel Git, ajoutez le suivi des fichiers de script que vous avez ajoutés en exécutant la commande suivante.

        git add .

6. Dans l'interface de ligne de commande de votre référentiel Git, validez les mises à jour en exécutant la commande suivante.

        git commit -m "Added role based access control to table operations"
  
7. Dans l'interface de ligne de commande de votre référentiel Git, déployez les mises à jour de votre référentiel Git local vers le service mobile en exécutant la commande suivante. Cette commande suppose que vous avez mis à jour la branche *principale*.

        git push origin master

8. Dans le [portail de gestion Azure], vous devez pouvoir accéder aux opérations de table pour le service mobile et afficher les mises à jour, comme indiqué ci-dessous.

    ![][4]

##Test de l'accès client

[AZURE.INCLUDE [mobile-services-aad-rbac-test-app](../../includes/mobile-services-aad-rbac-test-app.md)]







<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/users.png
[1]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/group-membership.png
[2]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/sales-group.png
[3]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/table-perms.png
[4]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/insert-table-op-view.png
[5]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/sales-group-id.png
[6]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/client-id-and-key.png

<!-- URLs. -->
[Ajout de l'authentification à votre application]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md
[Ajout de l'authentification à votre application Mobile Service]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[portail de gestion Azure]: https://manage.windowsazure.com/
[Directory Sync Scenarios]: http://msdn.microsoft.com/library/azure/jj573653.aspx
[Stockage des scripts serveur]: mobile-services-store-scripts-source-control.md
[Inscription pour utiliser une connexion Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[API Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[référence sur les erreurs de l'API Graph]: http://msdn.microsoft.com/library/azure/hh974480.aspx
[IsMemberOf]: http://msdn.microsoft.com/library/azure/dn151601.aspx
[Accès aux informations Azure Active Directory Graph]: mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info.md
<!--HONumber=54--> 