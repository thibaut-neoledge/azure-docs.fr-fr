<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-aspnet-webapi-windowsphonedotnet" urlDisplayName="Notify Windows Store app users by using Web API" pageTitle="Register the current user for push notifications by using Web API - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification Windows Store app" description="Learn how to request push notification registration in a Windows Store app with Azure Notification Hubs when registeration is performed by ASP.NET Web API." metaCanonical="" services="notification-hubs" documentationCenter="" title="Register the current user for push notifications by using ASP.NET" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Inscription de l'utilisateur actif aux notifications Push à l'aide d'ASP.NET

<div class="dev-center-tutorial-selector sublanding">
    <a href="/fr-fr/documentation/articles/notification-hubs-windows-store-aspnet-register-user-push-notifications/" title="Windows Store C#" class="current">Windows Store C#</a><a href="/fr-fr/documentation/articles/notification-hubs-ios-aspnet-register-user-push-notifications/" title="iOS">iOS</a>
</div>

Cette rubrique montre comment demander une inscription aux notifications Push avec Azure Notification Hubs lorsque l'inscription est réalisée par l'API Web ASP.NET. Cette rubrique s'inscrit dans le prolongement du didacticiel [Notification des utilisateurs avec Notification Hubs][Notification des utilisateurs avec Notification Hubs]. Vous devez avoir suivi les étapes de ce didacticiel permettant de créer le service mobile authentifié. Pour plus d'informations sur les scénarios de notification des utilisateurs, consultez la rubrique [Notification des utilisateurs avec Notification Hubs][Notification des utilisateurs avec Notification Hubs].

1.  Dans Visual Studio 2012, ouvrez le fichier app.xaml.cs du projet que vous avez créé avec le didacticiel prérequis [Notifications des utilisateurs avec Notification Hubs][Notification des utilisateurs avec Notification Hubs].

2.  Localisez la méthode **InitNotificationsAsync** et placez le code en commentaire dans la méthode.

    Vous allez utiliser l'API Web ASP.NET pour l'inscription aux notifications.

3.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Gérer les packages NuGet**.

4.  Dans le volet gauche, sélectionnez la catégorie **En ligne**, recherchez `json.net`, cliquez sur **Installer** au niveau du package **Json.NET**, puis acceptez le contrat de licence.

    ![][0]

    L'assembly tiers Newtonsoft.Json.dll est ajouté à votre projet.

5.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet, cliquez sur **Ajouter**, sur **Classe**, puis tapez `LocalStorageManager` et cliquez sur **Ajouter**.

    ![][1]

    Un fichier de code est alors ajouté au projet pour la classe **LocalStorageManager**.

6.  Ouvrez le nouveau fichier projet LocalStorageManager.cs et ajoutez l'instruction **using** suivante :

        using Windows.Storage;

7.  Remplacez la définition de la classe **LocalStorageManager** par le code suivant :

        class LocalStorageManager
        {
            private static ApplicationDataContainer GetLocalStorageContainer()
            {
                if (!ApplicationData.Current.LocalSettings
                    .Containers.ContainsKey("InstallationContainer"))
                {
                    ApplicationData.Current.LocalSettings
                        .CreateContainer("InstallationContainer",                                                            
                        ApplicationDataCreateDisposition.Always);
                }
                return ApplicationData.Current.LocalSettings
                    .Containers["InstallationContainer"];
            }

            public static string GetInstallationId()
            {
                var container = GetLocalStorageContainer();
                if (!container.Values.ContainsKey("InstallationId"))
                {
                    container.Values["InstallationId"] = Guid.NewGuid().ToString();
                }
                return (string)container.Values["InstallationId"];
            }
        }

    Ce code crée et stocke un ID d'installation propre à l'appareil et utilisé comme balise lors de la création de notifications. Si un ID d'installation existe déjà, il est utilisé.

8.  Ouvrez le fichier projet MainPage.xaml, puis remplacez l'élément racine **Grille** par le code XAML suivant :

        <Grid Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
            <Grid Margin="120, 58, 120, 80" >
                <Grid.RowDefinitions>
                    <RowDefinition />
                    <RowDefinition/>
                    <RowDefinition/>
                    <RowDefinition/>
                    <RowDefinition/>
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition MaxWidth="200"/>
                    <ColumnDefinition/>
                </Grid.ColumnDefinitions>
                <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  
                               TextWrapping="Wrap" Text="Push To Users with Notification Hubs" 
                               FontSize="42"  VerticalAlignment="Top"/>
                <TextBlock Grid.Row="1" Grid.Column="0" Text="Installation Id:" 
                               FontSize="24" VerticalAlignment="Center" />
                <TextBlock Grid.Row="1" Grid.Column="1" Name="InstId" FontSize="24" 
                               VerticalAlignment="Center"/>
                <TextBlock Grid.Row="2" Grid.Column="0" Text="Username:" FontSize="24" 
                               VerticalAlignment="Center"/>
                <TextBox Grid.Row="2" Grid.Column="1" Name="User" FontSize="24" 
                             Width="300" Height="40" HorizontalAlignment="Left"/>
                <TextBlock Grid.Row="3" Grid.Column="0" Text="Password:" FontSize="24" 
                               VerticalAlignment="Center" />
                <PasswordBox Grid.Row="3" Grid.Column="1" Name="Password" FontSize="24" 
                                 Width="300" Height="40" HorizontalAlignment="Left"/>
                <Button Name="Login" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" 
                            Content="Login and Register for Push" FontSize="24" Click="Login_Click" />
            </Grid>
        </Grid>

    Ce code crée une interface utilisateur pour la collecte d'un nom d'utilisateur et d'un mot de passe et l'affichage de l'ID d'installation.

9.  Ouvrez le fichier MainPage.xaml.cs, puis ajoutez les instructions **using** suivantes :

        using Newtonsoft.Json;
        using System.Net.Http;
        using System.Text;
        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

10. Ajoutez la définition suivante à la classe **MainPage** :

        private string registerUri = "https://<SERVICE_ROOT_URL>/api/register";

    Remplacez *`<SERVICE_ROOT_URL>`* par l'URI racine de l'API web que vous avez créée dans le cadre du didacticiel **Notification des utilisateurs avec Notification Hubs**.

11. Ajoutez la méthode suivante à la classe **MainPage** :

        private async void Login_Click(object sender, RoutedEventArgs e)
        {
            // Get the info that we need to request registration.
            var installationId = InstId.Text = LocalStorageManager.GetInstallationId();
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();
            var user = User.Text;
            var pwd = Password.Password;

            // Define a registration to pass in the body of the POST request.
            var registration = new Dictionary<string, string>()
                                   {{"platform", "windows"},
                                   {"instId", installationId.ToString()},
                                   {"channelUri", channel.Uri}};

            // Create a client to send the HTTP registration request.
            var client = new HttpClient();
            var request =
                new HttpRequestMessage(HttpMethod.Post, new Uri(registerUri));

            // Add the Authorization header with the basic login credentials.
            var auth = "Basic " +
                Convert.ToBase64String(Encoding.UTF8.GetBytes(user + ":" + pwd));
            request.Headers.Add("Authorization", auth);
            request.Content = new StringContent(JsonConvert.SerializeObject(registration),
                Encoding.UTF8, "application/json");

            string message;

            try
            {
                // Send the registration request.
                HttpResponseMessage response = await client.SendAsync(request);

                // Get and display the response, either the registration ID
                // or an error message.
                message = await response.Content.ReadAsStringAsync();
                message = string.Format("Registration ID: {0}", message);
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display a message dialog.
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

    Cette méthode obtient à la fois un ID d'installation et un canal pour les notifications Push et les envoie avec le type d'appareil à la méthode d'API Web authentifiée qui crée une inscription dans Notification Hubs. Cette API Web a été définie dans le cadre du didacticiel [Notification des utilisateurs avec Notification Hubs][Notification des utilisateurs avec Notification Hubs].

Maintenant que l'application cliente est à jour, retournez au didacticiel [Notification des utilisateurs avec Notification Hubs][Notification des utilisateurs avec Notification Hubs] et mettez le service mobile à jour pour qu'il envoie des notifications à l'aide de Notification Hubs.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Notification des utilisateurs avec Notification Hubs]: /fr-fr/manage/services/notification-hubs/notify-users-aspnet
  [0]: ./media/notification-hubs-windows-store-aspnet-register-user-push-notifications/notification-hub-add-nuget-package-json.png
  [1]: ./media/notification-hubs-windows-store-aspnet-register-user-push-notifications/notification-hub-create-aspnet-class.png
