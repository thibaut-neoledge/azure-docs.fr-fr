---
title: "Résoudre les problèmes liés à Azure RemoteApp : échecs de lancement et de connexion d’applications | Microsoft Docs"
description: "Apprenez à résoudre les problèmes liés au démarrage et à la connexion d’applications dans Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: e5cf7171-d1c2-4053-a38b-5af7821305e1
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 6bc1325809f39a45d387914e1fdc4d10f46df539
ms.lasthandoff: 03/31/2017


---
# <a name="troubleshoot-azure-remoteapp---application-launch-and-connection-failures"></a>Résoudre les problèmes liés à Azure RemoteApp : échecs de lancement et de connexion d’applications
> [!IMPORTANT]
> Azure RemoteApp ne sera plus disponible à partir du 31 août 2017. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Le lancement des applications hébergées dans Azure RemoteApp peut échouer pour différentes raisons. Cet article décrit ces diverses raisons et les messages d’erreur que les utilisateurs peuvent recevoir quand ils essaient de lancer des applications. Il traite également des échecs de connexion. (Mais cet article ne décrit pas les problèmes de connexion au client Azure RemoteApp.)  

Lisez la suite pour plus d’informations sur les messages d’erreur courants dus à des échecs de lancement et de connexion d’applications.

## <a name="were-getting-you-set-up-try-again-in-10-minutes"></a>Nous vous configurons... Réessayez dans 10 minutes.
Cette erreur signifie qu’Azure RemoteApp est monté en puissance pour répondre aux besoins en capacité de vos utilisateurs. En arrière-plan, d’autres instances de machines virtuelles Azure RemoteApp sont créées pour gérer les besoins en capacité de vos utilisateurs. En général, cette opération prend entre cinq et 10 minutes. Parfois, elle n’est pas assez rapide alors que vous avez besoin des ressources tout de suite. Exemple : un scénario dans lequel de nombreux utilisateurs ont besoin d’utiliser votre application dans Azure RemoteApp en même temps à 9h00. Si cette situation se produit, nous pouvons activer le **mode capacité** sur le serveur principal. Pour cela, ouvrez un ticket de support Azure. Veillez à inclure votre ID d’abonnement dans la demande.  

![Nous vous configurons](./media/remoteapp-apptrouble/ra-apptrouble1.png)

## <a name="could-not-auto-reconnect-to-your-applications-please-re-launch-your-application"></a>Impossible de vous reconnecter automatiquement, relancez vos applications
Ce message d’erreur apparaît souvent si vous utilisez Azure RemoteApp, puis mettez votre ordinateur en veille pendant plus de 4 heures. Quand vous sortez votre ordinateur de veille, le client Azure RemoteApp tente de se reconnecter automatiquement alors que le délai d’expiration a été dépassé.  Demandez aux utilisateurs de revenir à l’application et d’essayer de l’ouvrir à partir du client Azure RemoteApp.

![Impossible de vous reconnecter automatiquement](./media/remoteapp-apptrouble/ra-apptrouble2.png) 

## <a name="problems-with-the-temp-profile"></a>Problèmes avec le profil temporaire
Cette erreur se produit quand le montage de votre profil utilisateur (disque de profil utilisateur) a échoué et que l’utilisateur a reçu un profil temporaire.  Les administrateurs doivent accéder à la collection dans le portail Azure, puis à l’onglet **Sessions** pour essayer de **déconnecter** l’utilisateur. Cette intervention force la session utilisateur à se fermer totalement. L’utilisateur est ensuite invité à essayer de relancer une application. En cas d’échec, contactez le support Azure.

## <a name="azure-remoteapp-has-stopped-working"></a>Azure RemoteApp a cessé de fonctionner
Ce message d’erreur signifie que le client Azure RemoteApp rencontre un problème et doit être redémarré. Demandez aux utilisateurs de fermer le programme : sélectionnez **Fermer le programme** , puis relancez le client Azure RemoteApp.  Si le problème persiste, ouvrez un ticket de support Azure.

![Azure RemoteApp a cessé de fonctionner](./media/remoteapp-apptrouble/ra-apptrouble3.png)  

## <a name="an-error-occurred-while-remote-desktop-connection-was-accessing-this-resource-retry-the-connection-or-contact-your-system-administrator"></a>Une erreur s’est produite pendant que la connexion Bureau à distance accédait à la ressource. Essayez de vous reconnecter ou contactez votre administrateur système.
Il s’agit d’un message d’erreur générique : contactez le support Azure pour que nous puissions examiner le problème. 

![Message Azure RemoteApp générique](./media/remoteapp-apptrouble/ra-apptrouble4.png) 


