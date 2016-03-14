<properties
   pageTitle="Utilisation d’Azure Key Vault pour protéger la confidentialité des secrets d’application | Microsoft Azure"
   description="Comment utiliser le service Key Vault pour stocker des secrets d’application"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Utilisation d’Azure Key Vault pour protéger la confidentialité des secrets d’application

Cet article fait [partie d’une série] qui s’accompagne d’un [exemple d’application].

## Vue d'ensemble

Il est courant que les paramètres d’une application revêtent un caractère sensible et doivent être protégés, par exemple :

- Chaînes de connexion de base de données
- Mot de passe
- Clés de chiffrement

Par sécurité, il est conseillé de ne jamais stocker ces données secrètes dans le contrôle de code source. En effet, le risque de fuite est trop élevé, même si votre référentiel de code source est privé. De plus, il ne s’agit pas seulement de les préserver d’un accès par le grand public. Sur les projets volumineux, vous souhaiterez peut-être restreindre l’accès aux données de production secrètes à certains développeurs et opérateurs. (Les paramètres sont différents pour les environnements de test et de développement.)

Une option plus sécurisée consiste à stocker ces données secrètes dans [Azure Key Vault][KeyVault]. Ce service hébergé dans le cloud assure la gestion des clés de chiffrement et d’autres données secrètes. Cet article explique comment l’utiliser pour stocker les paramètres de configuration de votre application.

Dans l’application [Tailspin Surveys][Surveys], les paramètres suivants sont secrets :

- la chaîne de connexion de base de données ;
- la chaîne de connexion Redis ;
- la clé secrète client de l’application web.

Pour stocker les données de configuration secrètes dans le coffre de clés, l’application Surveys implémente un fournisseur de configuration personnalisé, qui se connecte au [système de configuration ][configuration] ASP.NET Core 1.0. Le fournisseur personnalisé lit les paramètres de configuration présents dans le coffre de clés au démarrage.

L’application Surveys charge les paramètres de configuration à partir des emplacements suivants :

- le fichier appsettings.json ;
- le [magasin de clés secrètes de l’utilisateur][user-secrets] (environnement de développement uniquement ; à des fins de test ) ;
- l’environnement d’hébergement (paramètres des applications web Azure) ;
- Key Vault

Chacun d’eux écrase le précédent. Par conséquent, les paramètres stockés dans le coffre de clés sont prioritaires.

> [AZURE.NOTE] Par défaut, le fournisseur de configuration du coffre de clés est désactivé. Il n’est pas nécessaire pour exécuter l’application localement. Vous devez l’activer dans un environnement de production.

> [AZURE.NOTE] Actuellement, le fournisseur du coffre de clés n’est pas pris en charge avec .NET Core, car il requiert le package [Microsoft.Azure.KeyVault][Microsoft.Azure.KeyVault].

Au démarrage, l’application lit les paramètres de chaque fournisseur de configuration enregistré et les utilise pour remplir un objet d’options fortement typé. (Pour plus d’informations, consultez [Using Options and configuration objects][options] (Utilisation des objets d’options et de configuration.)

## Implémentation

La classe [KeyVaultConfigurationProvider][KeyVaultConfigurationProvider] est un fournisseur de configuration qui se connecte au [système de configuration][configuration] ASP.NET Core 1.0.

Pour utiliser la classe `KeyVaultConfigurationProvider`, appelez la méthode d’extension `AddKeyVaultSecrets` dans la classe de démarrage :

```csharp
    var builder = new ConfigurationBuilder()
        .SetBasePath(appEnv.ApplicationBasePath)
        .AddJsonFile("appsettings.json");

    if (env.IsDevelopment())
    {
        builder.AddUserSecrets();
    }
    builder.AddEnvironmentVariables();
    var config = builder.Build();

    // Add key vault configuration:
    builder.AddKeyVaultSecrets(config["AzureAd:ClientId"],
        config["KeyVault:Name"],
        config["AzureAd:Asymmetric:CertificateThumbprint"],
        Convert.ToBoolean(config["AzureAd:Asymmetric:ValidationRequired"]),
        loggerFactory);
```

Notez que `KeyVaultConfigurationProvider` requiert des paramètres de configuration, qui doivent être stockés dans l’une des autres sources de configuration.

Lorsque l’application démarre, `KeyVaultConfigurationProvider` énumère toutes les données secrètes du coffre de clés. Pour chaque donnée secrète, elle recherche une balise nommée « ConfigKey ». La valeur de la balise est le nom du paramètre de configuration.

> [AZURE.NOTE] [Tags]Les [balises clés] sont des métadonnées facultatives stockées avec une clé. Les balises sont utilisées ici, car les noms de clé ne peuvent pas contenir de caractères deux-points (:).

```csharp
var kvClient = new KeyVaultClient(GetTokenAsync);
var secretsResponseList = await kvClient.GetSecretsAsync(_vault, MaxSecrets, token);
foreach (var secretItem in secretsResponseList.Value)
{
    //The actual config key is stored in a tag with the Key "ConfigKey"
    // because ':' is not supported in a shared secret name by Key Vault.
    if (secretItem.Tags != null && secretItem.Tags.ContainsKey(ConfigKey))
    {
        var secret = await kvClient.GetSecretAsync(secretItem.Id, token);
        Data.Add(secret.Tags[ConfigKey], secret.Value);
    }
}
```

> [AZURE.NOTE] Consultez la page [KeyVaultConfigurationProvider.cs].

## Configuration du coffre de clés dans l’application Surveys

Configuration requise :

- Installez les [applets de commande d’Azure Resource Manager][azure-rm-cmdlets].
- Configurez l’application Surveys comme décrit dans la page [Running the Surveys application][readme] (Exécution de l’application Surveys).

Procédure générale :

1. Configurez un utilisateur admin dans le client.
2. Configurez un certificat client.
3. Création d’un coffre de clés
4. Ajoutez des paramètres de configuration à votre coffre de clés.
5. Supprimez les marques de commentaire du code qui active le coffre de clés.
6. Mettez à jour les clés secrètes de l’utilisateur de l’application.

### Configuration d’un utilisateur admin

> [AZURE.NOTE] Pour créer un coffre de clés, vous devez utiliser un compte qui peut gérer votre abonnement Azure. En outre, toute application dont vous autorisez la lecture dans le coffre de clés doit être enregistrée dans le même client que ce compte.

Dans cette étape, vous allez vous assurer que vous pouvez créer un coffre de clés lorsque vous êtes connecté en tant qu’utilisateur du client où l’application Surveys est inscrite.

Commencez par modifier l’annuaire associé à votre abonnement Azure.

1. Connectez-vous au [portail de gestion Azure][azure-management-portal].

2. Cliquez sur **Paramètres**.

    ![Paramètres](media/guidance-multitenant-identity/settings.png)

3. Sélectionnez votre abonnement Azure.

4. Cliquez sur **Modifier l’annuaire** au bas du portail.

    ![Paramètres](media/guidance-multitenant-identity/edit-directory.png)

5. Sous « Modifier l’annuaire associé », sélectionnez le client Azure AD où l’application Surveys est inscrite,

    ![Paramètres](media/guidance-multitenant-identity/edit-directory2.png)

6. Cliquez sur le bouton fléché et renseignez la boîte de dialogue.

Créez un utilisateur admin au sein du client Azure AD où l’application Surveys est inscrite.

1. Connectez-vous au [portail de gestion Azure][azure-management-portal].

2. Sélectionnez le client Azure AD où votre application est inscrite.

3. Cliquez sur **Utilisateurs** > **Ajouter un utilisateur**.

4. Dans la boîte de dialogue **Ajouter un utilisateur**, affectez à l’utilisateur le rôle Administrateur général.

Ajoutez l’utilisateur admin en tant que coadministrateur pour votre abonnement Azure.

1. Connectez-vous au [portail de gestion Azure][azure-management-portal].

2. Cliquez sur **Paramètres** et sélectionnez votre abonnement Azure.

3. Cliquez sur **Administrateurs**.

4. Cliquez sur **Ajouter** en bas du portail.

5. Entrez l’adresse e-mail de l’utilisateur admin que vous avez créé précédemment.

6. Cochez la case correspondant à l’abonnement.

7. Cliquez sur la coche pour valider et quitter la boîte de dialogue.

![Ajout d’un coadministrateur](media/guidance-multitenant-identity/co-admin.png)


### Configuration d’un certificat client

1. Exécutez le script PowerShell [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault] comme suit :
    ```
    .\Setup-KeyVault.ps1 -Subject <<subject>>
    ```
    Pour le paramètre `Subject`, entrez un nom, comme « surveysapp ». Le script génère un certificat auto-signé et le stocke dans le magasin de certificats « Utilisateur actuel/Personnel ».

2. La sortie du script est un fragment JSON. Ajoutez-le au manifeste d’application de l’application web, en procédant comme suit :

    1. Connectez-vous au [portail de gestion Azure][azure-management-portal] et accédez à votre annuaire Azure AD.

    2. Cliquez sur **Applications**.

    3. Sélectionnez l’application Surveys.

    4.	Cliquez sur **Gérer le manifeste** et sélectionnez **Télécharger le manifeste**.

    5.	Ouvrez le fichier JSON du manifeste dans un éditeur de texte. Collez la sortie du script dans la propriété `keyCredentials`. Le résultat doit être semblable à ce qui suit :
    ```
            "keyCredentials": [
                {
                  "type": "AsymmetricX509Cert",
                  "usage": "Verify",
                  "keyId": "29d4f7db-0539-455e-b708-....",
                  "customKeyIdentifier": "ZEPpP/+KJe2fVDBNaPNOTDoJMac=",
                  "value": "MIIDAjCCAeqgAwIBAgIQFxeRiU59eL.....
                }
              ],
    ```          
    6.	Enregistrez les modifications apportées au fichier JSON.

    7.	Revenez au portail. Cliquez sur **Gérer le manifeste** > **Télécharger le manifeste sur le serveur**, puis téléchargez le fichier JSON.

3. Ajoutez le même fragment JSON au manifeste d’application de l’API web (Surveys.WebAPI).

4. Exécutez la commande suivante pour obtenir l’empreinte numérique du certificat.
    ```
    certutil -store -user my [subject]
    ```
    où `[subject]` est la valeur que vous avez spécifiée pour l’objet dans le script PowerShell. L’empreinte numérique est répertoriée sous « Cert Hash(sha1) ». Supprimez les espaces entre les nombres hexadécimaux.

Vous utiliserez l’empreinte numérique ultérieurement.

### Création d’un coffre de clés

1. Exécutez le script PowerShell [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault] comme suit :

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ResourceGroupName <<resource group name>> -Location <<location>>
    ```

    Lorsque vous êtes invité à entrer vos informations d’identification, connectez-vous avec les informations de l’utilisateur Azure AD que vous avez créé précédemment. Le script crée un groupe de ressources et un coffre de clés au sein de ce groupe de ressources.

    Remarque : pour le paramètre -Location, vous pouvez utiliser la commande PowerShell suivante afin d’obtenir la liste des régions valides :

    ```
    Get-AzureRmResourceProvider -ProviderNamespace "microsoft.keyvault" | Where-Object { $_.ResourceTypes.ResourceTypeName -eq "vaults" } | Select-Object -ExpandProperty Locations
    ```

2. Réexécutez SetupKeyVault.ps avec les paramètres suivants :

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ApplicationIds @("<<web app client ID>>", "<<web API client ID>>")
    ```

    où

    - key vault name = Le nom que vous avez affecté au coffre de clés à l’étape précédente.
    - web app client ID = L’ID client de l’application web Surveys.
    - web api client ID = L’ID client de l’application Surveys.WebAPI.

    Exemple :
    ```
    .\Setup-KeyVault.ps1 -KeyVaultName tailspinkv -ApplicationIds @("f84df9d1-91cc-4603-b662-302db51f1031", "8871a4c2-2a23-4650-8b46-0625ff3928a6")
    ```

    > [AZURE.NOTE] Vous pouvez obtenir les ID client via le [portail de gestion Azure][azure-management-portal]. Sélectionnez le client Azure AD, sélectionnez l’application, puis cliquez sur **Configurer**.

    Ce script autorise l’application web et l’API web à extraire les données secrètes de votre coffre de clés. Pour plus d’informations, consultez [Prise en main d’Azure Key Vault][authorize-app].

### Ajout de paramètres de configuration à votre coffre de clés

1. Exécutez SetupKeyVault.ps comme suit :

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName RedisCache -KeyValue "<<Redis DNS name>>.redis.cache.windows.net,password=<<Redis access key>>,ssl=true" -ConfigName "Redis:Configuration"
    ```
    où

    - key vault name = Le nom que vous avez affecté au coffre de clés à l’étape précédente.
    - Redis DNS name = Le nom DNS de votre instance de cache Redis.
    - Redis access key = la clé d’accès pour votre instance de cache Redis.

    Cette commande ajoute une clé secrète à votre coffre de clés. Elle se compose d’une paire nom/valeur et d’une balise :

    -	Le nom de la clé n’est pas utilisé par l’application, mais il doit être unique dans le coffre de clés.
    -	La valeur correspond à celle de l’option de configuration, dans ce cas, la chaîne de connexion Redis.
    -	La balise « ConfigKey » contient le nom de la clé de configuration.

2. À ce stade, il est judicieux de vérifier si vous avez stocké correctement les clés secrètes dans le coffre de clés. Exécutez la commande PowerShell suivante :

    ```
    Get-AzureKeyVaultSecret <<key vault name>> RedisCache | Select-Object *
    ```
    La sortie doit indiquer la valeur de clé secrète, ainsi que des métadonnées :

    ![Sortie PowerShell](media/guidance-multitenant-identity/get-secret.png)

3. Réexécutez SetupKeyVault.ps pour ajouter la chaîne de connexion de base de données :

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName ConnectionString -KeyValue <<DB connection string>> -ConfigName "Data:SurveysConnectionString"
    ```

    où `<<DB connection string>>` est la valeur de la chaîne de connexion de base de données.

    Pour effectuer un test avec la base de données locale, copiez la chaîne de connexion à partir du fichier Tailspin.Surveys.Web/appsettings.json. Si vous procédez ainsi, veillez à remplacer la double barre oblique inverse (« \\\ ») par une seule barre oblique inverse. La double barre oblique inverse est un caractère d’échappement dans le fichier JSON.

    Exemple :

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName mykeyvault -KeyName ConnectionString -KeyValue "Server=(localdb)\MSSQLLocalDB;Database=Tailspin.SurveysDB;Trusted_Connection=True;MultipleActiveResultSets=true" -ConfigName "Data:SurveysConnectionString"
    ```

### Suppression des marques de commentaire du code qui active le coffre de clés

1. Ouvrez la solution Tailspin.Surveys.

2. Dans [Tailspin.Surveys.Web/Startup.cs][web-startup], localisez le bloc de code suivant et supprimez les marques de commentaire.

    ```csharp
    //#if DNX451
    //            _configuration = builder.Build();
    //            builder.AddKeyVaultSecrets(_configuration["AzureAd:ClientId"],
    //                _configuration["KeyVault:Name"],
    //                _configuration["AzureAd:Asymmetric:CertificateThumbprint"],
    //                Convert.ToBoolean(_configuration["AzureAd:Asymmetric:ValidationRequired"]),
    //                loggerFactory);
    //#endif
    ```

3. Dans [Tailspin.Surveys.WebAPI/Startup.cs][web-api-startup], localisez le bloc de code suivant et supprimez les marques de commentaire.

    ```csharp
    //#if DNX451
    //            var config = builder.Build();
    //            builder.AddKeyVaultSecrets(config["AzureAd:ClientId"],
    //                config["KeyVault:Name"],
    //                config["AzureAd:Asymmetric:CertificateThumbprint"],
    //                Convert.ToBoolean(config["AzureAd:Asymmetric:ValidationRequired"]),
    //                loggerFactory);
    //#endif
    ```

4. Dans [Tailspin.Surveys.Web/Startup.cs][web-startup], recherchez le code qui inscrit le `ICredentialService`. Supprimez les marques de commentaire de la ligne qui utilise `CertificateCredentialService`, puis commentez la ligne qui utilise `ClientCredentialService` :

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

    Cette modification permet à l’application web d’utiliser l’[assertion du client][client-assertion] pour obtenir des jetons d’accès OAuth. Avec l’assertion du client, vous n’avez pas besoin de clé secrète client OAuth. Vous pouvez également stocker la clé secrète client dans le coffre de clés. Toutefois, le coffre de clés et l’assertion du client utilisent tous deux un certificat client. Par conséquent, si vous activez le coffre de clés, il est recommandé d’activer également l’assertion du client.

### Mise à jour des données secrètes de l’utilisateur

Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet Tailspin.Surveys.Web, puis sélectionnez **Gérer les données secrètes de l’utilisateur**. Dans le fichier secrets.json, supprimez le script JSON existant et collez les éléments suivants :

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys web app client ID]",
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Surveys.WebAPI application]",
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint. Example: 105b2ff3bc842c53582661716db1b7cdc6b43ec9]",
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "KeyVault": {
        "Name": "[key vault name]"
      }
    }
    ```

Remplacez les entrées entre [crochets] par les valeurs correctes.

- `AzureAd:ClientId` : L’ID client de l’application Surveys.
- `AzureAd:WebApiResourceId` : L’URI ID d’application que vous avez spécifié lorsque vous avez créé l’application Surveys.WebAPI dans Azure AD.
- `Asymmetric:CertificateThumbprint` : L’empreinte numérique de certificat que vous avez obtenue précédemment, lorsque vous avez créé le certificat client.
- `KeyVault:Name` : Le nom de votre coffre de clés.

> [AZURE.NOTE] `Asymmetric:ValidationRequired` a la valeur false, car le certificat que vous avez créé précédemment n’est pas signé par une autorité de certification racine. En production, utilisez un certificat signé par une autorité de certification, puis affectez à `ValidationRequired` la valeur true.

Enregistrez le fichier secrets.json mis à jour.

Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet Tailspin.Surveys.WebApi, puis sélectionnez **Gérer les données secrètes de l’utilisateur**. Supprimez le script JSON existant et collez les éléments suivants :

```
{
  "AzureAd": {
    "ClientId": "[Surveys.WebAPI client ID]",
    "WebApiResourceId": "https://tailspin5.onmicrosoft.com/surveys.webapi",
    "Asymmetric": {
      "CertificateThumbprint": "[certificate thumbprint]",
      "StoreName": "My",
      "StoreLocation": "CurrentUser",
      "ValidationRequired": "false"
    }
  },
  "KeyVault": {
    "Name": "[key vault name]"
  }
}
```

Remplacez les entrées entre [crochets] et enregistrez le fichier secrets.json.

> [AZURE.NOTE] Pour l’API web, veillez à utiliser l’ID client de l’application Surveys.WebAPI et non de l’application Surveys.


<!-- Links -->
[authorize-app]: ../key-vault/key-vault-get-started.md/#authorize
[azure-management-portal]: https://manage.windowsazure.com/
[azure-rm-cmdlets]: https://msdn.microsoft.com/library/mt125356.aspx
[client-assertion]: guidance-multitenant-identity-client-assertion.md
[configuration]: https://docs.asp.net/en/latest/fundamentals/configuration.html
[KeyVault]: https://azure.microsoft.com/services/key-vault/
[KeyVaultConfigurationProvider]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Configuration.KeyVault/KeyVaultConfigurationProvider.cs
[balises clés]: https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_Keytags
[Microsoft.Azure.KeyVault]: https://www.nuget.org/packages/Microsoft.Azure.KeyVault/
[options]: https://docs.asp.net/en/latest/fundamentals/configuration.html#using-options-and-configuration-objects
[readme]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md
[Setup-KeyVault]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: guidance-multitenant-identity-tailspin.md
[user-secrets]: http://go.microsoft.com/fwlink/?LinkID=532709
[web-startup]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[web-api-startup]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/Startup.cs
[partie d’une série]: guidance-multitenant-identity.md
[KeyVaultConfigurationProvider.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Configuration.KeyVault/KeyVaultConfigurationProvider.cs
[exemple d’application]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->