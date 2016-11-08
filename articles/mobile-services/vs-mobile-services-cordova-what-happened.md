---
title: Qu’est-il arrivé à mon projet Cordova (services connectés de Visual Studio) ? | Microsoft Docs
description: 'Qu''est-il arrivé à mon projet Azure Cordova après l''ajout d''Azure Mobile Services à l''aide des services connectés Visual Studio ? '
services: mobile-services
documentationcenter: na
author: mlhoop
manager: douge
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 07/21/2016
ms.author: mlearned

---
# Qu'est-il arrivé à mon projet Azure Cordova après l'ajout d'Azure Mobile Services à l'aide des services connectés de Visual Studio ?
## Références ajoutées
Le plug-in du client Azure Mobile Services (fourni avec toutes les applications hybrides multi-appareils) a été activé.

## Valeurs de la chaîne de connexion pour Mobile Services
Sous `services\mobileServices\settings`, un nouveau fichier JavaScript (.js) avec un objet **MobileServiceClient** a été généré. Il contient l’URL et la clé d’application du service mobile sélectionné. Ce fichier permet l'initialisation d'un objet client du service mobile, de la même manière que le code ci-dessous.

    var mobileServiceClient;
    document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
            "<your mobile service name>.azure-mobile.net",
            "<insert your key>"
        );

[En savoir plus sur Mobile Services](https://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=AcomDC_0727_2016-->