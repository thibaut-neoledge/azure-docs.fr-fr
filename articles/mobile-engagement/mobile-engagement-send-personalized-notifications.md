---
title: "Envoyer une notification personnalisée avec Azure Mobile Engagement"
description: "Comment envoyer des notifications personnalisées en incluant des informations de profil utilisateur dans les notifications (par exemple des noms)"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: dwrede
editor: 
ms.assetid: 51007089-156a-4bac-bb1b-a590633bf2a2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: all
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b6debaa3a27cfc2a24d23a9c586fce9962bd9605


---
# <a name="personalize-notifications-by-including-user-name"></a>Personnaliser les notifications en incluant le nom d’utilisateur
Dans votre quête visant à rendre les notifications plus attrayantes aux yeux des utilisateurs de votre application, vous pouvez envisager de les personnaliser. Une approche efficace consiste à utiliser les noms des utilisateurs de l’application de manière sélective afin de rendre les notifications plus personnelles. Avertissement ici : l’ajout des noms des utilisateurs aux notifications doit être utilisé avec parcimonie. Si vous abusez de cette stratégie, elle risque d’effrayer certains utilisateurs de l’application. Vous devez également vous assurer de laisser à l’utilisateur le choix de vous fournir ces informations personnelles avec un consentement total dans l’application mobile avant de commencer à utiliser cette option. 

Techniquement, avec Azure Mobile Engagement, vous pouvez personnaliser la notification en suivant la procédure ci-dessous, dans laquelle nous allons utiliser le scénario d’inclusion du nom de l’utilisateur dans les notifications. Vous allez utiliser le concept d’informations sur l’application ou de balises dont les valeurs peuvent être transmises par les Kits de développement logiciel (SDK) intégrés dans l’application mobile ou via les API. Ces informations sur l’application ou balises peuvent ensuite être utilisées dans les cas suivants :

1. Pour cibler des notifications à des utilisateurs spécifiques en fonction des valeurs informations sur l’application. 
2. En tant qu’espaces réservés dans les notifications, qui seront remplacés par des valeurs spécifiques à l’appareil ou à l’utilisateur lors de l’envoi de notifications à cet appareil. 

> [!IMPORTANT]
> Notez que l’envoi des notifications sera ralenti en raison de ce traitement supplémentaire consistant à remplacer les valeurs d’informations sur l’application pour chaque notification. 
> 
> 

## <a name="register-app-info-in-the-mobile-engagement-portal"></a>Enregistrer des informations sur l’application dans le portail Mobile Engagement
1) Commencez par enregistrer des informations sur l’application ou des balises dans le Portail Azure. Pour ce faire, accédez à **Paramètres** -> **Balise (informations sur l’application)**.  

![][1]    

2) Cliquez sur **Nouvelle balise (informations sur l’application)** et indiquez le nom au format *nom_utilisateur* et le type sous forme de *chaîne*, puis cliquez sur **Soumettre**. 

![][2]

3) Enfin, vous verrez ces informations sur l’application enregistrées comme suit :

![][3]

## <a name="send-app-info-from-the-client-sdk"></a>Envoyer des informations sur l’application à partir du Kit de développement logiciel (SDK) client
Ici, nous utilisons l’exemple d’application universelle Windows, mais des méthodes équivalentes existent également pour nos autres Kits de développement logiciel (SDK). 

Supposons que vous avez une méthode dans l’application mobile vous permettant d’obtenir où vous obtenez les informations de profil de l’utilisateur, par exemple son nom, probablement après son authentification. Ici, vous appellerez la méthode `SendAppInfo` et renseignerez la valeur des informations sur l’application `user_name` que vous avez enregistrées précédemment dans le serveur principal du service Mobile Engagement. 

    Dictionary<object, object> appInfo = new Dictionary<object, object>();
    appInfo.Add("user_name", str);
    EngagementAgent.Instance.SendAppInfo(appInfo); 

## <a name="send-personalized-notifications"></a>Envoi de notifications personnalisées
Vous êtes maintenant prêt à envoyer des notifications à l’aide de ce **nom_utilisateur**. 

1) Accédez au portail Mobile Engagement dans l’onglet **Reach** pour créer une notification. Vous pouvez utiliser cet espace réservé au format suivant n’importe où dans le titre ou le corps de la notification. 

![][4]    

> [!NOTE]
> Les utilisateurs pour lesquels les informations sur l’application nom_utilisateur ne sont pas définies ne recevront pas de notification. Si vous exécutez la campagne de notification en mode test et si les informations d’application ne sont pas définies, nous allons envoyer le caractère « ? » pour remplacer l’espace réservé. 
> 
> 

2) Lorsque Mobile Engagement sélectionnera un appareil pour envoyer cette notification, il consultera ces informations sur l’application et remplacera la valeur de l’espace réservé.  
Par exemple, si nous avons défini `str = "Scott"` pour un utilisateur, l’enregistrement de l’appareil sera associé aux informations sur l’application de **nom_utilisateur = SCOTT** pour cet utilisateur. En outre, cet utilisateur verra une notification push en dehors de l’application au format suivant. 

![][5]    

<!-- Images. -->
[1]: ./media/mobile-engagement-send-personalized-notifications/app-info.png
[2]: ./media/mobile-engagement-send-personalized-notifications/create-app-info.png
[3]: ./media/mobile-engagement-send-personalized-notifications/app-info-user-name.png
[4]: ./media/mobile-engagement-send-personalized-notifications/personal-notification.png
[5]: ./media/mobile-engagement-send-personalized-notifications/notification.png




<!--HONumber=Nov16_HO3-->


