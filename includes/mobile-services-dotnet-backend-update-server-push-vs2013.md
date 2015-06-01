Cette procédure permet de créer un [ApiController][ApiController] personnalisé qui envoie des notifications Push à l'application. Vous pouvez implémenter ce même code dans un [TableController][TableController] ou n'importe où ailleurs dans vos services principaux.

1.  Dans l'Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le dossier Controllers du projet de service mobile, développez **Add**, puis cliquez sur **New Scaffolded Item**.

    La boîte de dialogue Add Scaffold s'affiche.

2.  Développez **Azure Mobile Services**, cliquez sur **Azure Mobile Services Custom Controller**, cliquez sur **Add**, indiquez un **nom de contrôleur** pour `NotifyAllUsersController`, puis cliquez à nouveau sur **Add**.

    ![boîte de dialogue Add Scaffold de l'API Web][boîte de dialogue Add Scaffold de l'API Web]

    Une nouvelle classe de contrôleur vide appelée **NotifyAllUsersController** est créée.

3.  Dans le nouveau fichier de projet NotifyAllUsersController.cs, ajoutez les instructions **using** suivantes :

        using Newtonsoft.Json.Linq;
        using System.Threading.Tasks;

4.  Ajoutez le code suivant qui implémente la méthode POST sur le contrôleur :

        public async Task<bool> Post(JObject data)
        {
            try
            {
                // Define the XML paylod for a WNS native toast notification 
                // that contains the value supplied in the POST request.
                string wnsToast = 
                    string.Format("<?xml version="1.0" encoding="utf-8"?>" +
                    "<toast><visual><binding template="ToastText01">" + 
                    "<text id="1">{0}</text></binding></visual></toast>", 
                    data.GetValue("toast").Value<string>());

                // Define the WNS native toast with the payload string.
                WindowsPushMessage message = new WindowsPushMessage();
                message.XmlPayload = wnsToast;

                // Send the toast notification.
                await Services.Push.SendAsync(message);
                return true;
            }
            catch (Exception e)
            {
                Services.Log.Error(e.ToString());
            }
            return false;
        }

    > [WACOM.NOTE]This POST method can be called by any client that has the application key, which is not secure. Pour sécuriser le point de terminaison, appliquez l'attribut `[AuthorizeLevel(AuthorizationLevel.User)]` à la méthode ou à la classe afin d'exiger une authentification.

  [ApiController]: http://go.microsoft.com/fwlink/p/?LinkId=512673
  [TableController]: http://msdn.microsoft.com/library/azure/dn643359.aspx
  [boîte de dialogue Add Scaffold de l'API Web]: ./media/mobile-services-dotnet-backend-update-server-push-vs2013/add-custom-api-controller.png
