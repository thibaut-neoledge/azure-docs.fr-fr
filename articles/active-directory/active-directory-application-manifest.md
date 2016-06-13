<properties
   pageTitle="Connaître le manifeste d’application Azure Active Directory | Microsoft Azure"
   description="Présentation détaillée de l’utilisation du manifeste d’application Azure Active Directory, qui représente la configuration d’identité d’une application dans un locataire Azure AD et permet de faciliter l’autorisation OAuth, le consentement et bien plus encore."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/26/2016"
   ms.author="dkershaw;bryanla"/>

# Connaître le manifeste d’application Azure Active Directory

Les applications qui s’intègrent à Azure Active Directory (AD) doivent être inscrites auprès d’Azure AD, fournissant une configuration permanente d’identité pour l’application. Cette configuration est consultée lors de l’exécution, activant des scénarios qui permettent à une application d’externaliser et d’assurer le courtage de l’authentification/autorisation via Azure AD. Pour plus d’informations sur le modèle d’application Azure AD, consultez l’article [Ajout, mise à jour et suppression d’une application][ADD-UPD-RMV-APP].

## Mise à jour d’une configuration de l’identité d’une application

Il existe en fait plusieurs options pour mettre à jour les propriétés de configuration de l’identité d’une application dont les capacités et le degré de difficulté varient, notamment les suivantes :

- L’**interface utilisateur web du [portail Azure Classic][AZURE-CLASSIC-PORTAL]** vous permet de mettre à jour les propriétés les plus courantes d’une application. Il s’agit de la méthode la plus rapide et la plus susceptible de mettre à jour les propriétés de votre application, mais elle ne pas vous donne un accès complet à toutes les propriétés, au même titre que les deux méthodes suivantes.
- Pour des scénarios plus avancés nécessitant la mise à jour de propriétés qui ne sont pas exposées dans le portail Azure Classic, vous pouvez modifier le **manifeste d’application**. C’est le sujet qui est abordé dans cet article et est décrit en détail à partir de la section suivante.
- Il est également possible d’**écrire une application qui utilise l’[API Graph][GRAPH-API]** pour mettre à jour votre application, ce qui demande le plus d’effort. Cette option peut être malgré tout intéressante, si vous écrivez des logiciels de gestion ou si vous devez régulièrement mettre à jour les propriétés de l’application de manière automatique.

## Utilisation du manifeste d’application pour la mise à jour de la configuration de l’identité d’une application
Par le biais du [portail Azure Classic][AZURE-CLASSIC-PORTAL], vous pouvez gérer la configuration de l’identité de votre application, en téléchargeant et chargeant un fichier de représentation JSON nommé manifeste d’application. Aucun fichier réel n’est stocké dans le répertoire - le manifeste d’application est simplement une opération HTTP GET sur l’entité d’application API Azure AD Graph et le téléchargement est une opération HTTP PATCH sur l’entité de l’application.

Par conséquent, pour comprendre le format et les propriétés du manifeste d’application, vous devez référencer la documentation associée à l’[entité d’Application][APPLICATION-ENTITY] API Graph. Les exemples de mises à jour pouvant être effectuées via le manifeste d’application sont les suivants :

- **Déclaration des étendues d’autorisation (oauth2Permissions)** exposées par votre API web. Consultez la rubrique « Exposition des API de web pour les autres applications » dans [Intégration des applications avec Azure Active Directory][INTEGRATING-APPLICATIONS-AAD] pour plus d’informations sur l’implémentation de l’emprunt d’identité utilisateur à l’aide de la portée d’autorisation déléguée oauth2Permissions. Comme mentionné précédemment, toutes les propriétés d’entité d’application sont documentées dans l’article de référence [Entité et référence de type complexe][APPLICATION-ENTITY] sur l’API Graph, notamment la propriété oauth2Permissions, qui est une collection de type [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION].
- **Déclaration des rôles d’application (appRoles) exposées par votre application**. La propriété appRole de l’entité d’applications est une collection de type [AppRole][APPLICATION-ENTITY-APP-ROLE]. Consultez le [contrôle d’accès basé sur les rôles dans les applications de cloud avec Azure AD][RBAC-CLOUD-APPS-AZUREAD] pour obtenir un exemple d’implémentation.
- **Déclaration des applications clientes connues (knownClientApplications)**, ce qui vous permet de lier logiquement le consentement des applications clientes spécifiées à l’API web/la ressource.
- **Demander à Azure AD d’émettre une revendication d’appartenance au groupe** pour l’utilisateur connecté (groupMembershipClaims). REMARQUE : elle peut être configurée pour émettre des revendications au sujet des candidatures de rôle de répertoire Active Directory. Consultez l’article [Autorisation dans les applications de Cloud à l’aide de groupes Active Directory][AAD-GROUPS-FOR-AUTHORIZATION] pour obtenir un exemple d’implémentation.
- **Permettre à votre application de prendre en charge des flux d’octroi implicites OAuth 2.0** (oauth2AllowImplicitFlow). Ce type de flux d’octroi est utilisé avec les pages web JavaScript incorporées ou les applications SPA (Single Page Applications).
- **Activer l’utilisation de certificats X509 en tant que clé secrète** (keyCredentials). Consultez l’article [Créer des applications de démon et de service dans Office 365][O365-SERVICE-DAEMON-APPS] et le [ Guide des développeurs sur Auth avec l’API d’Azure Resource Manager][DEV-GUIDE-TO-AUTH-WITH-ARM] pour obtenir des exemples d’implémentation.
- **Ajouter un nouvel URI d’ID d’application** pour votre application (identifierURI). Les URI d’ID d’application sont utilisés pour identifier de manière unique une application dans son locataire Azure AD (ou sur plusieurs locataires Azure AD, pour les scénarios multi-locataires qualifiés via un domaine personnalisé vérifié). Ils sont utilisés lors de la demande d’autorisations d’accès à une application de ressources ou de l’acquisition d’un jeton d’accès pour une application de ressources. Lorsque vous mettez à jour cet élément, la même mise à jour est effectuée vers la collection servicePrincipalNames du principal de service correspondant, qui se trouve dans le client de base de l’application.

Le manifeste d’application constitue également un bon moyen de suivre l’état de l’enregistrement de l’application. Le fichier étant disponible au format JSON, la représentation du fichier peut être intégrée à votre contrôle de code source, avec le code source de votre application.

## Exemple étape par étape
Maintenant, examinons les opérations requises pour mettre à jour la configuration de l’identité de votre application dans le manifeste d’application. Nous allons mettre en avant un des exemples ci-dessus en vous montrant comment déclarer une nouvelle étendue d’autorisation sur une application de ressource :

1. Accédez au [Portail Azure Classic][AZURE-CLASSIC-PORTAL] et connectez-vous avec un compte disposant des privilèges administrateur ou coadministrateur de service.

2. Une fois l’authentification effectuée, faites défiler et sélectionnez l’extension Azure « Active Directory » dans le panneau de navigation de gauche (1), puis cliquez sur le locataire Azure AD dans lequel votre application est enregistrée (2).

    ![Sélectionnez le locataire Azure AD][SELECT-AZURE-AD-TENANT]

3. Lorsque la page de répertoire apparaît, cliquez sur « Applications » (1) en haut de la page pour afficher la liste des applications enregistrées dans le locataire. Recherchez ensuite l’application que vous souhaitez mettre à jour dans la liste, puis cliquez sur (2).

    ![Sélectionnez le locataire Azure AD][SELECT-AZURE-AD-APP]

4. Maintenant que vous avez sélectionné la page principale de l’application, notez la fonctionnalité « Gérer manifeste » en bas de la page (1). Si vous cliquez sur ce lien, vous serez invité à charger ou à télécharger le fichier manifeste de JSON. Un clic sur « Télécharger le manifeste » (2) sera immédiatement suivi de la boîte de dialogue vous invitant à confirmer l’opération en cliquant sur « Télécharger le manifeste » (3), puis ouvrez ou enregistrez le fichier en local (4).

    ![Gérer le manifeste, option de chargement][MANAGE-MANIFEST-DOWNLOAD]

    ![Télécharger le manifeste][DOWNLOAD-MANIFEST]

5. Dans cet exemple, nous avons enregistré le fichier localement, ce qui nous a permis de l’ouvrir un éditeur, apporté des modifications à JSON et de nouveau enregistré. Voici à quoi ressemble la structure JSON à l’intérieur de l’éditeur Visual Studio JSON. Notez qu’il suit le schéma de l’[entité d’application][APPLICATION-ENTITY] comme nous l’avons mentionné précédemment :

    ![Mettre à jour le manifeste JSON][UPDATE-MANIFEST]

    Par exemple, partons du principe que nous voulons implémenter/exposer une nouvelle autorisation appelée « Employees.Read.All » pour notre application de ressource (API). Dans ce cas, vous devez simplement ajouter un deuxième ou nouvel élément à la collection oauth2Permissions, par exemple :

        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],

    L’entrée doit être unique. Vous devez donc générer un nouveau GUID pour la propriété `"id"`. Dans ce cas, étant donné que nous avons spécifié `"type": "User"`, cette autorisation peut être consentie par n’importe quel compte authentifié par le locataire Azure AD dans lequel l’application d’API/de ressource est inscrite, en octroyant à l’application cliente l’autorisation d’accès au nom du compte. Les chaînes de nom d’affichage et de description sont utilisées durant le consentement et pour l’affichage dans le portail Azure Classic.

6. Une fois le manifeste mis à jour, revenez à la page d’application Azure AD du portail Azure Classic, cliquez de nouveau sur la fonction « Gérer le manifeste » (1), mais cette fois, sélectionnez l’option « Télécharger le manifeste » (2). Comme pour le téléchargement, vous serez accueilli à nouveau par une deuxième boîte de dialogue vous invitant à spécifier l’emplacement du fichier JSON. Cliquez sur « Rechercher le fichier... » (3), puis utilisez la boîte de dialogue « Choisir le fichier à télécharger » pour sélectionner le fichier JSON (4), puis appuyez sur « Ouvrir ». Lorsque la boîte de dialogue disparaît, activez la case à cocher « OK » (5) pour télécharger votre manifeste.

    ![Gérer le manifeste, option de téléchargement][MANAGE-MANIFEST-UPLOAD]

    ![Télécharger le manifeste JSON][UPLOAD-MANIFEST]

    ![Télécharger le manifeste JSON - confirmation][UPLOAD-MANIFEST-CONFIRM]

Maintenant que le manifeste est enregistré, vous pouvez octroyer à une application cliente inscrite un accès à la nouvelle autorisation ajoutée ci-dessus. Mais cette fois, vous pouvez utiliser l’interface utilisateur web du portail Azure Classic au lieu de modifier le manifeste de l’application cliente :

1. Tout d’abord, accédez à la page Configurer de l’application cliente à laquelle vous souhaitez ajouter l’accès à la nouvelle API, puis cliquez sur le bouton Ajouter l’application.
2. La liste des applications de ressource (API) inscrites s’affiche dans le locataire. Cliquez sur le signe plus/+ en regard du nom de l’application de ressource pour la sélectionner.  
3. Cliquez ensuite sur la coche dans la partie inférieure droite.
4. Lorsque vous revenez à la section Ajouter l’application de la page de configuration de votre client, la nouvelle application de ressource apparaît dans la liste. Si vous pointez sur la section Autorisations déléguées à droite de cette ligne, une liste déroulante s’affiche. Cliquez sur la liste, puis sélectionnez la nouvelle autorisation pour l’ajouter à la liste d’autorisations demandée par le client. Remarque : cette nouvelle autorisation est stockée dans la configuration d’identité de l’application cliente, dans la propriété de collection requiredResourceAccess.

![Autorisations pour d'autres applications][PERMS-TO-OTHER-APPS]

![Autorisations pour d'autres applications][PERMS-SELECT-APP]

![Autorisations pour d'autres applications][PERMS-SELECT-PERMS]

Vous avez terminé. Vos applications s’exécutent désormais à l’aide de leur nouvelle configuration d’identité.

## Étapes suivantes
Utilisez la section commentaires DISQUS ci-dessous pour fournir des commentaires et nous aider à affiner et à mettre en forme notre contenu.

<!--Image references-->
[DOWNLOAD-MANIFEST]: ./media/active-directory-application-manifest/download-manifest.png
[MANAGE-MANIFEST-DOWNLOAD]: ./media/active-directory-application-manifest/manage-manifest-download.png
[MANAGE-MANIFEST-UPLOAD]: ./media/active-directory-application-manifest/manage-manifest-upload.png
[PERMS-SELECT-APP]: ./media/active-directory-application-manifest/portal-perms-select-app.png
[PERMS-SELECT-PERMS]: ./media/active-directory-application-manifest/portal-perms-select-perms.png
[PERMS-TO-OTHER-APPS]: ./media/active-directory-application-manifest/portal-perms-to-other-apps.png
[SELECT-AZURE-AD-APP]: ./media/active-directory-application-manifest/select-azure-ad-application.png
[SELECT-AZURE-AD-TENANT]: ./media/active-directory-application-manifest/select-azure-ad-tenant.png
[UPDATE-MANIFEST]: ./media/active-directory-application-manifest/update-manifest.png
[UPLOAD-MANIFEST]: ./media/active-directory-application-manifest/upload-manifest.png
[UPLOAD-MANIFEST-CONFIRM]: ./media/active-directory-application-manifest/upload-manifest-confirm.png

<!--article references -->
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#ApplicationEntity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#AppRoleType
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionType
[AZURE-CLASSIC-PORTAL]: https://manage.windowsazure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

<!---HONumber=AcomDC_0601_2016-->