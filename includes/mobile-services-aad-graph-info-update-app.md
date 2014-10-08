1.  Dans Visual Studio, ouvrez le fichier MainPage.xaml.cs et ajoutez l'instruction `using` suivante au début de celui-ci.

        using System.Net.Http;

2.  Dans le fichier MainPage.xaml.cs, ajoutez la définition de classe suivante à l'espace de noms pour sérialiser les informations utilisateur.

        public class UserInfo
        {
            public String displayName { get; set; }
            public String streetAddress { get; set; }
            public String city { get; set; }
            public String state { get; set; }
            public String postalCode { get; set; }
        }

3.  Dans le fichier MainPage.xaml.cs, mettez à jour la méthode `AuthenticateAsync` pour indiquer à l'API personnalisée de renvoyer des informations utilisateur supplémentaires depuis AAD.

        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory);
                    UserInfo userInfo = await App.MobileService.InvokeApiAsync<UserInfo>("getuserinfo", 
                        HttpMethod.Get, null);
                    message = string.Format("{0}, you are now logged in.\n\nYour address is...\n\n{1}\n{2}, {3} {4}", 
                        userInfo.displayName, userInfo.streetAddress, userInfo.city, userInfo.state, 
                        userInfo.postalCode);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }

                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

4.  Enregistrez vos modifications, puis configurez le service de manière à vérifier l'absence d'erreurs de syntaxe.


