---
title: "Accès à vos applications à partir de n’importe quel appareil | Microsoft Docs"
description: "Découvrez quels clients sont pris en charge pour Azure RemoteApp et comment accéder à vos applications."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: fb7bd17d-7aa8-43fd-9278-f96e0e9308e4
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: c07773be6e4a2274287920de9420f4c8b96f58e1
ms.lasthandoff: 03/31/2017


---
# <a name="accessing-your-apps-in-azure-remoteapp"></a>Accès à vos applications dans Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp ne sera plus disponible à partir du 31 août 2017. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

L'un des intérêts de RemoteApp repose sur le fait que vous pouvez accéder à des applications à partir de l'un de vos appareils. Mieux encore, vous pouvez commencer à travailler sur un appareil, puis passer sur un autre et reprendre exactement là où vous vous étiez arrêté. Pour commencer, vous devez télécharger le client adapté à votre appareil et vous connecter au service.

Dans cette rubrique, nous allons passer en revue les clients actuellement pris en charge et les modes de téléchargement, avant de découvrir comment vous connecter à RemoteApp à partir de chacun de ces clients.

## <a name="supported-clients"></a>Clients pris en charge
Vous pouvez accéder à RemoteApp en suivant la procédure mentionnée ci-dessous si votre appareil exécute l'un des systèmes d'exploitation suivants :

* Windows 10 
* Windows 8.1
* Windows 8
* Windows 7 Service Pack 1
* Windows Phone 8.1
* iOS
* Mac OS X
* Android

 Qu'en est-il des clients légers ? Les clients légers Windows Embedded suivants sont pris en charge :

* Windows Embedded Standard 7
* Windows Embedded 8 Standard
* Windows Embedded 8.1 Industry Pro
* Windows 10 IoT Entreprise

## <a name="downloading-the-client"></a>Téléchargement du client
Quelle que soit la plateforme utilisée, le client dont vous avez besoin pour accéder à RemoteApp est disponible dans la page de [Téléchargement du client Bureau à distance](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) .

Si vous cliquez sur les différents liens, vous commencez directement le téléchargement du client ou vous accédez à la page de téléchargement du client dans le magasin d'applications de cette plateforme. Installez le client en suivant les instructions qui s'affichent à l'écran.

Après avoir installé le client sur votre appareil et l'avoir lancé, passez à la section appropriée ci-dessous pour savoir comment vous connecter à RemoteApp à partir de ce client.

## <a name="android"></a>Android
Une fois l'application Bureau à distance Microsoft installée à partir du Google Play Store, elle apparaît dans votre liste d'applications sous **Bureau à distance**.

1. Lorsque vous lancez l'application, vous accédez à un centre de connexion vide, sauf si vous avez déjà utilisé l'application. Pour prendre en main Azure RemoteApp, cliquez sur le bouton d'ajout **""+""**, puis sur **Azure RemoteApp**.    
   
     ![Centre de connexion vide](./media/remoteapp-clients/Android1.png)
2. Vous devez vous connecter avec votre adresse de messagerie pour accéder au service. Appuyez sur **Prise en main**.
   
    ![Invite de connexion](./media/remoteapp-clients/Android2.png)
3. Dans la page suivante, tapez votre **adresse de messagerie**, puis cliquez sur **Continuer**. Le processus de connexion à l'aide d'Azure Active Directory commence alors.
   
    ![Première page Azure Active Directory](./media/remoteapp-clients/Android3.png)
4. Suivez les instructions qui s'affichent à l'écran pour vous connecter avec votre compte Microsoft (précédemment appelé « LiveID ») ou votre ID d'organisation. Une fois que vous êtes connecté, une page répertoriant toutes les invitations que vous avez reçues peut s'afficher. Le cas échéant, sélectionnez les invitations que vous jugez fiables, puis cliquez sur **Terminé**.    
   
    ![Page des invitations](./media/remoteapp-clients/Android4.png)
5. Une fois vos invitations acceptées, la liste des applications auxquelles vous avez accès est téléchargée sur votre appareil et devient disponible dans le centre de connexion. Cliquez sur l'une des applications pour la lancer et commencer à l'utiliser.
   
    ![Centre de connexion avec un flux](./media/remoteapp-clients/Android5.png)
6. Si vous n'avez pas encore d'invitation, vous pouvez tout de même essayer le service. Pour ce faire, cliquez sur **Accéder à un essai gratuit** lorsque vous y êtes invité.
   
    ![Invite de démonstration de flux](./media/remoteapp-clients/Android6.png)
7. Cela vous permet d'accéder à un ensemble d'applications de base qui vous permettent de commencer à utiliser RemoteApp.
   
    ![Démonstration de flux pour Azure RemoteApp](./media/remoteapp-clients/Android7.png)

## <a name="ios"></a>iOS
Une fois l'application Bureau à distance Microsoft installée à partir du magasin d'applications, elle apparaît dans votre liste d'applications sous **Client Bureau à distance**.

1. Lorsque vous lancez l'application, vous accédez à un centre de connexion vide, sauf si vous avez déjà utilisé l'application. Pour prendre en main Azure RemoteApp, cliquez sur le bouton **""+""**, puis sur **Azure RemoteApp**.
   
    ![Centre de connexion vide](./media/remoteapp-clients/IOS1.png)
2. Vous devez vous connecter avec votre adresse de messagerie pour accéder au service. Pour démarrer ce processus, tapez votre **adresse de messagerie**, puis cliquez sur **Continuer**.
   
    ![Invite de connexion](./media/remoteapp-clients/picture1.png)
3. Suivez les instructions qui s'affichent à l'écran pour vous connecter avec votre compte Microsoft (LiveID) ou votre ID d'organisation. Une fois que vous êtes connecté, une page répertoriant toutes les invitations que vous avez reçues peut s'afficher. Le cas échéant, sélectionnez les invitations que vous jugez fiables, puis cliquez sur **Terminé**.
   
    ![Page des invitations](./media/remoteapp-clients/IOS3.png)
4. Une fois vos invitations acceptées, la liste des applications auxquelles vous avez accès est téléchargée sur votre appareil et devient disponible dans le centre de connexion. Cliquez sur l'une des applications pour la lancer et commencer à l'utiliser.
   
    ![Centre de connexion avec un flux](./media/remoteapp-clients/IOS4.png)
5. Si vous n'avez pas encore d'invitation, vous pouvez tout de même essayer le service. Pour ce faire, cliquez sur **Accéder à un essai gratuit** lorsque vous y êtes invité.
   
    ![Invite de démonstration de flux](./media/remoteapp-clients/IOS5.png)
6. Cela vous permet d'accéder à un ensemble d'applications de base qui vous permettent de commencer à utiliser RemoteApp.
   
    ![Démonstration de flux pour Azure RemoteApp](./media/remoteapp-clients/IOS6.png)

## <a name="mac-os-x"></a>Mac OS X
Une fois l'application Bureau à distance Microsoft installée à partir du magasin d'applications, elle apparaît dans votre liste d'applications sous **Bureau à distance Microsoft**.

1. Lorsque vous lancez l'application, vous accédez à un centre de connexion vide, sauf si vous avez déjà utilisé l'application. Pour commencer à utiliser Azure RemoteApp, cliquez sur le bouton **Azure RemoteApp** .
   
    ![Centre de connexion vide](./media/remoteapp-clients/Mac1.png)
2. Vous devez vous connecter avec votre adresse de messagerie pour accéder au service. Pour démarrer ce processus, cliquez sur **Prendre en main**.
   
    ![Invite de connexion](./media/remoteapp-clients/Mac2.png)
3. Dans la page suivante, tapez votre **adresse de messagerie**, puis cliquez sur **Continuer**. Le processus de connexion à l'aide d'Azure Active Directory commence alors.
   
    ![Première page Azure Active Directory](./media/remoteapp-clients/picture2.png)
4. Suivez les instructions qui s'affichent à l'écran pour vous connecter avec votre compte Microsoft (LiveID) ou votre ID d'organisation. Une fois que vous êtes connecté, une page répertoriant toutes les invitations que vous avez reçues peut s'afficher. Le cas échéant, sélectionnez les invitations que vous jugez fiables, puis fermez la boîte de dialogue.
   
    ![Page des invitations](./media/remoteapp-clients/Mac4.png)
5. Une fois vos invitations acceptées, la liste des applications auxquelles vous avez accès est téléchargée sur votre appareil et devient disponible dans le centre de connexion. Double-cliquez sur l'une des applications pour la lancer et commencer à l'utiliser.
   
    ![Centre de connexion avec un flux](./media/remoteapp-clients/Mac5.png)
6. Si vous n'avez pas encore d'invitation, vous pouvez tout de même essayer le service. Pour ce faire, cliquez sur **Accéder à un essai gratuit** lorsque vous y êtes invité.
   
    ![Invite de démonstration de flux](./media/remoteapp-clients/Mac6.png)
7. Cela vous permet d'accéder à un ensemble d'applications de base qui vous permettent de commencer à utiliser RemoteApp.
   
    ![Démonstration de flux pour Azure RemoteApp](./media/remoteapp-clients/Mac7.png)

## <a name="windows-all-supported-versions-except-windows-phone"></a>Windows (Toutes les versions prises en charge sauf Windows Phone)
Le client se lance automatiquement une fois son installation terminée. Cependant, pour y accéder à nouveau ultérieurement, vous le trouverez dans votre liste d'applications sous le nom **Azure RemoteApp**.

1. Une fois le client lancé, la première page qui s'affiche vous souhaite la bienvenue dans Azure RemoteApp. Pour continuer, cliquez sur **Prise en main**.
   
    ![Page d'accueil du client Azure RemoteApp](./media/remoteapp-clients/Windows1.png)
2. La page suivante commence le processus de connexion pour Azure RemoteApp à l'aide d'Azure Active Directory. Ce processus vous semblera familier si vous avez déjà utilisé des services Microsoft. Commencez par taper votre **adresse de messagerie**, puis cliquez sur **Continuer**.
   
    ![Invite de la première page Azure Active Directory](./media/remoteapp-clients/Windows2.png)
3. Suivez les instructions qui s'affichent à l'écran pour vous connecter avec votre compte Microsoft (LiveID) ou votre ID d'organisation. Une fois que vous êtes connecté, une page répertoriant toutes les invitations que vous avez reçues peut s'afficher. Le cas échéant, sélectionnez les invitations que vous jugez fiables, puis cliquez sur **Terminé**.
   
    ![Page des invitations du client Azure RemoteApp](./media/remoteapp-clients/Windows3.png)
4. Une fois vos invitations acceptées, la liste des applications auxquelles vous avez accès est téléchargée sur votre appareil et devient disponible dans le centre de connexion. Double-cliquez sur l'une des applications pour la lancer et commencer à l'utiliser.
   
    ![Centre de connexion du client Azure RemoteApp](./media/remoteapp-clients/Windows4.png)
5. Si personne ne vous a encore envoyé d'invitation, ne vous inquiétez pas : nous avons paré à cette éventualité ! Vous avez tout de même accès à une collection de démonstration afin de pouvoir tester le service.
   
    ![Démonstration de flux pour Azure RemoteApp](./media/remoteapp-clients/Windows5.png)

## <a name="windows-phone-81"></a>Windows Phone 8.1
Une fois l'application Bureau à distance Microsoft installée à partir du magasin Windows Phone 8.1, elle apparaît dans votre liste d'applications sous **Bureau à distance**.

1. Lorsque vous lancez l'application, vous accédez directement à un centre de connexion vide, sauf si vous avez déjà utilisé l'application. Pour prendre en main Azure RemoteApp, cliquez sur le bouton d'ajout **""+""** situé en bas de l'écran.
   
    ![Centre de connexion vide](./media/remoteapp-clients/WinPhone1.png)
2. Appuyez ensuite sur **Azure RemoteApp**.
   
    ![Page Ajouter des éléments](./media/remoteapp-clients/WinPhone2.png)
3. Vous devez vous connecter avec votre adresse de messagerie pour accéder au service. Pour démarrer ce processus, cliquez sur **Se connecter**.
   
    ![Invite de connexion](./media/remoteapp-clients/WinPhone3.png)
4. Dans la page suivante, tapez votre **adresse de messagerie**, puis cliquez sur **Continuer**. Le processus de connexion à l'aide d'Azure Active Directory commence alors.
   
    ![Première page Azure Active Directory](./media/remoteapp-clients/WinPhone4.png)
5. Suivez les instructions qui s'affichent à l'écran pour vous connecter avec votre compte Microsoft (LiveID) ou votre ID d'organisation. Une fois que vous êtes connecté, une page répertoriant toutes les invitations que vous avez reçues peut s'afficher. Le cas échéant, sélectionnez les invitations que vous jugez fiables, puis cliquez sur **Enregistrer**.
   
    ![Page des invitations](./media/remoteapp-clients/WinPhone5.png)
6. Une fois vos invitations acceptées, la liste des applications auxquelles vous avez accès est téléchargée sur votre appareil et devient disponible dans le centre de connexion. Cliquez sur l'une des applications pour la lancer et commencer à l'utiliser.
   
    ![Centre de connexion avec un flux](./media/remoteapp-clients/WinPhone6.png)
7. Si vous n'avez pas encore d'invitation, vous pouvez tout de même essayer le service. Pour ce faire, cliquez sur **Oui** lorsque vous y êtes invité.
   
    ![Invite de démonstration de flux](./media/remoteapp-clients/WinPhone7.png)
8. Cela vous permet d'accéder à un ensemble d'applications de base qui vous permettent de commencer à utiliser RemoteApp.
   
    ![Démonstration de flux pour Azure RemoteApp](./media/remoteapp-clients/WinPhone8.png)


