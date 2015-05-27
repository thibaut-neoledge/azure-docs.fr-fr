

Les instructions ci-dessous s’appliquent à la mise à jour d’une application cliente Windows Store, mais vous pouvez les tester sur n’importe quelle autre plateforme prise en charge par Azure Mobile Services.


1. Dans Visual Studio, ouvrez le fichier MainPage.xaml.cs et ajoutez l'instruction `using` en haut de celui-ci.
 
        using System.Net.Http;

2. Dans le fichier MainPage.xaml.cs, ajoutez la définition de classe suivante à l'espace de noms pour sérialiser les informations utilisateur.

	    public class UserInfo
	    {
	        public String displayName { get; set; }
	        public String streetAddress { get; set; }
	        public String city { get; set; }
	        public String state { get; set; }
	        public String postalCode { get; set; }
	        public String mail { get; set; }
	        public String[] otherMails { get; set; }
            
	        public override string ToString()
	        {
	            return "displayName : " + displayName + "\n" +
	                   "streetAddress : " + streetAddress + "\n" +
	                   "city : " + city + "\n" +
	                   "state : " + state + "\n" +
	                   "postalCode : " + postalCode + "\n" +
	                   "mail : " + mail + "\n" +
	                   "otherMails : " + string.Join(", ", otherMails);
	        }
	    }


3. Dans le fichier MainPage.xaml.cs, mettez à jour la méthode `AuthenticateAsync` pour indiquer à l'API personnalisée de renvoyer des informations utilisateur supplémentaires depuis AAD.

        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
                    // Change 'MobileService' to the name of your MobileServiceClient instance.
                    // Sign-in using Facebook authentication.
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory);

                    UserInfo userInfo = await App.MobileService
						.InvokeApiAsync<UserInfo>("getuserinfo", HttpMethod.Get, null);

                    message = string.Format("User info for the logged in user...\n\n{0}",userInfo.ToString());
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


4. Enregistrez vos modifications, puis configurez le service de manière à vérifier l'absence d'erreurs de syntaxe.

<!--HONumber=54-->