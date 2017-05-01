---
title: "Didacticiel : Intégration d’Azure Active Directory à Adobe Creative Cloud | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Adobe Creative Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 9ba1171e-56b1-4475-b308-58637d35e5a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 3d13608612c77236346b0e98551d7fc427d602e1
ms.lasthandoff: 04/07/2017


---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Didacticiel : Intégration d’Azure Active Directory à Adobe Creative Cloud

Dans ce didacticiel, vous allez apprendre à intégrer Adobe Creative Cloud à Azure Active Directory (Azure AD).

L’intégration de Adobe Creative Cloud dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Adobe Creative Cloud
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Adobe Creative Cloud (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes de manière centralisée dans le portail de gestion Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Adobe Creative Cloud, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Adobe Creative Cloud pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Adobe Creative Cloud à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Ajout de Adobe Creative Cloud à partir de la galerie
Pour configurer l’intégration de Adobe Creative Cloud à Azure AD, vous devez ajouter Adobe Creative Cloud de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Adobe Creative Cloud à partir de la galerie, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[Portail de gestion Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Adobe Creative Cloud**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_000.png)

5. Dans le volet de résultats, sélectionnez **Adobe Creative Cloud**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Adobe Creative Cloud, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Adobe Creative Cloud équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Adobe Creative Cloud associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Adobe Creative Cloud.

Pour configurer et tester l’authentification unique avec Azure AD avec Adobe Creative Cloud, vous devez compléter les blocs de construction suivants :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l'authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Adobe Creative Cloud](#creating-an-adobe-creative-cloud-test-user)** pour avoir un équivalent de Britta Simon dans Adobe Creative Cloud lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d'utiliser l'authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail de gestion Azure et configurer l’authentification unique dans votre application Adobe Creative Cloud.

**Pour configurer l’authentification unique Azure AD avec Adobe Creative Cloud, procédez comme suit :**

1. Dans le portail de gestion Azure, sur la page d’intégration de l’application **Adobe Creative Cloud**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_01.png)

3. Dans la section **Domaines et URL Adobe Creative Cloud**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en **Mode initié par IDP** :

    ![Configurer l’authentification unique](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_url1.png)

    a. Dans la zone de texte **Identificateur**, entrez la valeur `https://www.okta.com/saml2/service-provider/<token>`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<company name>.okta.com/auth/saml20/accauthlinktest`

    > [!NOTE] 
    > Notez qu’il ne s’agit pas des valeurs réelles. Vous devez mettre à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Nous vous suggérons d’utiliser ici la valeur de chaîne unique dans l’identificateur. Si vous devez créer un utilisateur manuellement, contactez l’équipe du support technique Adobe Creative Cloud.

4. Dans la section **Domaines et URL Adobe Creative Cloud**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en **Mode initié par SP** :

    ![Configurer l’authentification unique](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_url2.png)

    a. Cliquez sur l’option **Afficher les paramètres d’URL avancés**.

    b. Dans la zone de texte **URL de connexion**, tapez la valeur suivante : `https://adobe.com`

5. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_05.png) 

6. Dans la section **Configuration de Adobe Creative Cloud**, cliquez sur **Configurer Adobe Creative Cloud** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’ID d’entité SAML** et **l’URL du service SSO SAML** à partir de la section Référence rapide.

    ![Configurer l’authentification unique](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_06.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre client Adobe Creative Cloud en tant qu’administrateur.

8.  Accédez à **Identité** dans le volet de navigation de gauche et cliquez sur votre domaine. Suivez ensuite la procédure de la section **Configuration de l’authentification unique requise**.

    ![Paramètres](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_001.png "Paramètres")

9. Cliquez sur **Parcourir** pour télécharger le certificat obtenu à partir d’Azure AD dans **Certificat IDP**.

10. Dans la zone de texte **IDP issuer**, placez la valeur de **l’ID d’entité SAML** que vous avez copiée depuis la section **Configurer l’authentification** du portail Azure.

11. Dans la zone de texte **IDP Login URL**, placez la valeur de **l’URL du service SSO SAML** que vous avez copiée depuis la section **Configurer l’authentification** du portail Azure.

12. Sélectionnez **HTTP - Redirection** en tant que **Liaison IDP**.

13. Sélectionnez **Adresse de messagerie** en tant que **Paramètre de connexion utilisateur**.
 
14. Cliquez sur le bouton **Enregistrer** .

15. Le tableau de bord présente maintenant le fichier XML **« Télécharger les métadonnées »**. Il contient les URL EntityDescriptor et AssertionConsumerService d’Adobe. Ouvrez le fichier et configurez-les dans l’application Azure AD.

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_002.png)

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Utilisez la valeur EntityDescriptor que Adobe vous a fournie pour **Identificateur** dans la boîte de dialogue **Configurer les paramètres d’application**.

    b. Utilisez la valeur AssertionConsumerService que Adobe vous a fournie pour **URL de réponse** dans la boîte de dialogue **Configurer les paramètres d’application**.
 
### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le Portail de gestion Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**. 

### <a name="creating-an-adobe-creative-cloud-test-user"></a>Création d’un utilisateur d’Adobe Creative Cloud

Pour permettre aux utilisateurs Azure AD de se connecter à Adobe Creative Cloud, vous devez les approvisionner dans Adobe Creative Cloud.  
Dans le cas d’Adobe Creative Cloud, cet approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise Adobe Creative Cloud en tant qu’administrateur.

2. Cliquez sur **People**.

    ![Personnes](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_001.png "Personnes")

3. Cliquez sur **Invite User**.

    ![Inviter des utilisateurs](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_002.png "inviter des utilisateurs")

4. Dans la boîte de dialogue **Invite People**, procédez comme suit :

    ![Inviter des personnes](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_003.png "inviter des personnes")

    a. Dans la zone de texte **E-mail** , tapez l’adresse de messagerie du compte de Britta Simon.
    
    b. Cliquez sur **Inviter**.

    > [!NOTE]
    > Le titulaire du compte Azure Active Directory reçoit un message électronique contenant un lien à suivre pour confirmer son compte et l’activer.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Adobe Creative Cloud.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon Adobe Creative Cloud, procédez comme suit :**

1. Dans le portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Adobe Creative Cloud**.

    ![Configurer l’authentification unique](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_50.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Adobe Creative Cloud dans le volet d’accès, vous devez être connecté automatiquement à votre application Adobe Creative Cloud.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_203.png
