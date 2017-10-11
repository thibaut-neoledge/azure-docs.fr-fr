---
title: Notification des utilisateurs via Azure Notification Hubs avec service principal .NET
description: "Découvrez comment envoyer des notifications Push sécurisées dans Azure. Exemples de code écrits en C# à l’aide de l’API .NET."
documentationcenter: windows
author: ysxu
manager: erikre
services: notification-hubs
editor: 
ms.assetid: 012529f2-fdbc-43c4-8634-2698164b5880
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: c0b963ef661612b1a176dd8e5f01d56e61eb5acb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="azure-notification-hubs-notify-users-with-net-backend"></a>Notification des utilisateurs via Azure Notification Hubs avec service principal .NET
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>Vue d'ensemble
La prise en charge des notifications Push dans Azure vous permet d’accéder à une infrastructure Push conviviale, multi-plateforme et avec montée en charge qui simplifie fortement l’implémentation des notifications Push pour les applications consommateur et entreprise pour les plateformes mobiles. Ce didacticiel explique comment utiliser Azure Notification Hubs pour envoyer des notifications Push à un utilisateur particulier d'une application sur un appareil spécifique. Un serveur principal WebAPI ASP.NET est utilisé pour authentifier les clients. Il ajoute automatiquement une balise à l’enregistrement des notifications en se basant sur l’utilisateur du client authentifié. Cette balise sera utilisée pour l’envoi par le serveur principal afin de générer des notifications pour un utilisateur spécifique. Pour plus d’informations sur l’enregistrement pour les notifications à l’aide d’un serveur principal d’application, consultez la rubrique d’aide [Inscription auprès du serveur principal de votre application](http://msdn.microsoft.com/library/dn743807.aspx). Ce didacticiel fait intervenir le hub de notification et le projet que vous avez créés dans le didacticiel intitulé [Prise en main de Notification Hubs] .

Ce didacticiel est également un prérequis pour le didacticiel [sur les notifications Push sécurisées] . Une fois que vous avez suivi la procédure de ce didacticiel, vous pouvez passer au didacticiel [sur les notifications Push sécurisées] qui explique comment modifier le code de ce didacticiel pour envoyer une notification Push en toute sécurité.

## <a name="before-you-begin"></a>Avant de commencer
Nous accordons de l’importance à vos commentaires. Si vous avez des difficultés à terminer cette rubrique ou si vous avez des conseils pour améliorer ce contenu, nous apprécierions que vous nous fassiez part de vos commentaires en bas de la page.

Le code complet de ce didacticiel est disponible sur GitHub [ici](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers). 

## <a name="prerequisites"></a>Composants requis
Avant de commencer ce didacticiel, vous devez suivre les didacticiels Mobile Services suivants :

* [Prise en main de Notification Hubs]<br/>Ce didacticiel vous permet de créer votre concentrateur de notification, de réserver le nom de l’application et de vous inscrire pour recevoir des notifications. Ce didacticiel part du principe que vous avez déjà effectué ces étapes. Si ce n’est pas le cas, suivez les étapes de la rubrique [Prise en main de Notification Hubs (Windows Store)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), en particulier les sections [Inscrire votre application auprès du Windows Store](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#register-your-app-for-the-windows-store) et [Configurer votre hub de notification](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#configure-your-notification-hub). Vérifiez aussi que vous avez bien entré les valeurs **SID du package** et **Clé secrète client** dans le portail, sous l’onglet **Configurer** de votre hub de notification. Cette procédure de configuration est décrite dans la section [Configuration de votre concentrateur de notification](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#configure-your-notification-hub). Il s’agit d’une étape importante : si les informations d’identification sur le portail ne correspondent pas à celles spécifiées pour le nom d’application que vous avez choisi, la notification Push ne fonctionnera pas.

> [!NOTE]
> Si vous utilisez Mobile Apps dans Azure App Service comme service principal, consultez la [version Mobile Apps](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md) de ce didacticiel.
> 
> 

&nbsp;

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="update-the-code-for-the-client-project"></a>Mise à jour du code pour le projet client
Dans cette section, vous allez mettre à jour le code du projet que vous avez terminé lors du didacticiel [Prise en main de Notification Hubs] . Il doit déjà être associé au magasin et configuré pour votre hub de notification. Dans cette section, vous allez ajouter du code pour appeler le nouveau serveur principal WebAPI et l’utiliser pour l’enregistrement et l’envoi de notifications.

1. Dans Visual Studio, ouvrez la solution que vous avez créée lors du didacticiel [Prise en main de Notification Hubs] .
2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **(Windows 8.1)**, puis cliquez sur **Gérer les packages NuGet**.
3. Dans la partie gauche, cliquez sur **En ligne**.
4. Dans la zone **Rechercher**, entrez **Client Http**.
5. Dans la liste de résultats, cliquez sur **Bibliothèques clientes HTTP Microsoft**, puis cliquez sur **Installer**. Terminez l'installation.
6. De retour dans la zone **Recherche** NuGet, tapez **Json.net**. Installez le package **Json.NET** , puis fermez la fenêtre du Gestionnaire de package NuGet.
7. Répétez les étapes ci-dessus pour le projet **(Windows Phone 8.1)** afin d’installer le package NuGet **JSON.NET** pour le projet Windows Phone.
8. Dans l’Explorateur de solutions, dans le projet **(Windows 8.1)**, double-cliquez sur **MainPage.xaml** pour l’ouvrir dans l’éditeur de Visual Studio.
9. Dans le code XML de **MainPage.xaml**, remplacez la section `<Grid>` par le code suivant. Ce code ajoute une zone de texte Nom d’utilisateur et Mot de passe pour l’authentification de l’utilisateur. Il ajoute également des zones de texte pour le message de notification et la balise username qui doit recevoir la notification :
   
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
   
            <TextBlock Grid.Row="0" Text="Notify Users" HorizontalAlignment="Center" FontSize="48"/>
   
            <StackPanel Grid.Row="1" VerticalAlignment="Center">
                <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition></ColumnDefinition>
                        <ColumnDefinition></ColumnDefinition>
                        <ColumnDefinition></ColumnDefinition>
                    </Grid.ColumnDefinitions>
                    <TextBlock Grid.Row="0" Grid.ColumnSpan="3" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="UsernameTextBox" Grid.Row="1" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
                    <TextBlock Grid.Row="2" Grid.ColumnSpan="3" Text="Password" FontSize="24" Margin="20,0,20,0" />
                    <PasswordBox Name="PasswordTextBox" Grid.Row="3" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
   
                    <Button Grid.Row="4" Grid.ColumnSpan="3" HorizontalAlignment="Center" VerticalAlignment="Center"
                                Content="1. Login and register" Click="LoginAndRegisterClick" Margin="0,0,0,20"/>
   
                    <ToggleButton Name="toggleWNS" Grid.Row="5" Grid.Column="0" HorizontalAlignment="Right" Content="WNS" IsChecked="True" />
                    <ToggleButton Name="toggleGCM" Grid.Row="5" Grid.Column="1" HorizontalAlignment="Center" Content="GCM" />
                    <ToggleButton Name="toggleAPNS" Grid.Row="5" Grid.Column="2" HorizontalAlignment="Left" Content="APNS" />
   
                    <TextBlock Grid.Row="6" Grid.ColumnSpan="3" Text="Username Tag To Send To" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="ToUserTagTextBox" Grid.Row="7" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                    <TextBlock Grid.Row="8" Grid.ColumnSpan="3" Text="Enter Notification Message" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="NotificationMessageTextBox" Grid.Row="9" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                    <Button Grid.Row="10" Grid.ColumnSpan="3" HorizontalAlignment="Center" Content="2. Send push" Click="PushClick" Name="SendPushButton" />
                </Grid>
            </StackPanel>
        </Grid>
10. Dans l’Explorateur de solutions, dans le projet **(Windows Phone 8.1)**, ouvrez **MainPage.xaml** et remplacez la section Windows Phone 8.1 `<Grid>` par le code précédent. L’interface doit ressembler à ce qui suit.
    
    ![][13]
11. Dans l’Explorateur de solutions, ouvrez le fichier **MainPage.xaml.cs** pour les projets **(Windows 8.1)** et **(Windows Phone 8.1)**. Ajoutez les instructions `using` suivantes au début des deux fichiers :
    
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;
        using System.Threading.Tasks;
12. Dans **MainPage.xaml.cs** pour les projets **(Windows 8.1)** et **(Windows Phone 8.1)**, ajoutez le membre suivant à la classe `MainPage`. Remplacez bien `<Enter Your Backend Endpoint>` par le point de terminaison réel de votre serveur principal, obtenu précédemment. Par exemple : `http://mybackend.azurewebsites.net`.
    
        private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
13. Ajoutez le code ci-dessous à la classe MainPage dans **MainPage.xaml.cs** pour les projets **(Windows 8.1)** et **(Windows Phone 8.1)**.
    
    La méthode `PushClick` correspond au gestionnaire de clic du bouton **Envoyer des notifications Push** . Elle appelle le serveur principal pour déclencher une notification vers tous les appareils avec une balise de nom d’utilisateur correspondant au paramètre `to_tag` . Le message de notification est envoyé en tant que contenu JSON dans le corps de la demande.
    
    La méthode `LoginAndRegisterClick` correspond au gestionnaire de clic du bouton **Se connecter et s’inscrire** . Elle stocke le jeton d’authentification de base dans un stockage local (notez que cela représente n’importe quel jeton utilisé par votre système d’authentification), puis utilise `RegisterClient` pour l’inscription aux notifications à l’aide du serveur principal.

        private async void PushClick(object sender, RoutedEventArgs e)
        {
            if (toggleWNS.IsChecked.Value)
            {
                await sendPush("wns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
            }
            if (toggleGCM.IsChecked.Value)
            {
                await sendPush("gcm", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
            }
            if (toggleAPNS.IsChecked.Value)
            {
                await sendPush("apns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);

            }
        }

        private async Task sendPush(string pns, string userTag, string message)
        {
            var POST_URL = BACKEND_ENDPOINT + "/api/notifications?pns=" +
                pns + "&to_tag=" + userTag;

            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                try
                {
                    await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                        System.Text.Encoding.UTF8, "application/json"));
                }
                catch (Exception ex)
                {
                    MessageDialog alert = new MessageDialog(ex.Message, "Failed to send " + pns + " message");
                    alert.ShowAsync();
                }
            }
        }

        private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
        {
            SetAuthenticationTokenInLocalStorage();

            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            // The "username:<user name>" tag gets automatically added by the message handler in the backend.
            // The tag passed here can be whatever other tags you may want to use.
            try
            {
                // The device handle used will be different depending on the device and PNS. 
                // Windows devices use the channel uri as the PNS handle.
                await new RegisterClient(BACKEND_ENDPOINT).RegisterAsync(channel.Uri, new string[] { "myTag" });

                var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
                SendPushButton.IsEnabled = true;
            }
            catch (Exception ex)
            {
                MessageDialog alert = new MessageDialog(ex.Message, "Failed to register with RegisterClient");
                alert.ShowAsync();
            }
        }

        private void SetAuthenticationTokenInLocalStorage()
        {
            string username = UsernameTextBox.Text;
            string password = PasswordTextBox.Password;

            var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
            ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
        }



1. Dans l’Explorateur de solutions, sous le projet **Partagé**, ouvrez le fichier **App.xaml.cs**. Recherchez l’appel vers `InitNotificationsAsync()` in the `OnLaunched()` . Commentez ou supprimez l’appel vers `InitNotificationsAsync()`. Le gestionnaire de bouton ajouté précédemment initialise les inscriptions aux notifications.

        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
            //InitNotificationsAsync();


1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **Partagé**, puis cliquez sur **Ajouter** et sur **Classe**. Nommez la classe **RegisterClient.cs**, puis cliquez sur **OK** pour générer la classe.
   
   Cette classe encapsule les appels REST nécessaires pour contacter le service principal de l'application et inscrire cette dernière pour les notifications Push. Il enregistre également en local les informations *registrationIds* créées par le hub de notification, comme expliqué dans la rubrique [Inscription auprès du serveur principal de votre application](http://msdn.microsoft.com/library/dn743807.aspx). Notez qu'il utilise un jeton d'autorisation qui se trouve dans le stockage local lorsque vous cliquez sur le bouton **Se connecter et s'inscrire** .
2. Ajoutez les instructions `using` suivantes au début du fichier RegisterClient.cs :
   
       using Windows.Storage;
       using System.Net;
       using System.Net.Http;
       using System.Net.Http.Headers;
       using Newtonsoft.Json;
       using System.Threading.Tasks;
       using System.Linq;
3. Ajoutez le code suivant dans la définition de classe `RegisterClient` .
   
       private string POST_URL;
   
       private class DeviceRegistration
       {
           public string Platform { get; set; }
           public string Handle { get; set; }
           public string[] Tags { get; set; }
       }
   
       public RegisterClient(string backendEndpoint)
       {
           POST_URL = backendEndpoint + "/api/register";
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
               throw new System.Net.WebException(statusCode.ToString());
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
                       throw new System.Net.WebException(response.StatusCode.ToString());
                   }
               }
           }
           return (string)settings["__NHRegistrationId"];
   
       }
4. Enregistrez toutes vos modifications.

## <a name="testing-the-application"></a>Test de l’application
1. Lancez l’application sur Windows 8.1 et Windows Phone 8.1. Pour Windows Phone 8.1, vous pouvez exécuter l’instance dans l’émulateur ou avec un appareil ciblé.
2. Dans l’instance Windows 8.1 de l’application, entrez un **Nom d’utilisateur** et un **Mot de passe**, comme indiqué dans l’écran ci-dessous. Ces informations d’identification doivent différer du nom d’utilisateur et du mot de passe entrés sur Windows Phone.
3. Cliquez sur **Se connecter et s’inscrire** et vérifiez le contenu de la boîte de dialogue indiquant que vous êtes connecté. Cette opération active également le bouton **Envoyer des notifications Push** .
   
    ![][14]
4. Sur l’instance Windows Phone 8.1, entrez une chaîne de nom d’utilisateur dans les champs **Nom d’utilisateur** et **Mot de passe**, puis cliquez sur **Se connecter et s’inscrire**.
5. Puis, dans le champ **Balise de nom d’utilisateur** , entrez le nom d’utilisateur enregistré sur Windows 8.1. Entrez un message de notification, puis cliquez sur **Envoyer des notifications Push**.
   
    ![][16]
6. Seuls les appareils enregistrés avec la balise de nom d’utilisateur correspondant reçoivent le message de notification.
   
    ![][15]

## <a name="next-steps"></a>Étapes suivantes
* Pour segmenter vos utilisateurs par groupes d'intérêt, consultez la page [Utilisation des Notification Hubs pour diffuser les dernières nouvelles].
* Pour en savoir plus sur l'utilisation de Notification Hubs, consultez la page [Recommandations relatives à Notification Hubs].

[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png



<!-- URLs. -->
[Prise en main de Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[sur les notifications Push sécurisées]: notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md
[Utilisation des Notification Hubs pour diffuser les dernières nouvelles]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Recommandations relatives à Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
