---
title: "Didacticiel : Intégration d’Azure Active Directory à Absorb LMS | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Absorb LMS."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: f5f20fee648cf26b4db49b9a8f5f5e5bcecd453e
ms.contentlocale: fr-fr
ms.lasthandoff: 09/13/2017

---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Didacticiel : Intégration d’Azure Active Directory avec Absorb LMS

Dans ce didacticiel, vous allez apprendre à intégrer Absorb LMS avec Azure Active Directory (Azure AD).

L’intégration d’Absorb LMS avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Absorb LMS.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Absorb LMS (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes depuis un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration de l’application SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec Absorb LMS, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Absorb LMS pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Absorb LMS à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-absorb-lms-from-the-gallery"></a>Ajout d’Absorb LMS à partir de la galerie
Pour configurer l’intégration d’Absorb LMS avec Azure AD, vous devez ajouter Absorb LMS à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Absorb LMS à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Absorb LMS**, sélectionnez **Absorb LMS** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Absorb LMS dans la liste des résultats](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Absorb LMS, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Absorb LMS équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Absorb LMS associé doit être établie.

Pour ce faire, affectez la valeur du champ **nom d’utilisateur** d’Azure AD comme valeur du champ **Username** (Nom d’utilisateur) dans Absorb LMS.

Pour configurer et tester l’authentification unique Azure AD avec Absorb LMS, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur test Absorb LMS](#create-an-absorb-lms-test-user)** pour avoir un équivalent de Britta Simon dans Absorb LMS lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le nouveau portail Azure et configurer l’authentification unique dans votre application Absorb LMS.

**Pour configurer l’authentification unique Azure AD avec Absorb LMS, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Absorb LMS**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. Dans la section **Domaine et URL Absorb LMS**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Absorb LMS](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.myabsorb.com/Account/SAML`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<subdomain>.myabsorb.com/Account/SAML`
     
    > [!NOTE] 
    > Il ne s’agit pas des valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Absorb LMS](https://www.absorblms.com/support). 

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-absorblms-tutorial/tutorial_general_400.png)
    
7. Dans la section **Configuration de Absorb LMS** , cliquez sur **Configurer Absorb LMS** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**URL de déconnexion** à partir de la **section Référence rapide.**

    ![Configuration d’Absorb LMS](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_configure.png) 

8. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Absorb LMS en tant qu’administrateur.

9. Cliquez sur **l’icône du compte** dans l’interface d’administration. 

    ![Configurer l’authentification unique](./media/active-directory-saas-absorblms-tutorial/1.png)

10. Cliquez sur **Paramètres du portail**.

    ![Configurer l’authentification unique](./media/active-directory-saas-absorblms-tutorial/2.png)
    
11. Cliquez sur l’onglet **Users** .

    ![Configurer l’authentification unique](./media/active-directory-saas-absorblms-tutorial/3.png)

12. Pour accéder aux champs de configuration de l’authentification unique, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-absorblms-tutorial/4.png)

    a. Sélectionnez **Identity Provider Initiated** (Initiée par le fournisseur d’identité).

    b. Ouvrez le certificat que vous avez téléchargé à partir du portail Azure dans le Bloc-notes, supprimez les balises **---BEGIN CERTIFICATE---** et **---END CERTIFICATE---**, puis collez le contenu restant dans la zone de texte **Key** (Clé).
    
    c. Dans **Id Property** (Propriété d’ID), sélectionnez l’attribut approprié configuré comme identificateur d’utilisateur dans Azure AD (par exemple, si userprinciplename est sélectionné dans Azure AD, ici vous devez sélectionner Username.)

    d. Dans **Login URL** (URL de connexion), collez l’**URL d’accès utilisateur** indiquée dans la page **Propriétés** du portail Azure.

    e. Dans **Logout URL** (URL de déconnexion), collez la valeur de l’**URL de déconnexion** que vous avez copiée depuis la fenêtre **Configurer l’authentification** du portail Azure.

13. Activez **« Autoriser uniquement la connexion SSO »**.

    ![Configurer l’authentification unique](./media/active-directory-saas-absorblms-tutorial/5.png)

14. Cliquez sur **Save** (Enregistrer).

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-absorblms-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-absorblms-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Bouton Ajouter](./media/active-directory-saas-absorblms-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-absorblms-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.

### <a name="create-an-absorb-lms-test-user"></a>Créer un utilisateur de test Absorb LMS

Pour se connecter à Absorb LMS, les utilisateurs d’Azure AD doivent être approvisionnés dans Absorb LMS.  
Pour Absorb LMS, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Absorb LMS en tant qu’administrateur.

2. Cliquez sur l’onglet **Users** (Utilisateurs).

    ![Inviter des personnes](./media/active-directory-saas-absorblms-tutorial/absorblms_users.png)

3. Cliquez sur **Users** (Utilisateurs) sous l’onglet **Users**.

    ![Inviter des personnes](./media/active-directory-saas-absorblms-tutorial/absorblms_userssub.png)

4.  Sélectionnez **User** (Utilisateur) dans la liste déroulante **Add New** (Ajouter nouveau).

    ![Inviter des personnes](./media/active-directory-saas-absorblms-tutorial/absorblms_createuser.png)

5. Dans la page **Add user** (Ajouter un utilisateur), procédez comme suit :

    ![Inviter des personnes](./media/active-directory-saas-absorblms-tutorial/user.png)

    a. Dans la zone de texte **First Name** (Prénom), tapez le prénom, par exemple Britta.

    b. Dans la zone de texte **Last Name** (Nom), tapez le nom, par exemple Simon.
    
    c. Dans la zone de texte **Username** (Nom d’utilisateur), tapez le nom d’utilisateur, par exemple Britta Simon.

    d. Dans la zone de texte **Password** (Mot de passe), tapez le mot de passe de Britta Simon.

    e. Dans la zone de texte **Confirmer le mot de passe**, tapez le même mot de passe.
    
    f. Définissez-le comme **ACTIVE** (Actif).   

6. Cliquez sur **Save** (Enregistrer).
 
### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Absorb LMS.

![Attribuer le rôle utilisateur][200]

**Pour affecter Britta Simon à Absorb LMS, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Absorb LMS**.

    ![Lien Absorb LMS dans la liste des applications](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Absorb LMS dans le volet d’accès, vous êtes automatiquement connecté à votre application Absorb LMS. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_203.png


