<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-dotnet" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your Windows Store app with Live Connect" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Store application." metaCanonical="" services="mobile-services" documentationCenter="" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Authentification unique de vos applications Windows Store avec Live Connect

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/fr-fr/develop/mobile/tutorials/single-sign-on-windows-8-dotnet" title="Windows Store C#" class="current">Windows Store C#</a><a href="/fr-fr/develop/mobile/tutorials/single-sign-on-windows-8-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/fr-fr/develop/mobile/tutorials/single-sign-on-wp8" title="Windows Phone" class="current">Windows Phone</a>
</div>

Cette rubrique vous montre comment utiliser la solution d'authentification unique Live Connect pour authentifier les utilisateurs dans Azure Mobile Services depuis une application Windows Store ou Windows Phone 8.1 Store. Dans ce didacticiel, vous allez ajouter le processus d'authentification au projet de démarrage rapide à l'aide de Live Connect. Après avoir été authentifié par Live Connect, l'utilisateur connecté est accueilli par son nom et son ID est affiché.

> [WACOM.NOTE]Ce didacticiel montre les avantages de l'authentification unique Live Connect pour les applications Windows Store. Cette fonction vous permet d'authentifier plus facilement un utilisateur déjà connecté avec votre service mobile. Pour une expérience d'authentification plus globale permettant la prise en charge de plusieurs fournisseurs de services d'authentification, consultez la rubrique [Prise en main de l'authentification][Prise en main de l'authentification].

Ce didacticiel vous familiarise avec les étapes de base pour activer l'authentification Live Connect :

1.  [Inscription de votre application pour l'authentification et configuration de Mobile Services][Inscription de votre application pour l'authentification et configuration de Mobile Services]
2.  [Restriction des autorisations de table pour les utilisateurs authentifiés][Restriction des autorisations de table pour les utilisateurs authentifiés]
3.  [Ajout de l'authentification à l'application][Ajout de l'authentification à l'application]

Ce didacticiel requiert les éléments suivants :

-   [Kit de développement logiciel (SDK) Live][Kit de développement logiciel (SDK) Live]
-   Microsoft Visual Studio 2012 Express pour Windows 8 RC ou version ultérieure

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services].

> [WACOM.NOTE]Ce didacticiel utilise un service mobile principal JavaScript. L'authentification gérée par client à l'aide de Live Connect ou d'autres clients d'authentification n'est pas encore prise en charge dans un service mobile principal .NET.

## <a name="register"></a>Inscription de votre application pour le Windows Store

Pour pouvoir authentifier les utilisateurs, vous devez soumettre votre application à Windows Store. Vous devez ensuite inscrire la clé secrète client pour intégrer Live Connect à Mobile Services.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

## <a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  Dans Visual Studio 2012 Express pour Windows 8, ouvrez le projet que vous avez créé avec le didacticiel [Prise en main de Mobile Services][1].

2.  Appuyez sur la touche F5 pour exécuter cette application basée sur le démarrage rapide ; vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application.

    Cela se produit, car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table *TodoItem* requiert désormais l'authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

## <a name="add-authentication"></a>Ajout de l'authentification à l'application

1.  Téléchargez et installez le [Kit de développement logiciel (SDK) Live][Kit de développement logiciel (SDK) Live].

2.  Dans le menu **Projet** de Visual Studio, cliquez sur **Ajouter une référence**, puis développez **Windows**, cliquez sur **Extensions**, cochez **Kit de développement logiciel (SDK)**, puis cliquez sur **OK**.

    ![][0]

    Cette action permet d'ajouter une référence au Kit de développement logiciel (SDK) Live dans le projet.

3.  Ouvrez le fichier projet MainPage.xaml.cs et ajoutez l'instruction using suivante :

        using Microsoft.Live;        

4.  Ajoutez l'extrait de code suivant à la classe MainPage :

        private LiveConnectSession session;
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            LiveAuthClient liveIdClient = new LiveAuthClient("<< INSERT REDIRECT DOMAIN HERE >>");

            while (session == null)
            {
                // Force a logout to make it easier to test with multiple Microsoft Accounts
                if (liveIdClient.CanLogout)
                    liveIdClient.Logout();

                LiveLoginResult result = await liveIdClient.LoginAsync(new[] { "wl.basic" });
                if (result.Status == LiveConnectSessionStatus.Connected)
                {
                    session = result.Session;
                    LiveConnectClient client = new LiveConnectClient(result.Session);
                    LiveOperationResult meResult = await client.GetAsync("me");
                    MobileServiceUser loginResult = await App.MobileService
                        .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                    string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                    var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                    var dialog = new MessageDialog(message, title);
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
                else
                {
                    session = null;
                    var dialog = new MessageDialog("You must log in.", "Login Required");
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
            }
         }

    Cela crée une variable membre pour le stockage de la session Live Connect actuelle et une méthode pour gérer le processus d'authentification. Ce code permet une déconnexion forcée, si possible, ce qui garantit ainsi que l'utilisateur est invité à entrer ses informations d'identification chaque fois que l'application s'exécute. Il facilite le test de l'application avec différents comptes Microsoft afin de s'assurer que l'authentification fonctionne correctement. Ce mécanisme n'est opérationnel que si l'utilisateur connecté n'a pas de compte Microsoft connecté.

    > [WACOM.NOTE]Vous ne devez pas effectuer de demande de jetons d'authentification Live Connect ou Mobile Services chaque fois que votre application s'exécute. Cette méthode est non seulement inefficace, mais vous pouvez rencontrer des problèmes d'utilisation si de nombreux clients tentent de lancer votre application en même temps. Il est préférable de mettre les jetons en cache et d'essayer tout d'abord d'utiliser le jeton Mobile Services en cache avant d'appeler **LoginWithMicrosoftAccountAsync**. Pour voir un exemple de mise en cache de ce jeton, consultez [Prise en main de l'authentification][2]

5.  Remplacez la chaîne *\<\< INSERT REDIRECT DOMAIN HERE \>\>* de l'étape précédente par le domaine de redirection qui a été spécifié lors de la configuration de l'application dans Live Connect, au format **https://_service-name_.azure-mobile.net/**.

    <div class="dev-callout"><b>Remarque</b>
<p>Dans une application Windows Store, une instance de la classe <strong>LiveAuthClient</strong> est cr&eacute;&eacute;e en transf&eacute;rant l'URI du domaine de redirection au constructeur de classe. Dans une <a href="/fr-fr/develop/mobile/tutorials/single-sign-on-wp8/">application Windows Phone&nbsp;8</a>, la m&ecirc;me classe est instanci&eacute;e en transmettant l'ID du client.</p>
</div>

6.  Remplacez le gestionnaire d'événements **OnNavigatedTo** existant par le gestionnaire qui appelle la nouvelle méthode **Authenticate** :

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            RefreshTodoItems();
        }

7.  Appuyez sur la touche F5 pour exécuter l'application et vous connecter à Live Connect avec votre compte Microsoft.

	Lorsque vous êtes connecté, l'application doit s'exécuter sans erreur et vous devez pouvoir exécuter des requêtes Mobile Services et mettre à jour les données.

## <a name="next-steps"> </a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation des utilisateurs avec des scripts][Autorisation des utilisateurs avec des scripts], vous allez utiliser la valeur de l'ID utilisateur fournie par Mobile Services en fonction de l'utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services. Pour plus d'informations sur l'utilisation d'autres fournisseurs d'identité à des fins d'authentification, consultez la page [Prise en main de l'authentification][3]. Obtenez plus d'informations sur Mobile Services avec .NET dans [le guide de fonctionnement Mobile Services .NET][le guide de fonctionnement Mobile Services .NET]

  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [Inscription de votre application pour l'authentification et configuration de Mobile Services]: #register
  [Restriction des autorisations de table pour les utilisateurs authentifiés]: #permissions
  [Ajout de l'authentification à l'application]: #add-authentication
  [Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started
  [mobile-services-register-windows-store-app]: ../includes/mobile-services-register-windows-store-app.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [1]: /fr-fr/documentation/articles/mobile-services-windows-store-get-started
  [0]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-add-reference-live-dotnet.png
  [2]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/#tokens
  [Autorisation des utilisateurs avec des scripts]: /fr-fr/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
  [3]: /fr-fr/develop/mobile/tutorials/get-started-with-users-dotnet
  [le guide de fonctionnement Mobile Services .NET]: /fr-fr/develop/mobile/how-to-guides/work-with-net-client-library
