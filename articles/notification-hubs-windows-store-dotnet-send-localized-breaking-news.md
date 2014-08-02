<properties linkid="develop-notificationhubs-tutorials-send-localized-breaking-news-windowsdotnet" urlDisplayName="Localized Breaking News" pageTitle="Notification Hubs Localized Breaking News Tutorial" metaKeywords="" description="Learn how to use Azure Service Bus Notification Hubs to send localized breaking news notifications." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send localized breaking news" authors="ricksal" solutions="" manager="" editor="" />

Utilisation de Notification Hubs pour envoyer les dernières nouvelles localisées
================================================================================

[Windows Store C\#](/en-us/manage/services/notification-hubs/breaking-news-localized-dotnet "Windows Store C#")[iOS](/en-us/manage/services/notification-hubs/breaking-news-localized-ios "iOS")

Cette rubrique montre comment utiliser la fonctionnalité de **modèle** d'Azure Notification Hubs pour diffuser des notifications relatives aux dernières nouvelles qui ont été localisées par langue et par appareil. Vous devez commencer ce didacticiel avec l'application Windows Store que vous avez créée dans le cadre du didacticiel [Utilisation de Notification Hubs pour envoyer les dernières nouvelles](/en-us/manage/services/notification-hubs/breaking-news-dotnet). Lorsque vous aurez terminé, vous pourrez vous inscrire aux catégories qui vous intéressent, spécifier une langue dans laquelle recevoir les notifications et recevoir uniquement des notifications Push pour les catégories sélectionnées dans cette langue.

Ce didacticiel vous familiarise avec les étapes de base pour activer ce scénario :

1.  [Concepts de modèle](#concepts)
2.  [Interface utilisateur de l'application](#ui)
3.  [Création de l'application cliente Windows Store](#building-client)
4.  [Envoi de notifications à partir de votre serveur principal](#send)

Ce scénario comporte deux parties :

-   L'application Windows Store permet aux appareils clients de spécifier une langue et de s'abonner à différentes catégories de dernières nouvelles.

-   Le serveur principal diffuse les notifications à l'aide des fonctionnalités de **balise** et de **modèle** d'Azure Notification Hubs.

Configuration requise
---------------------

Vous devez avoir suivi le didacticiel [Utilisation de Notifications Hubs pour envoyer les dernières nouvelles](/en-us/manage/services/notification-hubs/breaking-news-dotnet) et avoir le code à disposition, car le présent didacticiel est basé sur ce code.

Vous avez également besoin de Visual Studio 2012.

ConceptsConcepts de modèle
--------------------------

Dans le didacticiel [Utilisation de Notification Hubs pour envoyer les dernières nouvelles](/en-us/manage/services/notification-hubs/breaking-news-dotnet), vous avez créé une application qui se sert de **balises** pour s'abonner aux notifications relatives à différentes catégories de nouvelles. Cependant, de nombreuses applications sont destinées à plusieurs marchés et doivent donc être localisées. Cela signifie que le contenu des notifications proprement dites doit lui aussi être localisé et envoyé au bon ensemble d'appareils. Dans cette rubrique, nous allons vous montrer comment utiliser la fonctionnalité de **modèle** de Notification Hubs pour facilement envoyer des notifications de dernières nouvelles localisées.

Remarque : pour envoyer des notifications localisées, vous pouvez notamment créer plusieurs versions de chaque balise. Par exemple, pour prendre en charge l'anglais, le français et le mandarin, nous aurions besoin de trois balises différentes pour les nouvelles internationales : « world\_en », « world\_fr » et « world\_ch ». Il faudrait ensuite que nous envoyions une version localisée des nouvelles internationales à chacune de ces balises. Dans cette rubrique, nous utilisons des modèles afin d'éviter la prolifération de balises et d'éliminer la nécessité d'envoyer plusieurs messages.

À un haut niveau, les modèles permettent de spécifier comment un appareil particulier reçoit une notification. Le modèle spécifie le format de charge utile exact en se référant aux propriétés qui font partie du message envoyé par le serveur principal de votre application. Aux fins de notre exemple, nous allons envoyer un message de paramètres régionaux contenant toutes les langues prises en charge :

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Ensuite, nous allons nous assurer que les appareils s'inscrivent avec un modèle qui se réfère à la bonne propriété. Par exemple, une application Windows Store qui veut recevoir un simple message toast doit s'inscrire pour le modèle suivant :

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">$(News_English)</text>
        </binding>
      </visual>
    </toast>

Les modèles sont une fonctionnalité très puissante sur laquelle vous pouvez obtenir plus d'informations en lisant notre article [Recommandations relatives à Notification Hubs](http://msdn.microsoft.com/en-us/library/jj927170.aspx). Vous trouverez une référence pour le langage d'expression des modèles dans [Notification Hubs, procédures pour Windows Store](http://msdn.microsoft.com/en-us/library/jj927172.aspx).

Interface utilisateur de l'applicationInterface utilisateur de l'application
----------------------------------------------------------------------------

Nous allons maintenant modifier l'application de dernières nouvelles que vous avez créée à la rubrique [Utilisation de Notification Hubs pour envoyer les dernières nouvelles](/en-us/manage/services/notification-hubs/breaking-news-dotnet) pour envoyer les dernières nouvelles localisées à l'aide de modèles.

Pour adapter vos applications clientes afin qu'elles puissent recevoir des messages localisés, vous devez remplacer vos inscriptions *natives* (c.-à-d. les inscriptions qui ne spécifient pas de modèle) par des inscriptions avec modèle.

Dans votre application Windows Store :

Modifiez le fichier MainPage.xaml pour qu'il inclue une zone de liste modifiable pour les paramètres régionaux :

    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Dernières nouvelles" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Paramètres régionaux" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="Monde" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politique" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Économie" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technologie" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="Button_Click" />
    </Grid>

Interface utilisateur de l'applicationCréation de l'application cliente Windows Store
-------------------------------------------------------------------------------------

1.  Dans la classe Notifications, ajoutez un paramètre de paramètre régional aux méthodes *StoreCategoriesAndSubscribe* et *SubscribeToCateories*.

         public async Task StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
         {
             ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
             ApplicationData.Current.LocalSettings.Values["locale"] = locale;
             await SubscribeToCategories(locale, categories);
         }

         public async Task SubscribeToCategories(string locale, IEnumerable<string> categories)
         {
             var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
             var template = String.Format(@"<toast><visual><binding template=""ToastText01""><text id=""1"">$(News_{0})</text></binding></visual></toast>", locale);

             await hub.RegisterTemplateAsync(channel.Uri, template, "newsTemplate", categories);
         }

    Vous remarquerez qu'au lieu d'appeler la méthode *RegisterNativeAsync*, nous appelons *RegisterTemplateAsync* : en effet, nous inscrivons un format de notification spécifique dans lequel le modèle dépend des paramètres régionaux. Nous avons également fourni un nom pour le modèle (« newsTemplate »), parce qu'il est possible que nous inscrivions plusieurs modèles (par exemple un pour les notifications toast et un pour les vignettes) et nous devons donc les nommer pour pouvoir les mettre à jour ou les supprimer.

    Notez que si un appareil inscrit plusieurs modèles avec la même balise, un message entrant ciblant cette balise entraînera l'envoi de plusieurs notifications à l'appareil (un pour chaque modèle). Ce comportement s'avère utile lorsque le même message logique doit générer plusieurs notifications visuelles, par exemple affichant un badge et un toast dans une application Windows Store.

2.  Ajoutez la méthode suivante pour extraire les paramètres régionaux stockés :

         public string RetrieveLocale()
         {
             var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
             return locale != null 
         locale : "English";
         }

3.  Dans le fichier MainPage.xaml.cs, mettez le gestionnaire de clics de bouton à jour en extrayant la valeur actuelle de la zone de liste modifiable Paramètres régionaux et en la fournissant à l'appel de la classe Notifications, comme indiqué ci-après :

          var locale = (string)Locale.SelectedItem;
                
          var categories = new HashSet<string>();
          if (WorldToggle.IsOn) categories.Add("Monde");
          if (PoliticsToggle.IsOn) categories.Add("Politique");
          if (BusinessToggle.IsOn) categories.Add("Économie");
          if (TechnologyToggle.IsOn) categories.Add("Technologie");
          if (ScienceToggle.IsOn) categories.Add("Science");
          if (SportsToggle.IsOn) categories.Add("Sports");

          await ((App)Application.Current).Notifications.StoreCategoriesAndSubscribe(locale, categories);

          var dialog = new MessageDialog(String .Format("Paramètres régionaux : {0}; Abonné à : {1}", locale, string.Join(",", categories)));
          dialog.Commands.Add(new UICommand("OK"));
          await dialog.ShowAsync();

4.  Enfin, dans le fichier App.xaml.cs, n'oubliez pas de mettre à jour l'appel du singleton Notifications dans la méthode *OnLaunched*.

         Notifications.SubscribeToCategories(Notifications.RetrieveLocale(), Notifications.RetrieveCategories());

Envoi de notifications localiséesEnvoi de notifications localisées à partir de votre serveur principal
------------------------------------------------------------------------------------------------------

[WACOM.INCLUDE [notification-hubs-localized-back-end](../includes/notification-hubs-localized-back-end.md)]

Étapes suivantes
----------------

Pour plus d'informations sur les scénarios de notification des utilisateurs, consultez [Notification des utilisateurs avec Notification Hubs : ASP.NET](/en-us/manage/services/notification-hubs/notify-users-aspnet), [Notification des utilisateurs avec Notification Hubs : Mobile Services](/en-us/manage/services/notification-hubs/notify-users) et [Recommandations relatives à Notification Hubs](http://msdn.microsoft.com/en-us/library/jj927170.aspx). Vous trouverez une référence pour le langage d'expression des modèles dans [Notification Hubs, procédures pour Windows Store](http://msdn.microsoft.com/en-us/library/jj927172.aspx).

