<properties linkid="develop-mobile-tutorials-js-rbac-with-aad" urlDisplayName="Role Based Access Control with Azure Active Directory" pageTitle="Role Based Access Control in Mobile Services and Azure Active Directory (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to control access based on Azure Active Directory roles in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Role Based Access Control in Mobile Services and Azure Active Directory" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/21/2014" ms.author="wesmc"></tags>

# Contrôle d'accès en fonction du rôle dans Mobile Services et Azure Active Directory

<div class="dev-center-tutorial-selector sublanding">
    <a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/" title="Windows Store&nbsp;C#" class="current">Windows Store&nbsp;C#</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/" title=".NET backend">Serveur principal .NET</a> |
    <a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/" title="JavaScript backend" class="current">JavaScript backend</a>
</div>

Le contrôle d'accès en fonction du rôle (RBAC) consiste à affecter des autorisations aux différents rôles assumés par vos utilisateurs, afin de définir des limites en fonction des appartenances. Ce didacticiel vous familiarise avec la procédure d'ajout du contrôle RBAC de base pour Azure Mobile Services.

Ce didacticiel présente le contrôle d'accès en fonction du rôle, qui contrôle l'appartenance de chaque utilisateur à un groupe Sales (Ventes) défini dans Azure Active Directory (AAD). Le contrôle sera effectué avec le projet de service mobile JavaScript principal à l'aide de l'[API Graph][API Graph] pour Azure Active Directory. Seuls les utilisateurs appartenant au rôle Sales seront autorisés à accéder aux données.

> [WACOM.NOTE] L'objectif de ce didacticiel est d'approfondir vos connaissances sur l'authentification pour inclure des pratiques d'autorisation. Vous devez donc au préalable avoir suivi le didacticiel [Prise en main de l'authentification][Prise en main de l'authentification] avec le fournisseur d'authentification Azure Active Directory. Ce didacticiel poursuit la mise à jour de l'application TodoItem utilisée dans le didacticiel [Prise en main de l'authentification][Prise en main de l'authentification].

Ce didacticiel vous familiarise avec la procédure suivante :

1.  [Création d'un groupe Sales avec appartenance][Création d'un groupe Sales avec appartenance]
2.  [Génération d'une clé pour l'application intégrée][Génération d'une clé pour l'application intégrée]
3.  [Ajout d'un script partagé vérifiant l'appartenance][Ajout d'un script partagé vérifiant l'appartenance]
4.  [Ajout du contrôle d'accès en fonction du rôle pour les opérations de base de données][Ajout du contrôle d'accès en fonction du rôle pour les opérations de base de données]
5.  [Test de l'accès client][Test de l'accès client]

Ce didacticiel requiert les éléments suivants :

-   Visual Studio 2013 s'exécutant sous Windows 8.1.
-   Achèvement du didacticiel [Prise en main de l'authentification][Prise en main de l'authentification] à l'aide du fournisseur d'authentification Azure Active Directory
-   Achèvement du didacticiel [Stockage des scripts serveur][Stockage des scripts serveur] afin de maîtriser l'utilisation d'un référentiel Git pour stocker des scripts serveur

## <a name="create-group"></a>Création d'un groupe Sales avec appartenance

[WACOM.INCLUDE [mobile-services-aad-rbac-create-sales-group][mobile-services-aad-rbac-create-sales-group]]

## <a name="generate-key"></a>Génération d'une clé pour l'application intégrée

Avec le didacticiel [Prise en main de l'authentification][Prise en main de l'authentification], vous avez créé une inscription pour l'application intégrée lors de l'étape [Inscription à des fins d'utilisation d'une connexion Azure Active Directory][Inscription à des fins d'utilisation d'une connexion Azure Active Directory]. Dans cette section, vous allez générer la clé devant être utilisée pour lire les informations d'annuaire avec l'ID client de cette application intégrée.

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key][mobile-services-generate-aad-app-registration-access-key]]

## <a name="add-shared-script"></a>Ajout d'un script partagé vérifiant l'appartenance au service mobile

Dans cette section, vous allez utiliser Git pour déployer un fichier de script partagé nommé *rbac.js* à votre service mobile. Ce fichier de script partagé contient les fonctions qui utilisent l'[API Graph][API Graph] pour vérifier l'appartenance au groupe de l'utilisateur.

Si vous débutez avec le déploiement de scripts vers votre service mobile avec Git, lisez le didacticiel [Stockage des scripts serveur][Stockage des scripts serveur] avant de suivre les procédures de cette section.

1.  Créez un fichier de script nommé *rbac.js* dans le répertoire *./service/shared/* du référentiel local de votre service mobile.
2.  En haut du fichier, ajoutez le script suivant qui définit la fonction `getAADToken`. Étant donné le *domaine du locataire*, l'*ID client* de l'application intégrée et la *clé* d'application, cette fonction fournit un jeton d'accès Graph permettant de lire les informations de l'annuaire.

    > [WACOM.NOTE] Vous devez mettre le jeton en cache plutôt qu'en créer un pour chaque contrôle d'accès. Actualisez ensuite le cache lorsqu'une tentative d'utilisation du jeton renvoie une réponse 401 Authentication\_ExpiredToken comme indiqué dans la page de [référence sur les erreurs de l'API Graph][référence sur les erreurs de l'API Graph]. Par souci de simplicité, le code ci-après ne reflète pas cette possibilité, mais cela permet de réduire le trafic réseau supplémentaire vers Active Directory.

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

3.  Ajoutez le code suivant à *rbac.js* pour définir la fonction `getGroupId`. Cette dernière utilise le jeton d'accès pour obtenir l'ID de groupe en fonction du nom de groupe utilisé dans un filtre.

    > [WACOM.NOTE] Ce code recherche le groupe Active Directory par nom. Dans la plupart des cas, il est préférable de stocker l'ID du groupe en tant que paramètre d'application de service mobile et d'utiliser uniquement l'ID de groupe. En effet, le nom de groupe peut changer, mais l'ID reste le même. Cependant, un changement de nom de groupe implique au minimum un changement de la portée du rôle qui nécessite de mettre à jour le code du service mobile.

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

4.  Ajoutez le code suivant à *rbac.js* pour définir la fonction `isMemberOf` qui appelle le point de terminaison [IsMemberOf][IsMemberOf] de l'API REST Graph.

    Cette fonction est un wrapper fin autour du point de terminaison [IsMemberOf][IsMemberOf] de l'API REST Graph. Elle utilise le jeton d'accès Graph pour vérifier si l'ID d'objet d'annuaire de l'utilisateur appartient au groupe selon l'ID de groupe.

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

5.  Ajoutez la fonction `checkGroupMembership` exportée suivante dans *rbac.js*.

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

6.  Enregistrez vos modifications dans *rbac.js*.

## <a name="add-access-checking"></a>Ajout du contrôle d'accès en fonction du rôle pour les opérations de base de données

Lorsque vous avez suivi le didacticiel [Prise en main de l'authentification][Prise en main de l'authentification], vous aviez dû au préalable définir les opérations de table pour qu'elles requièrent une authentification, comme indiqué ci-dessous.

![][]

Avec chaque opération de base de données nécessitant une authentification, vous pouvez ajouter des scripts qui utilisent l'objet utilisateur pour les vérifications d'accès.

La procédure suivante montre comment déployer un contrôle d'accès en fonction du rôle à l'aide de scripts dans chaque opération de table de votre service mobile. Un script qui utilise le script *rbac.js* partagé est exécuté pour chaque opération de table.

1.  Ajoutez un nouveau fichier de script nommé *todoitem.insert.js* dans le répertoire *./service/table/* du référentiel Git local de votre service mobile. Collez le script suivant dans ce fichier.

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

2.  Ajoutez un nouveau fichier de script nommé *todoitem.read.js* dans le répertoire *./service/table/* du référentiel Git local de votre service mobile. Collez le script suivant dans ce fichier.

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

3.  Ajoutez un nouveau fichier de script nommé *todoitem.update.js* dans le répertoire *./service/table/* du référentiel Git local de votre service mobile. Collez le script suivant dans ce fichier.

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

4.  Ajoutez un nouveau fichier de script nommé *todoitem.delete.js* dans le répertoire *./service/table/* du référentiel Git local de votre service mobile. Collez le script suivant dans ce fichier.

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

5.  Dans l'interface de ligne de commande de votre référentiel Git, ajoutez le suivi des fichiers de script que vous avez ajoutés en exécutant la commande suivante.

        git add .

6.  Dans l'interface de ligne de commande de votre référentiel Git, validez les mises à jour en exécutant la commande suivante.

        git commit -m "Added role based access control to table operations"

7.  Dans l'interface de ligne de commande de votre référentiel Git, déployez les mises à jour de votre référentiel Git local vers le service mobile en exécutant la commande suivante. Cette commande suppose que vous avez mis à jour la branche *principale*.

        git push origin master

8.  Dans le [portail de gestion Azure][portail de gestion Azure], vous devez pouvoir accéder aux opérations de table pour le service mobile et afficher les mises à jour, comme indiqué ci-dessous.

    ![][1]

## <a name="test-client"></a>Test de l'accès client

[WACOM.INCLUDE [mobile-services-aad-rbac-test-app][mobile-services-aad-rbac-test-app]]

<!-- Anchors. -->
<!-- Images -->
<!-- URLs. -->

  [Windows Store C\#]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/ "Windows Store C#"
  [Serveur principal .NET]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/ ".NET backend"
  [JavaScript backend]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/ "JavaScript backend"
  [API Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [Création d'un groupe Sales avec appartenance]: #create-group
  [Génération d'une clé pour l'application intégrée]: #generate-key
  [Ajout d'un script partagé vérifiant l'appartenance]: #add-shared-script
  [Ajout du contrôle d'accès en fonction du rôle pour les opérations de base de données]: #add-access-checking
  [Test de l'accès client]: #test-client
  [Stockage des scripts serveur]: /fr-fr/documentation/articles/mobile-services-store-scripts-source-control/
  [mobile-services-aad-rbac-create-sales-group]: ../includes/mobile-services-aad-rbac-create-sales-group.md
  [Inscription à des fins d'utilisation d'une connexion Azure Active Directory]: /fr-fr/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [mobile-services-generate-aad-app-registration-access-key]: ../includes/mobile-services-generate-aad-app-registration-access-key.md
  [référence sur les erreurs de l'API Graph]: http://msdn.microsoft.com/en-us/library/azure/hh974480.aspx
  [IsMemberOf]: http://msdn.microsoft.com/en-us/library/azure/dn151601.aspx
  []: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/table-perms.png
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/insert-table-op-view.png
  [mobile-services-aad-rbac-test-app]: ../includes/mobile-services-aad-rbac-test-app.md
