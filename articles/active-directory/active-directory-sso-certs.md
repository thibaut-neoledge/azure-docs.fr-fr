---
title: Gestion des certificats de fédération dans Azure AD | Microsoft Docs
description: Apprenez à personnaliser la date d’expiration pour vos certificats de fédération, mais aussi à renouveler les certificats arrivant à expiration.
services: active-directory
documentationcenter: ''
author: asmalser-msft
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2016
ms.author: asmalser-msft

---
# <a name="managing-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Gestion des certificats pour l’authentification unique fédérée sur Azure Active Directory
Cet article répond aux questions relatives aux certificats créés par Azure Active Directory pour établir une authentification unique fédérée vers vos applications SaaS.

Cet article concerne uniquement les applications configurées pour utiliser **Authentification unique Azure AD**, comme indiqué dans l’exemple suivant :

![Authentification unique Azure AD](./media/active-directory-sso-certs/fed-sso.PNG)

## <a name="how-to-customize-the-expiration-date-for-your-federation-certificate"></a>Personnalisation de la date d’expiration de votre certificat de fédération
Les certificats sont configurés par défaut pour expirer après deux ans. Vous pouvez choisir une autre date d’expiration pour votre certificat en procédant comme suit. Les captures d'écran présentées ici ont été prises pendant une utilisation de Salesforce, mais les étapes peuvent s'appliquer à n'importe quelle application SaaS fédérée.

1. Dans Azure Active Directory, sur la page Démarrage rapide de votre application, cliquez sur **Configurer l’authentification unique**.
   
    ![Ouvrez l’Assistant Configuration de l’authentification unique.](./media/active-directory-sso-certs/config-sso.png)
2. Sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
3. Entrez **l’URL de connexion** de votre application et cochez la case pour **Configurer le certificat utilisé pour l’authentification unique fédérée**. Cliquez ensuite sur **Suivant**.
   
    ![Authentification unique Azure AD](./media/active-directory-sso-certs/new-app-config-sso.PNG)
4. Sur la page suivante, sélectionnez **Générer un nouveau certificat**, puis sélectionnez la durée de validité de votre choix pour le certificat. Cliquez ensuite sur **Next**.
   
    ![Générer un nouveau certificat](./media/active-directory-sso-certs/new-app-config-cert.PNG)
5. Cliquez ensuite sur **Télécharger le certificat**. Pour savoir comment charger le certificat vers votre application SaaS, cliquez sur **Afficher les instructions de configuration**.
   
    ![Télécharger, puis charger le certificat](./media/active-directory-sso-certs/new-app-config-app.PNG)
6. Le certificat n’est pas activé tant que vous n’avez pas coché la case de confirmation en bas de la boîte de dialogue et appuyé sur Envoyer.

## <a name="how-to-renew-a-certificate-that-will-soon-expire"></a>Renouvellement d’un certificat arrivant à expiration
Dans l’idéal, les étapes de renouvellement ci-dessous ne doivent pas entraîner de temps d’arrêt important pour vos utilisateurs. Les captures d’écran présentées dans cette section ont été prises pendant une utilisation de Salesforce, mais les étapes sont valables pour n’importe quelle application SaaS fédérée.

1. Dans Azure Active Directory, sur la page Démarrage rapide de votre application, cliquez sur **Configurer l’authentification unique**.
   
    ![Ouvrir l’Assistant Configuration de l’authentification unique](./media/active-directory-sso-certs/renew-sso-button.PNG)
2. **L’Authentification unique Azure AD** doit déjà être sélectionnée sur la première page de la boîte de dialogue. Vous pouvez donc cliquer sur **Suivant**.
3. Sur la deuxième page, cochez la case pour **Configurer le certificat utilisé pour l’authentification unique fédérée**. Cliquez ensuite sur **Next**.
   
    ![Authentification unique Azure AD](./media/active-directory-sso-certs/renew-config-sso.PNG)
4. Sur la page suivante, sélectionnez **Générer un nouveau certificat**, puis sélectionnez la durée de validité de votre choix pour le nouveau certificat. Cliquez ensuite sur **Next**.
   
    ![Générer un nouveau certificat](./media/active-directory-sso-certs/new-app-config-cert.PNG)
5. Cliquez sur **Télécharger le certificat**. Pour renouveler correctement votre certificat, vous devez suivre ces deux étapes :
   
   * Chargez le nouveau certificat sur l’écran de configuration de l’authentification unique de l’application SaaS. Pour savoir comment procéder pour votre application SaaS, cliquez sur **Afficher les instructions de configuration**.
   * Dans Azure AD, activez le nouveau certificat en cochant la case de confirmation située en bas de la boîte de dialogue, puis cliquez sur **Suivant** pour l’envoyer.
     
     > [!IMPORTANT]
     > L’authentification unique pour l’application est alors désactivée au moment où l’une de ces étapes est terminée, mais elle est réactivée une fois la deuxième étape terminée. Par conséquent, pour réduire le temps d’arrêt, veuillez vous préparer à mener ces deux étapes en très peu de temps.
     > 
     > 
     
     ![Télécharger, puis charger le certificat](./media/active-directory-sso-certs/renew-config-app.PNG)

## <a name="related-articles"></a>Articles connexes
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Accès aux applications et authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Dépannage de l’authentification unique basée sur SAML](active-directory-saml-debugging.md)

<!--HONumber=Oct16_HO2-->


