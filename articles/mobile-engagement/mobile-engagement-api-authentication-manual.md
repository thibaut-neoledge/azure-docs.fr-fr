---
title: "Azure Mobile Engagement - Configuration manuelle de l’utilisation des API pour l’authentification"
description: "Décrit comment configurer manuellement l’authentification pour les API REST Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.translationtype: Human Translation
ms.sourcegitcommit: c6190a5a5aba325b15aef97610c804f5441ef7ad
ms.openlocfilehash: 9d6132e1a01be489b8e8e28a0219cf8a0b50b318
ms.contentlocale: fr-fr
ms.lasthandoff: 12/22/2016

---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Azure Mobile Engagement - Configuration manuelle de l’utilisation des API pour l’authentification
Ce document est une annexe de l’article [Azure Mobile Engagement - Utilisation des API pour l’authentification](mobile-engagement-api-authentication.md). Veillez à le lire en premier pour comprendre le contexte. Cette annexe décrit une autre méthode d’installation unique afin de configurer l’authentification pour les API REST Mobile Engagement à l’aide du portail Azure. 

> [!NOTE]
> Les instructions ci-dessous sont fondées sur ce [guide Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md) et personnalisées en fonction des besoins propres à l’authentification pour les API Mobile Engagement. Par conséquent, consultez ce guide pour bien comprendre les étapes décrites en détail ci-dessous. 
> 
> 

1. Connectez-vous à votre compte Azure via le [portail classique](https://manage.windowsazure.com/).
2. Sélectionnez **Active Directory** dans le volet gauche.
   
     ![sélectionner Active Directory][1]
3. Choisissez le **répertoire actif par défaut** dans le portail Azure. 
   
     ![choisir l’annuaire][2]
   
   > [!IMPORTANT]
   > Cette approche fonctionne uniquement quand vous travaillez dans le répertoire actif par défaut de votre compte et pas si vous l’utilisez dans un répertoire actif que vous avez créé pour votre compte. 
   > 
   > 
4. Pour afficher les applications dans votre annuaire, cliquez sur **Applications**.
   
     ![afficher les applications][3]
5. Cliquez sur **AJOUTER**. 
   
     ![ajouter l’application][4]
6. Cliquez sur **Ajouter une application développée par mon organisation**
   
     ![nouvelle application][5]
7. Renseignez le nom de l’application et sélectionnez le type d’application **APPLICATION WEB ET/OU API WEB** , puis cliquez sur le bouton Suivant.
   
     ![nommer l’application][6]
8. Vous pouvez fournir des URL factices pour **l’URL DE CONNEXION** et **l’URI ID D’APPLICATION**. Ces URL ne sont pas utilisées pour notre scénario et les URL elles-mêmes ne sont pas validées.  
   
     ![propriétés de l’application][7]
9. À la fin de cette opération, vous disposerez d’une application AAD portant le nom que vous avez indiqué précédemment, comme suit. Il s’agit de votre **AD\_APP\_NAME**. Prenez-en note.  
   
     ![nom de l’application][8]
10. Cliquez sur le nom de l’application, puis sur **Configurer**.
    
      ![configurer l’application][9]
11. Prenez note de l’ID CLIENT qui sera utilisé comme **CLIENT\_ID** pour les appels d’API. 
    
     ![configurer l’application][10]
12. Faites défiler l’écran jusqu’à la section **Clés** et ajoutez une clé avec de préférence un délai d’expiration de deux ans, puis cliquez sur **Enregistrer**. 
    
     ![configurer l’application][11]
13. Copiez immédiatement la valeur affichée pour la clé, car elle est affichée uniquement maintenant et n’est pas stockée, ce qui signifie qu’elle ne s’affichera plus ultérieurement. Si vous la perdez, vous devrez générer une nouvelle clé. Il s’agit de la **CLIENT_SECRET** pour les appels d’API. 
    
     ![configurer l’application][12]
    
    > [!IMPORTANT]
    > Cette clé expire à la fin de la durée que vous avez spécifiée. Vous devrez donc veiller à la renouveler le moment venu, sans quoi l’authentification de votre API ne fonctionnera plus. Vous pouvez également supprimer et recréer cette clé si vous pensez qu’elle a été compromise.
    > 
    > 
14. Cliquez maintenant sur le bouton **VOIR LES POINTS DE TERMINAISON** pour ouvrir la boîte de dialogue **Ajouter des points de terminaison**. 
    
    ![][13]
15. Dans la boîte de dialogue Points de terminaison d’app, copiez le **POINT DE TERMINAISON DE JETON OAUTH 2.0**. 
    
    ![][14]
16. Ce point de terminaison se présente comme indiqué ci-dessous, le GUID de l’URL étant votre **TENANT_ID**. Prenez-en note : 
    
        https://login.microsoftonline.com/<GUID>/oauth2/token
17. Nous allons maintenant configurer les autorisations pour cette application. Pour cela, vous devez ouvrir le [portail Azure](https://portal.azure.com). 
18. Cliquez sur **Groupes de ressources** et recherchez le groupe de ressources **Mobile Engagement**.  
    
    ![][15]
19. Cliquez sur le groupe de ressources **Mobile Engagement** et accédez au panneau **Paramètres** ici. 
    
    ![][16]
20. Cliquez sur **Utilisateurs** dans le panneau Paramètres, puis cliquez sur **Ajouter** pour ajouter un utilisateur. 
    
    ![][17]
21. Cliquez sur **Sélectionner un rôle**
    
    ![][18]
22. Cliquez sur **Propriétaire**
    
    ![][19]
23. Recherchez le nom de votre application **AD\_APP\_NAME** dans la zone de recherche. Il n’est pas affiché par défaut ici. Une fois que vous l’avez trouvé, sélectionnez-le, puis cliquez sur **Sélectionner** en bas du panneau. 
    
    ![][20]
24. Dans le panneau **Ajouter un accès**, il s’affiche sous la forme **1 utilisateur, 0 groupe**. Cliquez sur **OK** dans ce panneau pour confirmer la modification. 
    
    ![][21]

Vous avez maintenant terminé la configuration AAD requise et êtes prêt à appeler les API. 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client_secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png




