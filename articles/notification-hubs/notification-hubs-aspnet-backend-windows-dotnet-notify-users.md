<properties 
	pageTitle="Notification des utilisateurs via Azure Notification Hubs" 
	description="Découvrez comment envoyer des notifications Push sécurisées dans Azure. Exemples de code écrits en C# à l'aide de l'API .NET." 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	services="notification-hubs" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="wesmc"/>

#Notification des utilisateurs via Azure Notification Hubs

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal" class="current">Windows Universel</a><a href="/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/" title="iOS">iOS</a>
		<a href="/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

##Vue d'ensemble

La prise en charge des notifications Push dans Azure vous permet d'accéder à une infrastructure Push conviviale, multi-plateforme et avec montée en charge qui simplifie fortement l'implémentation des notifications Push pour les applications consommateur et entreprise pour les plateformes mobiles. Ce didacticiel explique comment utiliser Azure Notification Hubs pour envoyer des notifications Push à un utilisateur particulier d'une application sur un appareil spécifique. Un code WebAPI principal ASP.NET est utilisé pour authentifier les clients et pour générer les notifications, comme présenté dans la rubrique de conseils [Inscription auprès du serveur principal de votre application](http://msdn.microsoft.com/library/dn743807.aspx). Ce didacticiel fait intervenir le concentrateur de notification que vous avez créé dans le didacticiel intitulé [Prise en main de Notification Hubs].

Ce didacticiel constitue également une condition préalable au suivi du didacticiel [Notifications Push sécurisées]. Une fois que vous avez suivi la procédure de ce didacticiel, vous pouvez passer au didacticiel [Notifications Push sécurisées] qui explique comment modifier le code de ce didacticiel pour envoyer une notification Push en toute sécurité. 


##Conditions préalables 

Avant de commencer ce didacticiel, vous devez suivre les didacticiels Mobile Services suivants :

+ [Prise en main de Notification Hubs]<br/>Ce didacticiel vous permet de créer votre concentrateur de notification, de réserver le nom de l'application et de vous inscrire pour recevoir des notifications.




> [AZURE.NOTE] Si vous utilisez Mobile Services comme service principal, consultez la [version Mobile Services](../mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md) de ce didacticiel.


## Création et configuration du concentrateur de notification

Avant de commencer ce didacticiel, vous devez réserver un nom d'application, puis créer un concentrateur de notification Azure et le connecter à cette application. Suivez la procédure décrite dans [Prise en main de Notification Hubs (Windows Store)](notification-hubs-windows-store-dotnet-get-started.md), et plus précisément dans les sections [Inscription de votre application pour le Windows Store](notification-hubs-windows-store-dotnet-get-started.md#register) et [Configuration de votre concentrateur de notification](notification-hubs-windows-store-dotnet-get-started.md#configure-hub). En particulier, assurez-vous que vous avez bien entré les valeurs de **SID du package** et **Clé secrète client** dans le portail, dans l'onglet **Configurer** de votre concentrateur de notification. Cette procédure de configuration est décrite à la section [Configuration de votre concentrateur de notification](notification-hubs-windows-store-dotnet-get-started.md#configure-hub). Il s'agit d'une étape importante : si les informations d'identification sur le portail ne correspondent pas à celles spécifiées pour le nom d'application que vous avez choisi, la notification Push ne fonctionnera pas.

[AZURE.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Création du projet Windows Phone

L'étape suivante consiste à créer l'application Windows Phone. Pour ajouter ce projet à la solution actuelle, procédez comme suit :

1. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le nœud de niveau supérieur de la solution (**Solution NotifyUsers** dans ce cas), puis cliquez sur **Ajouter** et sur **Nouveau projet**.

2. Développez **Applications du Windows Store**, puis cliquez sur **Applications Windows Phone** et sur **Application vide (Windows Phone)**.

	![][9]

3. Dans la zone **Nom**, tapez **NotifyUserWindowsPhone**, puis cliquez sur **OK** pour générer le projet.

 
4. Associez cette application au Windows Phone Store : dans l'Explorateur de solutions, cliquez avec le bouton droit sur **NotifyUserWindowsPhone (Windows Phone 8.1)**, cliquez sur **Store**, puis cliquez sur **Associer l'application au Windows Store...**.

	![][10]
 
5. Suivez les étapes indiquées par l'Assistant pour vous connecter et associer l'application avec le Store.

	![][11]
	
	> [AZURE.NOTE] Notez bien le nom que vous avez choisi pour l'application au cours de cette procédure. Vous devez configurer le concentrateur de notification sur le portail à l'aide des informations d'identification que vous obtenez auprès du [Centre de développement Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409) pour ce nom d'application réservé spécifique. Cette procédure de configuration est décrite à la section [Configuration de votre concentrateur de notification](notification-hubs-windows-store-dotnet-get-started.md#configure-hub). Il s'agit d'une étape importante : si les informations d'identification sur le portail ne correspondent pas à celles spécifiées pour le nom d'application que vous avez choisi, la notification Push ne fonctionnera pas.

6. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet **NotifyUserWindowsPhone (Windows Phone 8.1)**, puis cliquez sur **Gérer les packages NuGet**.

7. Dans la partie gauche, cliquez sur **En ligne**.

8. Dans la zone **Rechercher**, entrez **Client Http**.

9. Dans la liste de résultats, cliquez sur **Bibliothèques clientes HTTP Microsoft**, puis cliquez sur **Installer**. Terminez l'installation.

10. Dans la zone **Rechercher** de NuGet, tapez **Json.net**. Installez le package **Json.NET**, puis fermez la fenêtre du Gestionnaire de package NuGet.

11. Dans l'Explorateur de solutions, dans le projet **NotifyUserWindowsPhone (Windows Phone 8.1)**, double-cliquez sur **MainPage.xaml** pour l'ouvrir dans l'éditeur de Visual Studio.

12. Dans le code XML de **MainPage.xaml**, remplacez la section `<Grid>` par le code suivant :

		<Grid>
	        <Grid.RowDefinitions>
	            <RowDefinition Height="Auto"/>
	            <RowDefinition Height="*"/>
	        </Grid.RowDefinitions>

	        <TextBlock Grid.Row="0" Text="Secure Push" HorizontalAlignment="Center" FontSize="48"/>

        	<StackPanel Grid.Row="1" VerticalAlignment="Center">
        	    <Grid>
        	        <Grid.RowDefinitions>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="*"/>
        	        </Grid.RowDefinitions>
            	    <TextBlock Grid.Row="0" Text="Username" FontSize="24" Margin="20,0,20,0"/>
            	    <TextBox Name="UsernameTextBox" Grid.Row="1" Margin="20,0,20,0"/>
            	    <TextBlock Grid.Row="2" Text="Password" FontSize="24" Margin="20,0,20,0" />
            	    <PasswordBox Name="PasswordTextBox" Grid.Row="3" Margin="20,0,20,0"/>
	
            	    <Button Grid.Row="4" HorizontalAlignment="Center" VerticalAlignment="Center" Content="1. Login and register" Click="LoginAndRegisterClick" />

            	    <Button Grid.Row="5" HorizontalAlignment="Center" VerticalAlignment="Center" Content="2. Send push" Click="PushClick" />
            	</Grid>
        	</StackPanel>
    	</Grid>


13. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet **NotifyUserWindowsPhone (Windows Phone 8.1)**, cliquez sur **Ajouter**, puis cliquez sur **Classe**. Nommez la classe **RegisterClient.cs**, puis cliquez sur **OK** pour générer la classe. Ce composant met en œuvre les appels REST nécessaires pour contacter le serveur principal de l'application, afin de l'inscrire aux notifications Push. Il enregistre également les valeurs *registrationIds* créées par le concentrateur de notification, comme expliqué en détail dans [Inscription auprès du serveur principal de votre application](http://msdn.microsoft.com/library/dn743807.aspx). Notez qu'il utilise un jeton d'autorisation qui se trouve dans le stockage local lorsque vous cliquez sur le bouton **Se connecter et s'inscrire**.

14. Ajoutez le code ci-après dans la définition de la classe `RegisterClient`. Remplacez bien `{backend endpoint}` par le point de terminaison de votre serveur principal, obtenu dans la section précédente :

		private string POST_URL = "{backend endpoint}/api/register";

        private class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }

        public async Task RegisterAsync(string handle, IEnumerable<string> tags)
        {
            var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();

            var deviceRegistration = new DeviceRegistration
            {
                Platform = "wns",
                Handle = handle,
                Tags = tags.ToArray<string>()
            };

            var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);

            if (statusCode == HttpStatusCode.Gone)
            {
                // regId is expired, deleting from local storage & recreating
                var settings = ApplicationData.Current.LocalSettings.Values;
                settings.Remove("__NHRegistrationId");
                regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
                statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
            }

            if (statusCode != HttpStatusCode.Accepted)
            {
                // log or throw
            }
        }

        private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
        {
            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

                var putUri = POST_URL + "/" + regId;

                string json = JsonConvert.SerializeObject(deviceRegistration);
                                var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
                return response.StatusCode;
            }
        }

        private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            if (!settings.ContainsKey("__NHRegistrationId"))
            {
                using (var httpClient = new HttpClient())
                {
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                    var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                    if (response.IsSuccessStatusCode)
                    {
                        string regId = await response.Content.ReadAsStringAsync();
                        regId = regId.Substring(1, regId.Length - 2);
                        settings.Add("__NHRegistrationId", regId);
                    }
                    else
                    {
                        throw new Exception();
                    }
                }
            }
            return (string)settings["__NHRegistrationId"];

        }


15. Ajoutez les instructions `using` ci-après au début du fichier RegisterClient.cs :

		using Windows.Storage;
		using System.Net;
		using System.Net.Http;
		using System.Net.Http.Headers;
		using Newtonsoft.Json;
		
16. Ajoutez du code pour les boutons dans MainPage.xaml.cs. Le rappel de la fonction **Se connecter et s'inscrire** stocke le jeton d'authentification de base dans le stockage local (notez que cela représente n'importe quel jeton utilisé par votre système d'authentification), puis utilise `RegisterClient` pour appeler le serveur principal. Le rappel de **AppBackend** appelle le serveur principal pour déclencher une notification sécurisée vers tous les appareils de cet utilisateur. 

	Ajoutez le code ci-après au fichier MainPage.xaml.cs après la méthode `OnNavigatedTo()`. Remplacez bien `{backend endpoint}` par le point de terminaison du serveur principal obtenu dans la section précédente :

		private async void PushClick(object sender, RoutedEventArgs e)
        {
            var POST_URL = "{backend endpoint}/api/notifications";

            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

                await httpClient.PostAsync(POST_URL, new StringContent(""));
            }
        }

        private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
        {
            SetAuthenticationTokenInLocalStorage();
            
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            await new RegisterClient().RegisterAsync(channel.Uri, new string[] { "myTag" });

            var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

        private void SetAuthenticationTokenInLocalStorage()
        {
            string username = UsernameTextBox.Text;
            string password = PasswordTextBox.Password;
            
            var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
            ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
        }


17. Ajoutez les instructions `using` suivantes au début du fichier MainPage.xaml.cs :

		using System.Net.Http;
		using Windows.Storage;
		using System.Net.Http.Headers;
		using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;

## Exécution de l'application

Pour exécuter l'application, procédez comme suit :

1. Dans Visual Studio, exécutez l'application Windows Phone **NotifyUserWindowsPhone (Windows Phone 8.1)**. L'émulateur Windows Phone s'exécute et charge l'application automatiquement.

2. Dans l'interface utilisateur de l'application **NotifyUserWindowsPhone**, entrez un nom d'utilisateur et un mot de passe. La valeur peut être une chaîne quelconque, mais elle doit être identique pour les deux.

3. Dans l'interface utilisateur de l'application **NotifyUserWindowsPhone**, cliquez sur **Se connecter et s'inscrire**. Cliquez ensuite sur **Send push** (Envoyer une notification Push).


[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push13.png



<!-- URLs. -->
[Prise en main de Notification Hubs]: notification-hubs-windows-store-dotnet-get-started.md
[Notifications Push sécurisées]: notification-hubs-aspnet-backend-windows-dotnet-secure-push.md

<!--HONumber=49--> 