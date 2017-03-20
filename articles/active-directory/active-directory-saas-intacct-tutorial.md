---
title: "Didacticiel : Intégration d’Azure Active Directory avec Intacct | Microsoft Docs"
description: "Apprenez à utiliser Intacct avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 20db13c5ba67fcc402db5c692842719dd9a34eb1
ms.openlocfilehash: 0ea77d5f9c5b139ce6f87bc0cdd817166b03c22e
ms.lasthandoff: 03/01/2017

---

# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>Didacticiel : Intégration d’Azure Active Directory à Intacct
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Intacct.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire Intacct

À l’issue de ce didacticiel, les utilisateurs Azure Active Directory (Azure AD) que vous avez affectés à Intacct pourront s’authentifier dans l’application sur votre site d’entreprise Intacct (connexion initiée par le fournisseur du service) ou en s’aidant du [Volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

* Activation de l’intégration d’application pour Intacct
* Configuration de l'authentification unique
* Configuration de l'approvisionnement des utilisateurs
* Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-intacct-tutorial/IC790030.png "Scénario")

## <a name="enable-the-application-integration-for-intacct"></a>Activer l’intégration d’applications pour Intacct
Cette section décrit l’activation de l’intégration d’application pour Intacct.

**Pour activer l’intégration d’applications pour Intacct, suivez les étapes ci-dessous :**

1. Dans le volet gauche du portail Azure Classic, cliquez sur l’icône Active Directory.

   ![Active Directory](./media/active-directory-saas-intacct-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d’annuaire, cliquez sur **Applications** dans le menu du haut.

   ![Applications](./media/active-directory-saas-intacct-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.

   ![Ajouter une application](./media/active-directory-saas-intacct-tutorial/IC749321.png "Ajouter une application")
5. Sur la page **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-intacct-tutorial/IC749322.png "Ajouter une application à partir de la galerie")
6. Dans la zone de recherche, entrez **Intacct**.

   ![Galerie d’applications](./media/active-directory-saas-intacct-tutorial/IC790031.png "Galerie d’applications")
7. Dans le volet de résultats, sélectionnez **Intacct**, puis cliquez sur **Terminer** pour ajouter l’application.

   ![Intacct](./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")

## <a name="configure-single-sign-on"></a>Configurer l’authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Intacct avec leur compte Azure AD. Une fédération basée sur le protocole SAML est utilisée dans le cadre de cette authentification.  

Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

**Pour configurer l’authentification unique, procédez comme suit :**

1. Sur la page d’intégration d’applications **Intacct** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la page **Configurer l’authentification unique**.

   ![Configurer l’authentification unique](./media/active-directory-saas-intacct-tutorial/IC790033.png "Configurer l’authentification unique")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Intacct**, sélectionnez **Authentification unique avec Window Azure AD**, puis cliquez sur **Suivant**.

   ![Configurer l’authentification unique](./media/active-directory-saas-intacct-tutorial/IC790034.png "Configurer l’authentification unique")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion**, tapez votre URL selon le modèle *https://Intacct.com/company*, puis cliquez sur **Suivant**.

   ![Configurer l’URL de l’application](./media/active-directory-saas-intacct-tutorial/IC790035.png "Configurer l’URL de l’application")
4. Dans la page **Configurer l’authentification unique sur Intacct**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

   ![Configurer l’authentification unique](./media/active-directory-saas-intacct-tutorial/IC790036.png "Configurer l’authentification unique")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Intacct en tant qu’administrateur.
6. Cliquez sur l’onglet **Company**, puis sur **Company Info**.

   ![Company](./media/active-directory-saas-intacct-tutorial/IC790037.png "Company")
7. Cliquez sur l’onglet **Security**, puis sur **Edit**.

   ![Sécurité](./media/active-directory-saas-intacct-tutorial/IC790038.png "Sécurité")
8. Dans la section **Single sign on (SSO)** , procédez comme suit :

   ![Single sign On](./media/active-directory-saas-intacct-tutorial/IC790039.png "Single sign On")

   1. Sélectionnez **Enable Single Sign-On**.
   2. Pour **Identity provider type**, sélectionnez **SAML 2.0**.
   3. Dans la page **Configurer l’authentification unique sur Intacct** du portail Azure Classic, copiez la valeur **URL de l’émetteur**, puis collez-la dans la zone de texte **URL de l’émetteur**.
   4. Dans la page **Configurer l’authentification unique sur Intacct** du portail Azure Classic, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **URL de connexion**.
   5. Créez un fichier **codé en base 64** à partir du certificat téléchargé. Pour plus d’informations, consultez la section [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).      
   6. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone **Certificat**.
   7. Cliquez sur **Save**.
9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la page **Configurer l’authentification unique**.

   ![Configurer l’authentification unique](./media/active-directory-saas-intacct-tutorial/IC790040.png "Configurer l’authentification unique")

## <a name="configure-user-provisioning"></a>Configurer l'approvisionnement de l'utilisateur

Pour configurer les utilisateurs Azure AD afin qu’ils puissent se connecter à Intacct, ils doivent être approvisionnés dans Intacct. Pour Intacct, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre locataire **Intacct**.
2. Cliquez sur l’onglet **Company**, puis sur **Users**.

   ![Utilisateurs](./media/active-directory-saas-intacct-tutorial/IC790041.png "Utilisateurs")
3. Cliquez sur l’onglet **Ajouter**.

   ![Add](./media/active-directory-saas-intacct-tutorial/IC790042.png "Add")
4. Dans la section **User Information** , procédez comme suit :

   ![User Information](./media/active-directory-saas-intacct-tutorial/IC790043.png "User Information")

   1. Tapez **l’ID utilisateur**, le **nom**, le **prénom**, **l’adresse de messagerie**, le **titre** et le **numéro de téléphone** d’un compte Azure AD que vous souhaitez approvisionner dans la section **Informations utilisateur**.
   2. Sélectionnez les **privilèges d’administrateur** d’un compte Azure AD que vous voulez approvisionner.
   3. Cliquez sur **Save**. Le détenteur du compte Azure AD reçoit un message électronique et suit un lien pour confirmer le compte avant qu’il ne soit activé.

>[!NOTE]
>Pour approvisionner des comptes utilisateur Azure AD, vous pouvez utiliser d’autres outils de création de compte utilisateur Intacct ou des API fournies par Intacct.
>

## <a name="assign-users"></a>Affecter des utilisateurs
Pour tester votre configuration, vous devez affecter des utilisateurs Azure AD à Intacct. Une fois qu’un utilisateur est attribué, il peut accéder à votre application.

**Pour affecter des utilisateurs à Intacct, suivez les étapes ci-dessous :**

1. Dans le portail Azure Classic, créez un compte de test.
2. Sur la page d’intégration d’applications **Intacct**, cliquez sur **Affecter des utilisateurs**.

   ![Affecter des utilisateurs](./media/active-directory-saas-intacct-tutorial/IC790044.png "Affecter des utilisateurs")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

   ![Oui](./media/active-directory-saas-intacct-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez configurer Britta Simon pour utiliser l’authentification unique Azure en lui accordant l’accès à Intacct.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à Intacct, procédez comme suit :**

1. Dans le Portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, naviguez jusqu’à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Intacct**.

    ![Configurer l’authentification unique](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_50.png)

3. Dans le menu **Gérer**, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202]

4. Cliquez sur le bouton **Ajouter**, puis dans **Ajouter une affectation**, sélectionnez **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][203]

5. Dans **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Dans **Utilisateurs et groupes**, cliquez sur le bouton **Sélectionner**.

7. Dans **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.



### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Intacct dans le volet d’accès, vous devez être connecté automatiquement à votre application Intacct.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_203.png

