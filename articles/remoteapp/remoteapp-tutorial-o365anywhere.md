---
title: "Profitez de la même expérience d’Office 365 sur n’importe quel appareil avec Azure RemoteApp | Microsoft Docs"
description: "Découvrez comment partager n’importe quelle application Office 365 avec vos utilisateurs grâce à Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: guscatalano
manager: mbaldwin
editor: 
ms.assetid: 0c971ce9-7d45-4cfb-9737-15b6706047e8
ms.service: remoteapp
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: compute
ms.date: 11/23/2016
ms.author: guscatal;elizapo
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 584c781c97097cda3c1455ade05cba8659f11073
ms.lasthandoff: 03/31/2017


---
# <a name="get-the-same-office-365-experience-on-any-device-with-azure-remoteapp"></a>Bénéficiez de la même expérience Office 365 sur n’importe quel appareil avec Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp ne sera plus disponible à partir du 31 août 2017. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Cet article explique comment déployer Office 365 sur n’importe quel appareil au sein de votre société. Vos utilisateurs peuvent bénéficier des mêmes fonctionnalités et de la même interface utilisateur sur Android, Apple et Windows.

Pour ce faire, nous allons utiliser Azure RemoteApp en hébergeant Office 365 sur des machines virtuelles évolutives dans Azure, auxquelles les utilisateurs peuvent se connecter. Nous appelons cet ensemble de machines virtuelles une « collection cloud ».

## <a name="create-a-cloud-collection"></a>Création d'une collection cloud
Après avoir créé un compte Azure, accédez à **RemoteApp** en cliquant sur le lien situé à gauche.
![Affichage d’Azure RemoteApp sur le portail Azure](./media/remoteapp-tutorial-o365anywhere/1-menu.png)

Continuez en cliquant sur **Nouveau** en bas de la fenêtre, puis sur Création rapide, afin de créer une collection. Indiquez un nom, la région, l’abonnement, le plan et l’image « Office Professionnel 2013 » proposée.
![Boîte de dialogue Créer](./media/remoteapp-tutorial-o365anywhere/2-quickcreate.png)

Une fois le formulaire rempli, le processus de création de formulaire devrait démarrer. Cette procédure peut prendre environ une heure.

![En attente](./media/remoteapp-tutorial-o365anywhere/3-waiting.png)

Une fois le processus terminé, vous devriez obtenir le résultat suivant. Si vous cliquez sur **Publication** , vous voyez que la plupart des applications Office ont déjà été publiées.
![La collection a été créée](./media/remoteapp-tutorial-o365anywhere/4-done.png)

![Applications publiées](./media/remoteapp-tutorial-o365anywhere/5-publish.png)

À ce stade, vous pouvez également ajouter des utilisateurs ayant accès à cette collection en cliquant sur **Accès utilisateur**.
![Configuration de l'accès utilisateur](./media/remoteapp-tutorial-o365anywhere/6-user.png)

À présent, nous allons essayer de nous connecter à Office 365 !

## <a name="connect-to-office-365"></a>Connexion à Office 365
Rendez-vous sur [https://www.remoteapp.windowsazure.com/](https://www.remoteapp.windowsazure.com/), faites défiler la page vers le bas et cliquez sur **Télécharger les clients** pour installer le client Azure RemoteApp sur votre appareil. Les captures d’écran ci-dessous correspondent à l’environnement Windows.

Lorsque l’application démarre, le système vous demande de vous connecter à l’aide de votre compte Microsoft (anciennement dénommé « Windows Live ID »). Utilisez celui de votre compte Azure pour le moment. Une fois connecté, vous devriez voir une notification vous indiquant de nouvelles invitations. Cliquez dessus pour afficher une liste comme celle ci-dessous. Acceptez l’invitation correspondant à l’adresse e-mail de votre compte Azure.

![Nouvelle invitation](./media/remoteapp-tutorial-o365anywhere/7-araclient.png)

Ce que vous voyez lorsque de nouvelles invitations sont disponibles.

![Accepter une application](./media/remoteapp-tutorial-o365anywhere/8-invitation.png)

Une fois l’invitation acceptée, vous devriez voir toutes les applications Office dans le client Azure RemoteApp.

![Liste des applications](./media/remoteapp-tutorial-o365anywhere/9-work.png)

Lorsque vous cliquez sur l’une de ces applications, celle-ci démarre sur la machine virtuelle Azure, entièrement configurée ! Vous n’avez plus qu’à l’utiliser !

![démarrage en cours](./media/remoteapp-tutorial-o365anywhere/10-arastart.png)

![PowerPoint](./media/remoteapp-tutorial-o365anywhere/11-pp.png)


