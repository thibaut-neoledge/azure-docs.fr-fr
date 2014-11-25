<properties title="Azure Notification Hubs Notify Users" pageTitle="Azure Notification Hubs Notify Users" metaKeywords="Azure push notifications, Azure notification hubs" description="Learn how to send secure push notifications in Azure. Code samples written in C# using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sethm" />

# Notification des utilisateurs via Azure Notification Hubs

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/fr-fr/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal" class="current">Windows Universal</a><a href="/fr-fr/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/" title="iOS">iOS</a>
        <a href="/fr-fr/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

La prise en charge des notifications Push dans Azure vous permet d'accéder à une infrastructure Push conviviale, multi-plateforme et avec montée en charge qui simplifie fortement l'implémentation des notifications Push pour les applications consommateur et entreprise pour les plateformes mobiles. Ce didacticiel explique comment utiliser Azure Notification Hubs pour envoyer des notifications Push à un utilisateur particulier d'une application sur un appareil spécifique. Un code WebAPI principal ASP.NET est utilisé pour authentifier les clients et pour générer les notifications, comme présenté dans la rubrique de conseils [Inscription auprès du serveur principal de votre application][Inscription auprès du serveur principal de votre application]. Ce didacticiel fait intervenir le concentrateur de notification que vous avez créé dans le didacticiel intitulé **Prise en main de Notification Hubs**.

Ce didacticiel est également un prérequis pour le didacticiel **sur les notifications Push sécurisées**. Une fois que vous avez terminé les étapes de ce didacticiel **Notification des utilisateurs**, vous pouvez passer au didacticiel **Notifications Push sécurisées**, qui vous présente comment modifier le code **Notification des utilisateurs** pour envoyer une notification Push de façon sécurisée.

> [AZURE.NOTE] Ce didacticiel part du principe que vous avez créé et configuré votre concentrateur de notification comme décrit dans la rubrique [Prise en main de Notification Hubs (Windows Store)][Prise en main de Notification Hubs (Windows Store)].
> Notez aussi que dans ce didacticiel, vous allez créer une application Windows Phone 8.1. Le même code peut être utilisé pour les applications Windows Store et Windows universel. Toutes ces applications doivent utiliser des informations d'identification Windows (et non Windows Phone).

## Création et configuration du concentrateur de notification

Avant de commencer ce didacticiel, vous devez réserver un nom d'application, puis créer un concentrateur de notification Azure et le connecter à cette application. Suivez les étapes de la rubrique [Prise en main de Notification Hubs (Windows Store)][Prise en main de Notification Hubs (Windows Store)], en particulier les sections [Inscription de votre application pour le Windows Store][Inscription de votre application pour le Windows Store] et [Configuration de votre concentrateur de notification][Configuration de votre concentrateur de notification]. En particulier, assurez-vous que vous avez bien entré les valeurs de **SID du package** et **Clé secrète client** dans le portail, dans l'onglet **Configurer** de votre concentrateur de notification. Cette procédure de configuration est décrite dans la section [Configuration de votre concentrateur de notification][Configuration de votre concentrateur de notification]. Cette étape est essentielle : si les informations d'identification sur le portail ne correspondent pas à celles spécifiées pour le nom d'application que vous avez choisi, la notification Push ne fonctionnera pas.

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Création du projet Windows Phone

L'étape suivante consiste à créer l'application Windows Phone. Pour ajouter ce projet à la solution actuelle, procédez comme suit :

1.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le nœud de niveau supérieur de la solution (**Solution NotifyUsers** dans ce cas), puis cliquez sur **Ajouter** et sur **Nouveau projet**.

2.  Développez **Applications Store**, puis cliquez sur **Applications Windows Phone**, puis sur **Application vierge (Windows Phone)**.

    ![][0]

3.  Dans la zone **Nom**, tapez **NotifyUserWindowsPhone**, puis cliquez sur **OK** pour générer le projet.

4.  Associez cette application au Windows Phone Store : dans l'Explorateur de solutions, cliquez avec le bouton droit sur **NotifyUserWindowsPhone (Windows Phone 8.1)**, puis cliquez sur **Store** et sur **Associer l'application au Windows Store...**.

    ![][1]

5.  Suivez les étapes indiquées par l'Assistant pour vous connecter et associer l'application avec le Store.

    ![][2]

    > [AZURE.NOTE] Notez bien le nom que vous avez choisi pour l'application au cours de cette procédure. Vous devez configurer le concentrateur de notification sur le portail à l'aide des informations d'identification que vous obtenez depuis le [Centre de développement Windows][Centre de développement Windows] pour ce nom d'application réservé spécifique. Cette procédure de configuration est décrite dans la rubrique [Configuration de votre concentrateur de notification][Configuration de votre concentrateur de notification]. Cette étape est essentielle : si les informations d'identification sur le portail ne correspondent pas à celles spécifiées pour le nom d'application que vous avez choisi, la notification Push ne fonctionnera pas.

6.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet **NotifyUserWindowsPhone (Windows Phone 8.1)**, puis cliquez sur **Gérer les packages NuGet**.

7.  Dans la partie gauche, cliquez sur **En ligne**.

8.  Dans la zone **Rechercher**, entrez **Client Http**.

9.  Dans la liste de résultats, cliquez sur **Bibliothèques clientes HTTP Microsoft**, puis cliquez sur **Installer**. Terminez l'installation.

10. Dans la zone **Rechercher**, entrez **Json.net**. Installez le package **Json.NET**, puis fermez la fenêtre du Gestionnaire de package NuGet.

11. Dans l'Explorateur de solutions, dans le projet **NotifyUserWindowsPhone (Windows Phone 8.1)**, double-cliquez sur **MainPage.xaml** pour l'ouvrir dans l'éditeur de Visual Studio.

12. Dans le code XML de **MainPage.xaml**, remplacez la section `<Grid>` par le code qui suit :

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

13. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet **NotifyUserWindowsPhone (Windows Phone 8.1)**, cliquez sur **Ajouter**, puis sur **Classe**. Nommez la classe **RegisterClient.cs**, puis cliquez sur **OK** pour générer la classe. Ce composant met en œuvre les appels REST nécessaires pour contacter le serveur principal de l'application, afin de l'inscrire aux notifications Push. Il enregistre également en local les informations *registrationIds* créées par le concentrateur de notification, comme expliqué dans la rubrique [Inscription auprès du serveur principal de votre application][Inscription auprès du serveur principal de votre application]. Notez qu'il utilise un jeton d'autorisation qui se trouve dans le stockage local lorsque vous cliquez sur le bouton **Se connecter et s'inscrire**.

14. Ajoutez le code suivant dans la définition de classe `RegisterClient` : Remplacez bien `{backend endpoint}` par le point de terminaison de votre serveur principal, obtenu dans la section précédente :

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

15. Ajoutez les instructions `using` suivantes au début du fichier RegisterClient.cs :

        using Windows.Storage;
        using System.Net;
        using System.Net.Http;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;

16. Ajoutez le code pour les boutons dans MainPage.xaml.cs. Le rappel de **Log in and register** stocke le jeton d'authentification de base dans le stockage local (notez que cela représente n'importe quel jeton d'authentification utilisé par votre système), puis utilise `RegisterClient` pour appeler le serveur principal. Le rappel de **AppBackend** appelle le serveur principal pour déclencher une notification sécurisée vers tous les appareils de cet utilisateur.

    Ajoutez le code suivant dans MainPage.xaml.cs après la méthode `OnNavigatedTo()`. Remplacez bien `{backend endpoint}` par le point de terminaison du serveur principal obtenu dans la section précédente :

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

17. Ajoutez les instructions `using` suivantes au début du fichier MainPage.xaml.cs :

        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

## Exécution de l'application

Pour exécuter l'application, procédez comme suit :

1.  Dans Visual Studio, exécutez l'application d'API web **AppBackend**. Une page web ASP.NET s'affiche.

2.  Dans Visual Studio, exécutez l'application Windows Phone **NotifyUserWindowsPhone (Windows Phone 8.1)**. L'émulateur Windows Phone s'exécute et charge l'application automatiquement.

3.  Dans l'interface utilisateur de l'application **NotifyUserWindowsPhone**, entrez un nom d'utilisateur et un mot de passe. La valeur peut être une chaîne quelconque, mais elle doit être identique pour les deux.

4.  Dans l'interface utilisateur de l'application **NotifyUserWindowsPhone**, cliquez sur **Se connecter et s'inscrire** a. Cliquez ensuite sur **Send push**.

  [Inscription auprès du serveur principal de votre application]: http://msdn.microsoft.com/fr-fr/library/dn743807.aspx
  [Inscription de votre application pour le Windows Store]: http://azure.microsoft.com/fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#register
  [Configuration de votre concentrateur de notification]: http://azure.microsoft.com/fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#configure-hub
  [0]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
  [1]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
  [2]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
  [Centre de développement Windows]: http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409
