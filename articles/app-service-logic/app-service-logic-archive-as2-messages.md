<properties 
   pageTitle="Archivage des messages du connecteur AS2 | Microsoft Azure App Service" 
   description="Comment archiver ou stocker les messages du connecteur AS2 dans Azure App Service" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>


# Vue d'ensemble de l'archivage des messages du connecteur AS2


[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Le [connecteur AS2](app-service-logic-connector-as2.md) montre la capacité à archiver des messages. L’archivage stocke le message dans le **conteneur d’objets blob Azure** qui fait partie des paramètres du package.

L'archivage est exposé à deux moments pour les messages et les accusés de réception (MDN) :

1. **Déclencheur de réception/décodage** : le message est archivé dès qu’il est reçu par l’instance de l’application API. 
2. **Action d’encodage/envoi** : le message encodé est archivé, une fois tout le traitement terminé et juste avant d’être envoyé au partenaire. 

## Récupération de l’URL archivée du message

Accédez à l’instance de l’application API du connecteur AS2 et cliquez sur Suivi. Limitez les informations de suivi à l'aide de paramètres de filtre. Une fois votre message visible, cliquez dessus pour en afficher la vue détaillée. L'URL archivée du message s'affiche dans cette vue détaillée : ![][1]

## Récupération du message archivé

Utilisez l'URL récupérée ci-dessus pour récupérer le message archivé dans le stockage d'objets blob Azure.


<!--Image references-->
[1]: ./media/app-service-logic-archive-as2-messages/Tracking.jpg
 

<!---HONumber=AcomDC_0420_2016-->