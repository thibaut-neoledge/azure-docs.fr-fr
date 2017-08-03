---
title: "Gestion des certificats de fédération dans Azure AD | Microsoft Docs"
description: "Apprenez à personnaliser la date d’expiration pour vos certificats de fédération, mais aussi à renouveler les certificats arrivant à expiration."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f516f7f0-b25a-4901-8247-f5964666ce23
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 4b859d631617fc3d375711876cf23e201662e43a
ms.contentlocale: fr-fr
ms.lasthandoff: 07/12/2017

---
# <a name="managing-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Gestion des certificats pour l’authentification unique fédérée sur Azure Active Directory
Cet article répond aux questions relatives aux certificats créés par Azure Active Directory pour établir une authentification unique fédérée vers vos applications SaaS. Ces applications peuvent être ajoutées à partir de la galerie d’applications Azure AD ou à l’aide du modèle des applications ne figurant pas dans la galerie. L’application doit être configurée à l’aide de l’option d’authentification unique fédérée.

Cet article concerne uniquement les applications configurées pour utiliser **l’authentification unique Azure AD** avec la **fédération SAML**, comme le montre l’exemple suivant :

![Authentification unique Azure AD](./media/active-directory-sso-certs/saml_sso.PNG)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Certificat généré automatiquement pour les applications de la galerie et celles ne figurant pas dans la galerie
Lorsque vous ajoutez une nouvelle application à partir de la galerie et configurez l’authentification SAML, Azure AD génère un certificat d’une validité de trois ans pour l’application. Vous pouvez télécharger ce certificat dans la section **Certificat de signature SAML**. Pour les applications de la galerie, cette section peut afficher une option permettant de télécharger le **certificat** ou les **métadonnées de fédération**. L’option qui s’affiche dépend de la configuration de l’application.

![Authentification unique Azure AD](./media/active-directory-sso-certs/saml_certificate_download.png)

## <a name="how-to-customize-the-expiration-date-for-your-federation-certificate-and-roll-over-the-new-certificate"></a>Comment personnaliser la date d’expiration de votre certificat de fédération et activer le nouveau certificat
Par défaut, les certificats sont configurés pour expirer au bout de trois ans. Vous pouvez choisir une date d’expiration différente pour votre certificat en suivant les étapes ci-dessous. Les captures d’écran montrent une utilisation de Salesforce. Toutefois, ces étapes peuvent être effectuées dans n’importe quelle application SaaS fédérée.

1. Dans le portail de gestion [Azure Active Directory](https://aad.portal.azure.com), cliquez sur **Application d’entreprise**, puis cliquez sur **Nouvelle application** dans la page **Vue d’ensemble**, comme indiqué ci-dessous :

   ![Ouvrez l’Assistant Configuration de l’authentification unique.](./media/active-directory-sso-certs/enterprise_application_new_application.png)

2. Recherchez une application dans la galerie, puis sélectionnez celle que vous voulez ajouter. Si vous ne trouvez pas l’application voulue, ajoutez l’application à l’aide de l’option **Application ne figurant pas dans la galerie**. Cette fonctionnalité est disponible uniquement dans **Azure AD Premium (P1 et P2)**.

    ![Authentification unique Azure AD](./media/active-directory-sso-certs/add_gallery_application.png)

3. Cliquez sur le lien **Authentification unique** dans le volet de navigation gauche, puis configurez le **Mode** sur **Authentification basée sur SAML**. Cette opération génère pour votre application un certificat valide trois ans.

4. Pour créer un certificat, cliquez sur le lien **Créer un certificat** dans la section **Certificat de signature SAML**.

    ![Générer un nouveau certificat](./media/active-directory-sso-certs/create_new_certficate.png)

5. Quand vous cliquez sur le lien Créer un certificat, le calendrier s’ouvre et vous permet de définir une date et une heure d’expiration, pouvant aller jusqu’à trois ans. La date et l’heure sélectionnées correspondent à la date et à l’heure d’expiration de votre nouveau certificat. Cliquez sur le bouton **Enregistrer** pour enregistrer le certificat.

    ![Télécharger, puis charger le certificat](./media/active-directory-sso-certs/certifcate_date_selection.PNG)

6. Le nouveau certificat peut désormais être téléchargé. Cliquez sur le lien **Certificat** pour le télécharger. À ce stade, votre certificat n’est pas encore actif. Pour activer le certificat, cochez la case **Définir comme certificat actif**, puis cliquez sur Enregistrer. Une fois le certificat activé, Azure AD démarre à l’aide du nouveau certificat pour authentifier la réponse.

7.  Pour savoir comment charger le certificat dans une application SaaS spécifique, cliquez sur le lien permettant d’afficher le didacticiel sur la configuration de l’application.

## <a name="how-to-renew-a-certificate-that-will-soon-expire"></a>Renouvellement d’un certificat arrivant à expiration
Dans l’idéal, les étapes de renouvellement ci-dessous ne doivent pas entraîner de temps d’arrêt important pour vos utilisateurs. Les captures d’écran présentées dans cette section ont été prises pendant une utilisation de Salesforce, mais les étapes sont valables pour n’importe quelle application SaaS fédérée.

1. Dans l’application **Azure Active Directory**, dans la page **Authentification unique**, générez un nouveau certificat pour votre application. Pour cela, cliquez sur le lien **Créer un certificat** dans la section **Certificat de signature SAML**.

    ![Générer un nouveau certificat](./media/active-directory-sso-certs/create_new_certficate.png)

2. Sélectionnez la date et l’heure d’expiration souhaitées pour votre nouveau certificat, puis cliquez sur le bouton **Enregistrer**.

3. Téléchargez le certificat à l’aide de l’option Certificat de signature SAML. Chargez le nouveau certificat sur l’écran de configuration de l’authentification unique de l’application SaaS. Pour savoir comment charger le certificat dans une application SaaS spécifique, cliquez sur le lien permettant d’afficher le **didacticiel sur la configuration de l’application**.
   
4. Pour activer le nouveau certificat dans Azure AD, cochez la case Définir comme certificat actif, puis cliquez sur le bouton **Enregistrer** en haut de la page. Le nouveau certificat est ainsi activé côté Azure AD. L’état du certificat passe de **Nouveau** à **Actif**. Une fois le certificat activé, Azure AD démarre à l’aide du nouveau certificat pour authentifier la réponse. 
   
    ![Générer un nouveau certificat](./media/active-directory-sso-certs/new_certificate_download.png)

## <a name="related-articles"></a>Articles connexes
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Accès aux applications et authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Dépannage de l’authentification unique basée sur SAML](active-directory-saml-debugging.md)
