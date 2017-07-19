## <a name="obtain-an-azure-resource-manager-token"></a>Obtenir un jeton Azure Resource Manager
Azure Active Directory doit authentifier toutes les tâches que vous effectuez sur des ressources à l’aide d’Azure Resource Manager. L’exemple présenté ici utilise une authentification par mot de passe. Pour d’autres approches, consultez [Demandes d’authentification Azure Resource Manager][lnk-authenticate-arm].

1. Ajoutez le code suivant à la méthode **Main** dans le fichier Program.cs pour récupérer un jeton d’Azure AD à l’aide de l’ID d’application et d’un mot de passe.
   
    ```
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.microsoftonline.com/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
   
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```
2. Créez un objet **ResourceManagementClient** qui utilise le jeton en ajoutant le code suivant à la fin de la méthode **Main** :
   
    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. Créez ou obtenez une référence au groupe de ressources que vous utilisez :
   
    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx