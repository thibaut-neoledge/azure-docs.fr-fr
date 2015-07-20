
L'exemple précédent montrait une connexion standard, qui nécessite que le client contacte le fournisseur d'identité et le service mobile à chaque démarrage de l'application. Cette méthode est non seulement inefficace, mais vous pouvez rencontrer des problèmes d'utilisation si de nombreux clients tentent de lancer votre application en même temps. Une meilleure approche consiste à mettre en cache le jeton d'autorisation renvoyé par Mobile Services et à l'utiliser en premier avant de faire appel à la connexion via un fournisseur.

>[AZURE.NOTE]Vous pouvez mettre en cache le jeton émis par Mobile Services, que vous utilisiez l'authentification gérée par un client ou gérée par un service. Ce didacticiel utilise cette dernière.

1. Ouvrez le fichier de projet MainPage.xaml.cs et ajoutez les instructions **using** suivantes :

		using System.IO.IsolatedStorage;
		using System.Security.Cryptography;		

2. Remplacez la méthode **AuthenticateAsync** par le code suivant :

        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            string message;
            // This sample uses the Facebook provider.
            var provider = "Facebook";

            // Provide some additional app-specific security for the encryption.
            byte [] entropy = { 1, 8, 3, 6, 5 };

            // Authorization credential.
            MobileServiceUser user = null;

            // Isolated storage for the app.
            IsolatedStorageSettings settings =
                IsolatedStorageSettings.ApplicationSettings;

            while (user == null)
            {
                // Try to get an existing encrypted credential from isolated storage.                    
                if (settings.Contains(provider))
                {
                    // Get the encrypted byte array, decrypt and deserialize the user.
                    var encryptedUser = settings[provider] as byte[];
                    var userBytes = ProtectedData.Unprotect(encryptedUser, entropy);
                    user = JsonConvert.DeserializeObject<MobileServiceUser>(
                        System.Text.Encoding.Unicode.GetString(userBytes, 0, userBytes.Length));
                }
                if (user != null)
                {
                    // Set the user from the stored credentials.
                    App.MobileService.CurrentUser = user;

                    try
                    {
                        // Try to return an item now to determine if the cached credential has expired.
                        await App.MobileService.GetTable<TodoItem>().Take(1).ToListAsync();
                    }
                    catch (MobileServiceInvalidOperationException ex)
                    {
                        if (ex.Response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
                        {
                            // Remove the credential with the expired token.
                            settings.Remove(provider);
                            user = null;
                            continue;
                        }
                    }
                }
                else
                {
                    try
                    {
                        // Login with the identity provider.
                        user = await App.MobileService
                            .LoginAsync(provider);

                        // Serialize the user into an array of bytes and encrypt with DPAPI.
                        var userBytes = System.Text.Encoding.Unicode
                            .GetBytes(JsonConvert.SerializeObject(user));
                        byte[] encryptedUser = ProtectedData.Protect(userBytes, entropy);

                        // Store the encrypted user credentials in local settings.
                        settings.Add(provider, encryptedUser);
                        settings.Save();
                    }
                    catch (MobileServiceInvalidOperationException ex)
                    {
                        message = "You must log in. Login Required";
                    }
                }
                message = string.Format("You are now logged in - {0}", user.UserId);
                MessageBox.Show(message);
            }
        }

	Dans cette version de **AuthenticateAsync**, l'application essaye d'utiliser des informations d'identification chiffrées dans le stockage local pour accéder au service mobile. Une requête simple est envoyée pour vérifier que le jeton stocké n'est pas expiré. Quand une erreur 401 est retournée, une connexion basée sur un fournisseur habituel est tentée. Une connexion normale est également effectuée quand il n'y a pas d'informations d'identification stockées.

	>[AZURE.NOTE]Cette application teste s’il y a des jetons expirés pendant la connexion, mais l’expiration des jetons peut également survenir après l’authentification, alors que l’application est en cours d’utilisation. Pour une solution permettant de gérer les erreurs d'autorisation liées à des jetons expirés, consultez le post [Mise en cache et gestion des jetons expirés dans le Kit de développement logiciel (SDK) managé d'Azure Mobile Services](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx).
	
3. Redémarrez l'application deux fois.

	Notez que lors du premier démarrage, la connexion avec le fournisseur est à nouveau requise. Cependant, lors du second redémarrage, les informations d'identification mises en cache sont utilisées et l'étape de connexion est ignorée.

<!---HONumber=July15_HO2-->