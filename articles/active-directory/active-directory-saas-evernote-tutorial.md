---
title: "Didacticiel : Intégration d’Azure Active Directory avec Evernote | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Evernote."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: e4e2ca40ae270e7d3bfe4e828fcd571b5594ffc8
ms.lasthandoff: 03/09/2017


---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>Didacticiel : Intégration d’Azure Active Directory avec Evernote

Dans ce didacticiel, vous allez apprendre à intégrer Evernote à Azure Active Directory (Azure AD).

L’intégration d’Evernote avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Evernote.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Evernote (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes de manière centralisée dans le Portail de gestion Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Evernote, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Evernote pour lequel l’authentification unique est activée


> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Evernote à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## <a name="adding-evernote-from-the-gallery"></a>Ajout d’Evernote à partir de la galerie
Pour configurer l’intégration d’Evernote à Azure AD, vous devez, à partir de la galerie, ajouter Evernote à votre liste d’applications SaaS gérées.

**Pour ajouter Evernote à partir de la galerie, procédez comme suit :**

1. Dans le **[Portail de gestion Azure](https://portal.azure.com)**, dans le panneau de navigation gauche, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, entrez **Evernote**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_search01.png)

5. Dans le volet de résultats, sélectionnez **Evernote**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_search_result01.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Evernote, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Evernote équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Evernote associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Evernote.

Pour configurer et tester l’authentification unique Azure AD avec Evernote, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
4. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail de gestion Azure et configurer l’authentification unique dans votre application Evernote.

**Pour configurer l’authentification unique Azure AD avec Evernote, procédez comme suit :**

1. Dans le portail de gestion Azure, sur la page d’intégration de l’application **Evernote**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_samlbase.png)

3. Dans la section **Evernote Domain and URLs** (Domaine et URL Evernote), si vous souhaitez configurer l’application en **IDP initiated mode** (Mode initié par IDP), vous n’avez aucune opération à effectuer.

    ![Configurer l’authentification unique](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_idp.png)
    
4. Dans la section **Evernote Domain and URLs** (Domaine et URL Evernote), si vous souhaitez configurer l’application en **SP initiated mode** (Mode initié par SP), procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_sp_01.png)
    
    a. Cliquez sur l’option **Afficher les paramètres d’URL avancés**.

    b. Dans la zone de texte **URL de connexion**, tapez l’URL de connexion suivante : `https://www.evernote.com/Login.action`

5. Dans la section **Certificat de signature SAML**, cliquez sur **Créer un certificat**.

    ![Configurer l’authentification unique](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_cert.png)     

6. Dans la boîte de dialogue **Créer un certificat**, cliquez sur l’icône de calendrier et sélectionnez une **date d’expiration**. Ensuite, cliquez sur le bouton **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-evernote-tutorial/tutorial_general_300.png)

7. Dans la section **Certificat de signature SAML**, sélectionnez **Activer le nouveau certificat** et cliquez sur **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_makecertactive.png)

8. Dans la fenêtre contextuelle **Certificat de substitution**, cliquez sur **OK**.

    ![Configurer l’authentification unique](./media/active-directory-saas-evernote-tutorial/tutorial_general_400.png)

9. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certificate.png) 

10. Dans la section **Evernote Configuration** (Configuration d’Evernote), cliquez sur **Configure Evernote** (Configurer Evernote) pour ouvrir la fenêtre **Configurer l’authentification**.

    ![Configurer l’authentification unique](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_configure.png) 

    ![Configurer l’authentification unique](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_configuresignon.png)

11. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Evernote en tant qu’administrateur.

12. Accédez à la **Console d’administration**

    ![Admin-Console](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

13. À partir de la **Console d’administration**, accédez à **Sécurité** et sélectionnez **Authentification unique**

    ![SSO-Setting](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_sso.png)

14. Configurez les valeurs suivantes :

    a.  **Activer SSO :** l’authentification unique est activée par défaut (cliquez sur **Désactiver l’authentification unique** pour supprimer l’exigence SSO)

    b. **SAML HTTP Request URL** (URL de requête HTML SAML) : renseignez le champ **SAML Single sign-on Service URL** (URL du service d’authentification unique SAML) à partir de la section **Configure Evernote** (Configurer Evernote) sur Azure AD

    c. **Certificat X.509** : ouvrez le certificat téléchargé à partir d’Azure AD dans un bloc-notes et copiez-en le contenu, de « BEGIN CERTIFICATE » jusqu’à « END CERTIFICATE »

    ![Certificate-Setting](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certx.png)

    d.Cliquez sur **Enregistrer les modifications** 


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le Portail de gestion Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-evernote-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-evernote-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-evernote-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-evernote-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**. 



### <a name="creating-an-evernote-test-user"></a>Création d’un utilisateur de test Evernote

Pour se connecter à Evernote, les utilisateurs d’Azure AD doivent être approvisionnés dans Evernote.  
Dans le cas d’Evernote, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Evernote en tant qu’administrateur.

2. Cliquez sur la **Console d’administration**.

    ![Admin-Console](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Dans la **Console d’administration**, accédez à **Ajouter des utilisateurs**.

    ![Add-testUser](./media/active-directory-saas-evernote-tutorial/create_aaduser_0001.png)

4. Sélectionnez **Ajouter des membres de l’équipe** dans la zone de texte **E-mail**, saisissez l’adresse de messagerie du compte d’utilisateur et cliquez sur **Inviter.**

    ![Add-testUser](./media/active-directory-saas-evernote-tutorial/create_aaduser_0002.png)
    
5. Une fois l’invitation envoyée, le titulaire du compte Azure Active Directory recevra un courrier électronique pour accepter l’invitation.   


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Evernote.

![Affecter des utilisateurs][200] 

**Pour attribuer Britta Simon à Evernote, procédez comme suit :**

1. Dans le Portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires, allez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Evernote**.

    ![Configurer l’authentification unique](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Evernote dans le panneau d’accès, vous devriez vous connecter à votre application Evernote. Vous allez vous connecter en tant qu’organisation, mais vous devrez ensuite vous connecter avec votre compte personnel.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_203.png

