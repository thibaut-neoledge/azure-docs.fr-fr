> [!NOTE]
> Nous prévoyons d’améliorer cette expérience et de déconseiller les étapes suivantes.

### <a name="create-an-administrator-credential-in-the-azure-ad-b2c-tenant"></a>Créer des informations d’identification d’administrateur dans le locataire Azure AD B2C

Pour la section suivante, vous devrez utiliser des informations d’identification liées au domaine de votre locataire Azure AD B2C. Pour ce faire, vous devez créer un compte Administrateur avec ces informations d’identification. Pour ce faire :

1. Dans le [portail Azure](https://portal.azure.com), basculez vers le contexte de votre locataire Azure AD B2C et ouvrez le panneau Azure AD B2C. [Montrez-moi comment.](..\articles\active-directory-b2c\active-directory-b2c-navigate-to-b2c-context.md)
1. Sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Tous les utilisateurs**.
1. Cliquez sur **+ Nouvel utilisateur**.
    * Définissez le **nom** = `Admin`.
    * Définissez le **nom d’utilisateur** = `admin@{tenantName}.onmicrosoft.com`, où `{tenantName}` représente le nom de votre locataire Azure AD B2C.
1. Sous **Rôle de répertoire**, sélectionnez **Administrateur général**, puis cliquez sur **OK**.
1. Cliquez sur **Créer** pour créer l’utilisateur administrateur.
1. Cochez la case **Afficher le mot de passe** et copiez le mot de passe.

### <a name="set-up-the-key-container"></a>Configurer le conteneur de clé

Le conteneur de clé permet de stocker les clés. Pour configurer un conteneur de clé :

1. Ouvrez une nouvelle invite de commandes PowerShell.  Pour ce faire, appuyez sur la combinaison de touches **Windows + R**, tapez `powershell`, puis appuyez sur Entrée.
1. Téléchargez cette reproduction pour obtenir l’outil ExploreAdmin de PowerShell.

    ```powershell
    git clone https://github.com/Azure-Samples/active-directory-b2c-advanced-policies
    ```

1. Basculez vers le dossier comportant l’outil ExploreAdmin.

    ```powershell
    cd active-directory-b2c-advanced-policies\ExploreAdmin
    ```

1. Importez l’outil ExploreAdmin dans PowerShell.

    ```powershell
    Import-Module .\ExploreAdmin.dll
    ```

1. Vérifiez que `b2c_1a_TokenSigningKeyContainer` n’existe pas encore.  Remplacez `{tenantName}` par le nom de votre locataire.

    ```powershell
    Get-CpimKeyContainer -TenantId {tenantName}.onmicrosoft.com -StorageReferenceId b2c_1a_TokenSigningKeyContainer -ForceAuthenticationPrompt
    ```

    a. Lorsque l’invite de connexion s’affiche, utilisez le compte Administrateur que vous avez créé dans la section précédente.

    b. Lorsque vous y êtes invité, entrez votre numéro de téléphone pour configurer l’authentification multifacteur.

    c. Modifiez votre mot de passe lorsque vous y êtes invité.

    d. **Une erreur est attendue !**  Cette erreur doit indiquer que `b2c_1a_TokenSigningKeyContainer` est introuvable.  Si aucune erreur n’est signalée parce que vous déjà effectué ces étapes, ignorez le reste de cette section.

1. Créez `b2c_1a_TokenSigningKeyContainer`.  Remplacez `{tenantName}` par le nom de votre locataire.

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com  b2c_1a_TokenSigningKeyContainer  b2c_1a_TokenSigningKeyContainer rsa 2048 0 0
    ```

1. Créez `b2c_1a_TokenEncryptionKeyContainer`.  Remplacez `{tenantName}` par le nom de votre locataire.

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com b2c_1a_TokenEncryptionKeyContainer b2c_1a_TokenEncryptionKeyContainer rsa 2048 0 0
    ```

1. Créez `b2c_1a_FacebookSecret`.  Remplacez `{tenantName}` par le nom de votre locataire.

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com  b2c_1a_FacebookSecret  b2c_1a_FacebookSecret rsa 2048 0 0
    ```
