---
title: "Envoyer des notifications planifiées | Microsoft Docs"
description: "Cette rubrique décrit l'utilisation de notifications planifiées avec Azure Notification Hubs."
services: notification-hubs
documentationcenter: .net
keywords: notifications push,notification push,planification de notifications push
author: ysxu
manager: erikre
editor: 
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: efac6e1ecc00359f1622d380333140bc055c83e0
ms.contentlocale: fr-fr
ms.lasthandoff: 11/17/2016

---
# <a name="how-to-send-scheduled-notifications"></a>Procédure : envoi de notifications planifiées
## <a name="overview"></a>Vue d’ensemble
Si vous avez un scénario dans lequel vous souhaitez envoyer une notification à un moment donné dans le futur, mais que vous ne disposez pas d'un moyen simple d'activer votre code principal pour envoyer la notification. Notification Hubs de niveau Standard prend en charge une fonctionnalité qui vous permet de planifier des notifications jusqu'à 7 jours à l'avance.

Lorsque vous envoyez une notification, utilisez simplement la classe [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) dans le Kit de développement logiciel (SDK) Notification Hubs, comme indiqué dans l'exemple suivant :

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

Vous pouvez également annuler une notification précédemment planifiée à l'aide de son ID de notification :

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

Il n'existe aucune limite quant au nombre de notifications planifiées que vous pouvez envoyer.


