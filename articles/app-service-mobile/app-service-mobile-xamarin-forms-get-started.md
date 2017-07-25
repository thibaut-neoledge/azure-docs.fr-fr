---
title: "Prise en main de Mobile Apps à l’aide de Xamarin.Forms"
description: "Suivez ce didacticiel pour commencer à utiliser Azure Mobile Apps pour le développement Xamarin.Forms"
services: app-service\mobile
documentationcenter: xamarin
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 6b8c5c2ac2e721c4d6f73c7c17f34eadc041e0c9
ms.contentlocale: fr-fr
ms.lasthandoff: 01/20/2017

---
# <a name="create-a-xamarinforms-app"></a>Créer une application Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Vue d'ensemble
Ce didacticiel vous montre comment ajouter un service principal cloud à une application Xamarin.Forms en utilisant un serveur principal d’applications mobiles Azure. Vous allez créer un serveur principal d’applications mobiles et une simple application Xamarin.Forms *Todo list* qui stocke les données d’application dans Azure.

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Mobile Apps pour les applications Xamarin.Forms.

## <a name="prerequisites"></a>Composants requis
Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous n’avez pas de compte, vous pouvez vous inscrire pour obtenir une version d’évaluation Azure et jusqu’à 10 applications Mobile App gratuites que vous pourrez conserver après l’expiration de votre période d’évaluation. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio avec Xamarin. Pour obtenir des instructions, consultez la page [Configuration et installation pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) .
* Un Mac sur lequel sont installés Xcode v7.0 ou version ultérieure et Xamarin Studio Community. Consultez la page [Configuration et installation pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) et [Configuration, installation et vérifications pour les utilisateurs de Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

> [!NOTE]
> Si vous voulez prendre en main Azure App Service avant de créer un compte Azure, accédez à la page [Essayer App Service](https://azure.microsoft.com/try/app-service/mobile/), où vous pouvez créer immédiatement une première application mobile temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
>
>

## <a name="create-a-new-azure-mobile-app-backend"></a>Créer un serveur principal d'applications mobiles Azure
Suivez ces étapes pour créer un serveur principal d’application mobile.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Vous avez maintenant configuré un serveur principal d’application mobile Azure qui peut être utilisé par vos applications clientes mobiles. Vous allez ensuite télécharger un projet de serveur pour un serveur principal « todo list » simple et le publier dans Azure.

## <a name="configure-the-server-project"></a>Configurer le projet de serveur
Suivez les étapes ci-dessous pour configurer le projet de serveur de sorte qu’il utilise le serveur principal Node.js ou .NET.

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>Télécharger et exécuter la solution Xamarin.Forms
Plusieurs options s’offrent à vous. Vous pouvez télécharger la solution sur un Mac et l’ouvrir dans Xamarin Studio, ou bien télécharger la solution sur un ordinateur Windows et l’ouvrir dans Visual Studio à l’aide d’un ordinateur Mac connecté au réseau pour la création de l’application iOS. Consultez [Configuration et installation pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) si vous avez besoin d’instructions plus détaillées sur les scénarios de configuration de Xamarin.

Commençons :

1. Sur votre Mac ou votre ordinateur Windows, ouvrez le [portail Azure] dans une fenêtre de navigateur.
2. Dans le panneau Paramètres de votre application mobile, cliquez sur **Get Started** (sous Mobile) > **Xamarin.Forms**. À l’Étape 3, cliquez sur **Create a new app** si cette option n’est pas déjà sélectionnée.  Cliquez ensuite sur le bouton **Download** .

   Cette opération télécharge un projet qui contient une application cliente connectée à votre application mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.
3. Extrayez le projet que vous avez téléchargé, puis ouvrez-le dans Xamarin Studio ou Visual Studio.

   ![][9]

   ![][8]

## <a name="optional-run-the-ios-project"></a>(Facultatif) Exécuter le projet iOS
Cette section s’applique à l’exécution du projet iOS Xamarin pour les appareils iOS. Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils iOS.

#### <a name="in-xamarin-studio"></a>Dans Xamarin Studio
1. Cliquez avec le bouton droit sur le projet iOS, puis cliquez sur **Définir comme projet de démarrage**.
2. Dans le menu **Exécuter**, cliquez sur **Démarrer le débogage** pour créer le projet et démarrer l’application dans l’émulateur iPhone.

#### <a name="in-visual-studio"></a>Dans Visual Studio
1. Cliquez avec le bouton droit sur le projet iOS, puis cliquez sur **Définir comme projet de démarrage**.
2. Dans le menu **Générer**, cliquez sur **Gestionnaire de configuration**.
3. Dans la boîte de dialogue **Gestionnaire de configuration**, cochez les cases **Générer** et **Déployer** du projet iOS.
4. Appuyez sur la touche **F5** pour générer le projet et démarrer l’application dans l’émulateur iPhone.

   > [!NOTE]
   > Si vous avez des problèmes de génération, exécutez le gestionnaire de package NuGet et mettez à jour vers la dernière version des packages de support Xamarin. Il arrive que les projets de démarrage rapide soient ralentis par la mise à jour vers la dernière version.    
   >
   >

Dans l’application, tapez un texte explicite, tel que *Découvrir Xamarin*, puis cliquez sur le bouton **+**.

![][10]

Ceci envoie une demande POST vers le nouveau backend d'application mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans cette table sont renvoyés par le backend d’application mobile et les données sont affichées dans la liste.

> [!NOTE]
> Vous trouverez le code qui vous permet d’accéder à votre serveur principal d’applications mobiles dans le fichier C# TodoItemManager.cs du projet de bibliothèque de classes portables de votre solution.
>
>

## <a name="optional-run-the-android-project"></a>(Facultatif) Exécuter le projet Android
Cette section s’applique à l’exécution du projet Xamarin pour Android. Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils Android.

#### <a name="in-xamarin-studio"></a>Dans Xamarin Studio
1. Cliquez avec le bouton droit sur le projet Android, puis cliquez sur **Définir comme projet de démarrage**.
2. Dans le menu **Exécuter**, cliquez sur **Démarrer le débogage** pour générer le projet et démarrer l’application dans l’émulateur Android.

#### <a name="in-visual-studio"></a>Dans Visual Studio
1. Cliquez avec le bouton droit sur le projet Android (Droid), puis cliquez sur **Définir comme projet de démarrage**.
2. Dans le menu **Générer**, cliquez sur **Gestionnaire de configuration**.
3. Dans la boîte de dialogue **Gestionnaire de configuration**, cochez les cases **Générer** et **Déployer** du projet Android.
4. Appuyez sur la touche **F5** pour générer le projet et démarrer l’application dans l’émulateur Android.

   > [!NOTE]
   > Si vous avez des problèmes de génération, exécutez le gestionnaire de package NuGet et mettez à jour vers la dernière version des packages de support Xamarin. Il arrive que les projets de démarrage rapide soient ralentis par la mise à jour vers la dernière version.    
   >
   >

Dans l’application, tapez un texte explicite, tel que *Découvrir Xamarin*, puis cliquez sur le bouton **+**.

![][11]

Ceci envoie une demande POST vers le nouveau backend d'application mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans cette table sont renvoyés par le backend d’application mobile et les données sont affichées dans la liste.

> [!NOTE]
> Vous trouverez le code qui vous permet d’accéder à votre serveur principal d’applications mobiles dans le fichier C# TodoItemManager.cs du projet de bibliothèque de classes portables de votre solution.
>
>

## <a name="optional-run-the-windows-project"></a>(Facultatif) Exécuter le projet Windows
Cette section s’applique à l’exécution du projet WinApp Xamarin pour les appareils Windows. Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils Windows.

#### <a name="in-visual-studio"></a>Dans Visual Studio
1. Cliquez avec le bouton droit sur les projets Windows, puis cliquez sur **Définir comme projet de démarrage**.
2. Dans le menu **Générer**, cliquez sur **Gestionnaire de configuration**.
3. Dans la boîte de dialogue **Gestionnaire de configuration**, cochez les cases **Générer** et **Déployer** du projet Windows que vous avez choisi.
4. Appuyez sur la touche **F5** pour générer le projet et démarrer l’application dans un émulateur Windows.

   > [!NOTE]
   > Si vous avez des problèmes de génération, exécutez le gestionnaire de package NuGet et mettez à jour vers la dernière version des packages de support Xamarin. Il arrive que les projets de démarrage rapide soient ralentis par la mise à jour vers la dernière version.    
   >
   >

Dans l’application, tapez un texte explicite, tel que *Découvrir Xamarin*, puis cliquez sur le bouton **+**.

Ceci envoie une demande POST vers le nouveau backend d'application mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans cette table sont renvoyés par le backend d’application mobile et les données sont affichées dans la liste.

![][12]

> [!NOTE]
> Vous trouverez le code qui vous permet d’accéder à votre serveur principal d’applications mobiles dans le fichier C# TodoItemManager.cs du projet de bibliothèque de classes portables de votre solution.
>
>

## <a name="next-steps"></a>Étapes suivantes
* [Ajout de l’authentification à votre application](app-service-mobile-xamarin-forms-get-started-users.md)  
  Découvrez comment authentifier les utilisateurs de votre application avec un fournisseur d’identité.
* [Ajouter des notifications Push à votre application](app-service-mobile-xamarin-forms-get-started-push.md)  
  Apprenez à ajouter la prise en charge des notifications Push à votre application et à configurer le serveur principal d’applications mobiles pour utiliser Azure Notification Hubs afin d’envoyer des notifications Push.
* [Activer la synchronisation hors connexion pour votre application](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Apprenez à ajouter une prise en charge hors connexion à votre application à l’aide d’un backend Mobile App. La synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile &mdash;pour afficher, ajouter ou modifier des données&mdash;, même lorsqu’il n’existe aucune connexion réseau.
* [Utilisation du client géré pour Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md)  
  Découvrez comment utiliser le Kit de développement logiciel (SDK) dans votre application Xamarin.

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[portail Azure]: https://portal.azure.com/

