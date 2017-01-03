---
title: "Utiliser des applications prenant en charge les revendications dans le proxy d’application"
description: "Explique comment devenir opérationnel avec le proxy d’application Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 91e6211b-fe6a-42c6-bdb3-1fff0312db15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 164f9fada5565110d37c0f191bcdfac9ec8e48e8


---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Utiliser des applications prenant en charge les revendications dans le proxy d’application
Les applications prenant en charge les revendications effectuent une redirection vers le service d’émission de jeton de sécurité (STS), qui à son tour demande les informations d’identification de l’utilisateur en échange d’un jeton avant de rediriger l’utilisateur vers l’application. Pour permettre au proxy d’application de travailler avec ces redirections, les étapes suivantes sont nécessaires.

## <a name="prerequisites"></a>Composants requis
Avant d’effectuer cette procédure, vérifiez que le service STS vers lequel l’application prenant en charge les revendications effectue la redirection est disponible en dehors de votre réseau local.

## <a name="azure-classic-portal-configuration"></a>Configuration du portail Azure Classic
1. Publiez votre application en suivant les instructions décrites dans [Publier des applications avec le proxy d’application](active-directory-application-proxy-publish.md).
2. Dans la liste des applications, sélectionnez l’application prenant en charge les revendications, puis cliquez sur **Configurer**.
3. Si vous avez choisi **Direct** comme **méthode de préauthentification**, assurez-vous de sélectionner **HTTPS** comme schéma **d’URL externe**.
4. Si vous choisissez **Azure Active Directory** comme **méthode de préauthentification**, sélectionnez **Aucune** comme **méthode d’authentification interne**.

## <a name="adfs-configuration"></a>Configuration d’AD FS
1. Ouvrez Gestion AD FS.
2. Accédez à **Approbations de la partie de confiance**, cliquez avec le bouton droit sur l’application que vous publiez avec le proxy d’application, puis choisissez **Propriétés**.  
   ![Approbations de partie de confiance, clic droit sur le nom de l’application – capture d’écran](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  
3. Sous l’onglet **Points de terminaison**, sous **Type de point de terminaison**, sélectionnez **WS-Federation**.
4. Sous **URL approuvée**, entrez l’URL que vous avez entrée dans le proxy d’application sous **URL externe**, puis cliquez sur **OK**.  
   ![Ajouter un point de terminaison, définition de la valeur de l’URL approuvée – capture d’écran](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## <a name="see-also"></a>Voir aussi
* [Publiez des applications avec le proxy d’application](active-directory-application-proxy-publish.md)
* [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
* [Résoudre les problèmes rencontrés avec le proxy d’application](active-directory-application-proxy-troubleshoot.md)
* [Activation d’applications clientes natives de manière à ce qu’elles interagissent avec des applications proxy](active-directory-application-proxy-native-client.md)

Pour les dernières nouvelles et mises à jour, visitez [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/)




<!--HONumber=Dec16_HO4-->


