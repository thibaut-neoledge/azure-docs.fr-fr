---
title: 'Azure Active Directory B2C : approches de migration utilisateur'
description: "Abordez les principaux concepts et les concepts avancés de la migration utilisateur avec API Graph et éventuellement avec les stratégies personnalisées Azure AD B2C."
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/04/2017
ms.author: yoelh
ms.openlocfilehash: b102edd997e69fb3568780a42dfe93fc9a90e332
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C : migration utilisateur
Lorsque vous envisagez de migrer votre fournisseur d’identité vers Azure AD B2C, vous devrez peut-être migrer le compte des utilisateurs également. Cet article explique comment migrer des comptes d’utilisateur existants de n’importe quel fournisseur d’identité vers Azure AD B2C. Cet article n’a pas vocation à être normatif, mais cherche plutôt à décrire deux approches différentes parmi les nombreuses qui existent.  Le développeur est responsable du choix de l’approche adaptée.

## <a name="user-migration-flows"></a>Flux de migration utilisateur
Azure AD B2C vous permet de migrer des utilisateurs via [Graph API](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet). Le processus de migration utilisateur se divise en deux flux :

* **Prémigration** : ce flux est adapté lorsque vous avez accès aux informations d’identification utilisateur (nom d’utilisateur et mot de passe) dans un texte en clair. Ou lorsque les informations d’identification sont chiffrées et que vous ne parvenez pas à les déchiffrer. Le processus comprend les étapes suivantes : la lecture des utilisateurs de l’ancien fournisseur d’identité et la création de comptes dans le répertoire Azure AD B2C.

* **Prémigration et réinitialisation du mot de passe** : ce flux est adapté lorsque le mot de passe de l’utilisateur n’est pas accessible. Par exemple :
    * Quand les mots de passe sont stockés au format HASH.
    * Quand les mots de passe sont stockés dans un fournisseur d’identité auquel vous n’avez pas accès. Votre ancien fournisseur d’identité valide les informations d’identification utilisateur en appelant un service web.

Dans les deux flux, vous devez tout d’abord exécuter le processus de __prémigration__, lire les utilisateurs de votre ancien fournisseur d’identité et créer des comptes dans un répertoire Azure AD B2C. Si vous n’avez pas le mot de passe, vous créez le compte avec un mot de passe aléatoire que vous générez. Vous demandez aux utilisateurs de modifier leur mot de passe. Ou, lorsque l’utilisateur se connecte pour la première fois, B2C lui demande de réinitialiser le mot de passe.

## <a name="password-policy"></a>Stratégie de mot de passe
La stratégie de mot de passe Azure AD B2C (pour les comptes locaux) est basée sur la stratégie Azure AD. Les stratégies de réinitialisation du mot de passe, d’inscription ou de connexion Azure AD B2C utilisent des mots de passe « forts » et qui n’expirent pas. Pour plus d’informations, consultez [Stratégie de mot de passe dans Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx) .

Si les comptes que vous souhaitez migrer ont un mot de passe moins fort que les [règles de mot de passe fort d’Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), vous pouvez désactiver la condition de mot de passe fort. Pour modifier la stratégie de mot de passe par défaut, définissez la propriété `passwordPolicies` sur `DisableStrongPassword`. Par exemple, vous pouvez modifier la demande de création d’utilisateur comme suit : 

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-using-graph-api-to-migrate-users"></a>Étape 1 : Utilisation d’API Graph pour migrer des utilisateurs
Vous créez le compte d’utilisateur Azure AD B2C via l’API Graph (avec le mot de passe ou avec le mot de passe aléatoire). Cette section décrit le processus de création de comptes d’utilisateur dans le répertoire Azure AD B2C, à l’aide de l’API Graph.

### <a name="step-11-register-your-application-in-your-tenant"></a>Étape 1.1 Inscription de votre application dans votre locataire
Pour communiquer avec l’API Graph, vous devez d’abord disposer d’un compte de service avec des privilèges Administrateur. Dans Azure AD, vous inscrivez une application et l’authentifiez auprès d’Azure AD. Les informations d’identification de l’application sont : **ID de l’application**  et **Secret de l’application**. L’application joue son propre rôle, et non celui d’un utilisateur, pour appeler l’API Graph.

Tout d’abord, inscrivez votre application de migration dans Azure AD. Puis, créez une clé d’application (secret de l’application) et configurez l’application avec les privilèges adéquats.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Choisissez votre locataire Azure AD **B2C** en sélectionnant votre compte dans le coin supérieur droit de la page.
3. Dans le panneau gauche, cliquez sur **Azure Active Directory** (pas sur Azure AD B2C). Vous devrez peut-être sélectionner **Plus de services** pour le trouver.
4. Sélectionnez **Inscriptions d’applications**.
5. Cliquez sur **Nouvelle inscription d’application**.

    ![Nouvelle inscription d’application](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)
6. Suivez les invites et créez une application.
    * Pour **Nom**, utilisez **B2CUserMigratioin**, ou n’importe quel nom de votre choix.
    * Pour **Type d’application**, utilisez **Application/API web**.
    * Pour **URL de connexion**, utilisez https://localhost (car cela n’est pas pertinent pour cette application).
    * Cliquez sur **Créer**.
7. Une fois que l’application est créée, sélectionnez l’application nouvellement créée **B2CUserMigratioin** dans la liste des applications.
Sélectionnez **Propriétés**, copiez **l’ID de l’application** et enregistrez-le pour une utilisation ultérieure.

### <a name="step-12-create-application-secret"></a>Étape 1.2 Création d’un secret d’application
8. Continuons dans l’application inscrite du portail Azure. Cliquez sur **Clés** et ajoutez une clé (également appelée clé secrète client). Copiez également la clé pour une utilisation ultérieure.

    ![ID de l’application et clés](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)

### <a name="step-13-grant-administrative-permission-to-your-application"></a>Étape 1.3 Octroi des autorisations d’administration à votre application
9. Continuons dans **l’application inscrite** du portail Azure.
10. Cliquez sur **Autorisations requises**.
11. Cliquez sur **Windows Azure Active Directory**.
12. Dans **Activer l’accès**, sous **Autorisations d’application**, sélectionnez **l’autorisation Accéder en lecture et en écriture aux données de l’annuaire** et cliquez sur **Enregistrer**.
13. Enfin, dans le menu **Autorisations requises**, cliquez sur le bouton **Accorder des autorisations**.

    ![Autorisations de l’application](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)

Vous disposez maintenant d’une application autorisée à créer, lire et mettre à jour des utilisateurs de votre locataire B2C.

### <a name="step-14-optional-environment-cleanup"></a>Étape 1.4 [facultative] Nettoyage de l’environnement
L’autorisation Accéder en lecture et en écriture aux données de l’annuaire n’inclut PAS la possibilité de supprimer des utilisateurs. Si vous souhaitez donner à votre application la possibilité de supprimer des utilisateurs (pour nettoyer votre environnement), vous devez effectuer une étape supplémentaire. Celle-ci implique l’exécution de PowerShell pour définir les autorisations __Administrateur des comptes d’utilisateur__. Si cela ne vous semble pas utile, vous pouvez passer à la section suivante.


> [!IMPORTANT]
> Vous devez utiliser un compte d’administrateur de locataire B2C qui est **local** pour le locataire B2C. Ces comptes se présentent comme suit : admin@contosob2c.onmicrosoft.com.

>[!NOTE]
> Le script PowerShell suivant nécessite [Azure Active Directory PowerShell **Version 2**](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).

Dans le script PowerShell suivant :
* Connectez-vous à votre service en ligne. Pour ce faire, exécutez la cmdlet `Connect-AzureAD` à l’invite de commandes Windows PowerShell et indiquez vos informations d’identification. 
* Utilisez **l’ID de l’application** pour assigner à l’application le rôle d’administrateur de compte utilisateur. Comme ces rôles ont des identificateurs bien connus, il vous suffit d’entrer **l’ID de votre application** dans le script.

```PowerShell
Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

Modifiez la valeur `$AppId` en la remplaçant par **l’ID de votre application** Azure AD.

## <a name="step-2-pre-migration-application-sample"></a>Étape 2 : Exemple d’application de prémigration
Téléchargez l’exemple de code et exécutez-le. Vous pouvez [télécharger l’exemple de code en tant que fichier .zip](http://www.github.com).

### <a name="step-21-edit-the-migration-data-file"></a>Étape 2.1 Modification du fichier de données de migration
L’exemple d’application utilise un fichier JSON qui contient des données d’utilisateurs factices. Après avoir correctement exécuté l’exemple, modifiez le code pour utiliser des données de votre propre base de données. Vous pouvez aussi exporter le profil utilisateur vers un fichier JSON et configurer l’application pour qu’elle utilise ce fichier.
Pour modifier le fichier JSON, ouvrez la solution Visual Studio `AADB2C.UserMigration.sln`. Dans le projet `AADB2C.UserMigration`, ouvrez le fichier `UsersData.json`.


![Fichier de données utilisateur](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Comme vous pouvez le voir, le fichier contient une liste d’entités d’utilisateur. Chaque entité d’utilisateur a :
* email
* displayName
* firstName
* lastName
* un mot de passe (peut être vide)

> [!NOTE]
> Au moment de la compilation, Visual Studio copie le fichier dans le répertoire `bin`.

### <a name="step-22-configure-the-application-settings"></a>Étape 2.2 Configuration des paramètres de l’application
Dans le projet `AADB2C.UserMigration`, ouvrez le fichier App.config. Remplacez les paramètres suivants de l’application par vos propres valeurs :

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users data e.g. UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure Table string}" />
    
</appSettings>
```

> [!NOTE]
> * L’utilisation de la chaîne de connexion Table Azure est décrite plus loin dans les sections suivantes.
> * Le nom de votre locataire B2C est le domaine que vous avez entré lors de la création du locataire, et il est affiché dans le portail Azure. Il se termine généralement par le suffixe `.onmicrosoft.com`, par exemple, `contosob2c.onmicrosoft.com`.
>

### <a name="step-23-run-the-pre-migration-process"></a>Étape 2.3 Exécution du processus de prémigration
Cliquez avec le bouton droit sur la solution `AADB2C.UserMigration` et générez à nouveau l’exemple. Si l’opération aboutit, vous devez maintenant disposer d’un fichier exécutable `UserMigration.exe` dans le répertoire `AADB2C.UserMigration\bin\Debug`. Pour exécuter le processus de migration, utilisez l’un des paramètres de ligne de commande suivants :

* Pour **migrer des utilisateurs avec un mot de passe**, utilisez la commande `UserMigration.exe 1`.

* Pour **migrer des utilisateurs avec un mot de passe aléatoire**, utilisez la commande `UserMigration.exe 2`. Cette opération crée également une entité Table Azure. Vous configurerez ultérieurement la stratégie permettant d’appeler le service d’API REST. Le service utilise l’entité Table Azure pour suivre et gérer le processus de migration.

![Démonstration du processus de migration](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Étape 2.4 Vérification du processus de prémigration
Pour vérifier le résultat, dans le portail Azure, ouvrez **Azure AD B2C** et cliquez sur **Utilisateurs ou groupes**. Dans la zone de recherche, tapez le nom d’affichage de l’un des utilisateurs et consultez le profil utilisateur. Vous pouvez également utiliser cet exemple d’application pour récupérer les utilisateurs par **adresse e-mail de connexion**. Pour rechercher un utilisateur par adresse e-mail de connexion, exécutez la commande suivante.

```Console
UserMigration.exe 3 {email address}
```

Vous pouvez également enregistrer la sortie sur un fichier JSON, comme suit :
```Console
UserMigration.exe 3 {email address} >> UserProfile.json
```


Ouvrez le fichier UserProfile.json avec l’éditeur JSON de votre choix. Avec Visual Studio Code, vous pouvez mettre en forme un document JSON avec `Shift+Alt+F` ou l’option Mettre le document en forme dans le menu contextuel.

![Profil utilisateur JSON](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

Vous pouvez également récupérer les utilisateurs par **nom d’affichage** à l’aide de la commande `UserMigration.exe 4 <Display name>`.

### <a name="step-25-optional-environment-cleanup"></a>Étape 2.5 [facultative] Nettoyage de l’environnement
Si vous souhaitez nettoyer votre locataire Azure AD et supprimer les utilisateurs de votre répertoire Azure AD, exécutez la commande `UserMigration.exe 5`.

> [!NOTE]
> * Pour nettoyer votre locataire, configurez les autorisations __Administrateur des comptes d’utilisateur__ pour votre application.
> * L’exemple d’application de migration nettoie tous les utilisateurs répertoriés dans le fichier JSON

### <a name="step-26-sing-in-with-migrated-users-with-password"></a>Étape 2.6 Connexion avec des utilisateurs migrés (avec un mot de passe)
Après avoir exécuté le processus de prémigration avec le mot de passe des utilisateurs, les comptes sont prêts à être utilisés et les utilisateurs sont en mesure de se connecter à votre application à l’aide d’Azure AD B2C. Si vous n’avez pas accès au mot de passe des utilisateurs, passez à la section suivante.

## <a name="step-3-password-reset"></a>Étape 3 : Réinitialisation de mot de passe
Si vous migrez des utilisateurs avec un mot de passe aléatoire, les utilisateurs doivent réinitialiser leur mot de passe. Pour réinitialiser le mot de passe :
* Envoyez un e-mail de bienvenue avec un lien pour réinitialiser le mot de passe.
* [Facultatif] Modifiez votre stratégie pour gérer une situation dans laquelle l’utilisateur ne réinitialise pas le mot de passe et essaie de se connecter. Lors de la connexion, votre stratégie vérifie l’état de migration. Si l’utilisateur n’a pas changé le mot de passe, générez un message d’erreur convivial l’invitant à cliquer sur « Vous avez oublié votre mot de passe ? ».

    > [!NOTE]
    > Pour vérifier et modifier l’état de migration utilisateur, vous devez utiliser une stratégie personnalisée. Suivez les étapes décrites dans [Bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md).
    >

### <a name="step-31-send-a-welcome-email-with-link-to-reset-password"></a>Étape 3.1 Envoi d’un e-mail de bienvenue avec un lien pour réinitialiser le mot de passe
Pour obtenir le lien vers votre stratégie de réinitialisation de mot de passe :

1.  Ouvrez **Paramètres Azure AD B2C** et accédez aux propriétés de votre stratégie de **réinitialisation de mot de passe**.

2. Sélectionnez votre application
    >[!NOTE]
    >
    >**Exécuter maintenant** nécessite la préinscription d’au moins une application sur le locataire. Pour découvrir comment inscrire des applications, consultez les articles sur Azure AD B2C [Bien démarrer](active-directory-b2c-get-started.md) et [Inscription des applications](active-directory-b2c-app-registration.md).  

2.  Sélectionnez **Exécuter maintenant** et vérifiez la stratégie.
3.  **Copiez** l’URL et envoyez-la à vos utilisateurs.

    ![Configurer les journaux de diagnostics](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-user-migration-status"></a>Étape 4 : [facultative] Modification de votre stratégie pour vérifier et définir l’état de migration utilisateur

Lorsque les utilisateurs tentent de se connecter sans commencer par réinitialiser le mot de passe, votre stratégie doit renvoyer un message d’erreur convivial. Par exemple : Votre mot de passe a expiré. Pour le réinitialiser, cliquez sur le lien de réinitialisation de mot de passe.  Cette étape facultative requiert l’utilisation d’Azure AD B2C avec des stratégies personnalisées, comme décrit dans l’article [Azure Active Directory B2C : bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md).

Dans cette section, vous modifiez la stratégie pour vérifier l’état de migration à la connexion. Si l’utilisateur n’a pas changé le mot de passe, renvoyez un message d’erreur 409 HTTP invitant l’utilisateur à cliquer sur le lien « Vous avez oublié votre mot de passe ? » . Pour suivre la modification de mot de passe, vous utilisez Table Azure. L’exécution du processus de prémigration avec le paramètre de ligne de commande `2` crée une entité utilisateur dans Table Azure. Votre service :

* Lors de la connexion, la stratégie Azure AD B2C appelle votre service Restful de migration, en envoyant une adresse e-mail en tant que revendication d’entrée. Le service recherche l’adresse e-mail dans Table Azure. S’il existe, générez le message d’erreur « Vous devez modifier le mot de passe. ».

* Lors de la réinitialisation de mot de passe, une fois que l’utilisateur a correctement modifié le mot de passe, supprimez l’entité de Table Azure.


> [!NOTE]
>Nous utilisons Table Azure pour simplifier l’exemple. Vous pouvez stocker l’état de migration dans n’importe quelle base de données ou en tant que propriété personnalisée dans le compte Azure AD B2C.

### <a name="41-application-settings"></a>4.1 Paramètres de l’application
Pour tester la démonstration d’API Restful. Ouvrez la solution `AADB2C.UserMigration.sln` Visual Studio dans Visual Studio. Dans le projet `AADB2C.UserMigration.API`, ouvrez le fichier App.config. Remplacez les trois paramètres de l’application par vos propres valeurs :

```XML
<appSettings>
    <add key="BlobStorageConnectionString" value="{The Azure Blob storage connection string"} />
</appSettings>
```

### <a name="step-42-deploy-your-web-application-to-azure-app-services"></a>Étape 4.2 Déploiement de votre application web vers Azure App Services
Publiez votre service d’API dans Azure App Services. Pour plus d’informations, consultez : [Déploiement Git local vers Azure App Service](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-deploy).

### <a name="step-43-add-technical-profile-and-technical-profile-validation-to-your-policy"></a>Étape 4.3 Ajout d’un profil technique et d’une validation de profil technique à votre stratégie 
1.  Ouvrez le fichier de stratégie d’extension (TrustFrameworkExtensions.xml) à partir de votre répertoire de travail. 
2. Recherchez la section `<ClaimsProviders>`
3. Ajoutez l’extrait de code XML suivant sous l’élément `ClaimsProviders`.
4. Modifiez la valeur de `ServiceUrl` pour qu’elle pointe vers l’URL de votre point de terminaison. 

```XML
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>

    <TechnicalProfile Id="LocalAccountSignIn">
        <DisplayName>Local account just in time migration</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
        </InputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <TechnicalProfile Id="LocalAccountPasswordReset">
        <DisplayName>Local account just in time migration</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
        </InputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>

<ClaimsProvider>
    <DisplayName>Local Account</DisplayName>
    <TechnicalProfiles>

    <!-- This technical profile uses a validation technical profile to authenticate the user. -->
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="LocalAccountSignIn" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    <TechnicalProfile Id="LocalAccountWritePasswordUsingObjectId">
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="LocalAccountPasswordReset" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    </TechnicalProfiles>
</ClaimsProvider>
```

Le profil technique définit une revendication d’entrée : `signInName` (envoyée sous forme d’adresse e-mail). Lors de la connexion, la revendication est envoyée à votre point de terminaison Restful.

Une fois le profil technique défini pour votre API Restful, configurez votre stratégie Azure AD B2C pour qu’elle appelle ce profil technique. L’extrait de code XML remplace `SelfAsserted-LocalAccountSignin-Email`, qui est défini dans la stratégie de base. L’extrait de code XML ajoute également `ValidationTechnicalProfile` avec ReferenceId pointant vers votre profil technique `LocalAccountUserMigration`. 

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Étape 4.4 Chargement de la stratégie sur votre locataire
1.  Dans le [portail Azure](https://portal.azure.com), passez au [contexte de votre locataire Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) et cliquez sur **Azure AD B2C**.
2.  Sélectionnez **Infrastructure d’expérience d’identité**.
3.  Cliquez sur **Toutes les stratégies**.
4.  Sélectionnez **Charger la stratégie**
5.  Cochez la case **Remplacer la stratégie si elle existe**.
6.  **Téléchargez** TrustFrameworkExtensions.xml et vérifiez que sa validation n’échoue pas
7.  Répétez la dernière étape et chargez le fichier SignUpOrSignIn.xml.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Étape 4.5 Test de la stratégie personnalisée en utilisant Exécuter maintenant
1.  Ouvrez **Paramètres Azure AD B2C** et accédez à **Infrastructure d’expérience d’identité**.
2.  Ouvrez **B2C_1A_signup_signin**, la stratégie personnalisée de partie de confiance que vous avez chargée. Sélectionnez **Exécuter maintenant**.
3. Essayez de vous connecter avec l’un des utilisateurs migrés et cliquez sur **Connexion**. Vous devez voir le message d’erreur suivant provenant de votre API Rest.

    ![Configurer les journaux de diagnostics](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshooting-your-rest-api"></a>Étape 4.6 [facultative] Résolution des problèmes liés à votre API REST
Vous pouvez surveiller et consulter les informations de journalisation en temps quasi-réel.

1. À partir du menu des paramètres de l’application Restful, faites défiler jusqu’à la section **Analyse** et cliquez sur **Journaux de diagnostic**. 
2. Activer la journalisation des applications (Système de fichiers) 
3. Définissez le paramètre **Niveau** sur **Détaillé**.
4. Cliquez sur **Enregistrer**.

    ![Configurer les journaux de diagnostics](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

5. Dans le menu des paramètres, cliquez sur **Flux de journaux**.
6. Vérifiez la sortie de l’API Restful.

Pour plus d’informations, consultez : [Diffusion en continu des journaux](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-streaming-logs-and-console).

> [!IMPORTANT]
> Les __journaux de diagnostic__ doivent être utilisés uniquement pendant le développement et le test. La sortie de l’API RESTful peut contenir des informations confidentielles qui ne doivent pas être exposées en production.
>

## <a name="download-the-complete-policy-files"></a>Télécharger les fichiers de stratégie complets
Facultatif : nous vous recommandons de créer votre scénario avec vos propres fichiers de stratégie personnalisée après avoir suivi la procédure pas à pas Bien démarrer avec les stratégies personnalisées, au lieu d’utiliser ces exemples de fichiers.  [Exemples de fichiers de stratégie de référence](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration)