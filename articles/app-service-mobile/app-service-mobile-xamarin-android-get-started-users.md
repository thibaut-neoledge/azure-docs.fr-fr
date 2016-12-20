---
title: Prise en main de l&quot;authentification pour Mobile Apps dans Xamarin Android
description: "Découvrez comment utiliser Mobile Apps pour authentifier les utilisateurs de votre application Xamarin Android via divers fournisseurs d&quot;identité, notamment AAD, Google, Facebook, Twitter et Microsoft."
services: app-service\mobile
documentationcenter: xamarin
author: adrianhall
manager: dwrede
editor: 
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4c0ba82ca010f1ee571424fa3d650718e5acdd8b


---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Ajout de l'authentification à votre application Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Cette rubrique montre comment authentifier les utilisateurs d'une application Mobile App à partir de votre application cliente. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Azure Mobile Apps. Une fois l'utilisateur authentifié et autorisé dans l’application Mobile App, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel est basé sur le démarrage rapide de Mobile App. Vous devez également commencer par suivre le didacticiel [Création d’une application Xamarin.Android]. Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter le package d’extension d’authentification à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="a-nameregisteraregister-your-app-for-authentication-and-configure-app-services"></a><a name="register"></a>Inscription de votre application pour l’authentification et configuration d’App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="a-namepermissionsarestrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Dans Visual Studio ou Xamarin Studio, exécutez le projet client sur un appareil ou un émulateur. Vérifiez qu'une exception non gérée avec un code d'état 401 (Non autorisé) est générée après le démarrage de l'application. Cette exception se produit, car l'application tente d'accéder à votre serveur principal d’applications mobiles en tant qu'utilisateur non authentifié. La table *TodoItem* nécessite désormais une authentification.

Ensuite, vous mettrez à jour l’application cliente pour demander des ressources au backend Mobile App avec un utilisateur authentifié.

## <a name="a-nameadd-authenticationaadd-authentication-to-the-app"></a><a name="add-authentication"></a>Ajouter l'authentification à l'application
L'application est mise à jour de manière à demander aux utilisateurs de cliquer sur le bouton **Se connecter** et de s'authentifier avant que les données ne s'affichent.

1. Ajoutez le code suivant à la classe **TodoActivity** :
   
        // Define a authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook);
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");
   
                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }
   
        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;
   
                // Load the data.
                OnRefreshItemsSelected();
            }
        }
   
    Cela crée une nouvelle méthode pour authentifier un utilisateur et un gestionnaire de méthode pour un nouveau bouton **Se connecter** . L'utilisateur de l'exemple de code ci-dessus est authentifié à l'aide d'une connexion Facebook. Une boîte de dialogue permet d'afficher l'ID d'utilisateur une fois authentifié.
   
   > [!NOTE]
   > Si vous utilisez un autre fournisseur d’identité que Facebook, remplacez la valeur passée à la méthode **LoginAsync** ci-dessus par l’une des valeurs suivantes : *MicrosoftAccount*, *Twitter*, *Google* ou *WindowsAzureActiveDirectory*.
   > 
   > 
2. Dans la méthode **OnCreate** , supprimez ou supprimez les marques de commentaire de la ligne de code suivante :
   
        OnRefreshItemsSelected ();
3. Dans le fichier Activity_To_Do.axml, ajoutez la définition de bouton *LoginUser* suivante avant le bouton *AddItem* existant :
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Ajoutez l’élément suivant au fichier de ressources Strings.xml :
   
        <string name="login_button_text">Sign in</string>
5. Dans Visual Studio ou Xamarin Studio, exécutez le projet client sur un appareil ou un émulateur et connectez-vous avec le fournisseur d’identité que vous avez choisi.
   
       When you are successfully logged-in, the app will display your login ID and the list of todo items, and you can make updates to the data.

<!-- URLs. -->
[Création d’une application Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md



<!--HONumber=Nov16_HO3-->


