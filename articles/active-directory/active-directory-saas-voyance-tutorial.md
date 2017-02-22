---
title: "Didacticiel : Intégration d’Azure Active Directory avec Voyance | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Voyance."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 539dc1f9-64c9-4dce-b259-2b0b49dcf857
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: d316178b9d3451eaefe88a8245538aef42b57a85
ms.openlocfilehash: 466b16cf391f6de101b6d212ad51d296d77cc804


---
# <a name="tutorial-azure-active-directory-integration-with-voyance"></a>Didacticiel : Intégration d’Azure Active Directory à Voyance

Dans ce didacticiel, vous allez apprendre à intégrer Voyance à Azure Active Directory (Azure AD).

L’intégration de Voyance à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Voyance
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Voyance (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Voyance, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Voyance pour lequel l’authentification unique est activée


> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Voyance à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## <a name="adding-voyance-from-the-gallery"></a>Ajout de Voyance à partir de la galerie
Pour configurer l’intégration de Voyance à Azure AD, vous devez ajouter Voyance à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Voyance à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone de recherche, tapez **Voyance**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_01.png)

7. Dans le volet des résultats, sélectionnez **Voyance**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Voyance avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Voyance équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Voyance associé doit être établie.

Pour ce faire, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **Username** dans Voyance.

Pour configurer et tester l’authentification unique Azure AD avec Voyance, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Voyance](#creating-a-voyance-test-user)** pour avoir un équivalent de Britta Simon dans Voyance lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail classique et configurer l’authentification unique dans votre application Voyance.


**Pour configurer l’authentification unique Azure AD avec Voyance, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’applications **Voyance**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique][6]

2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Voyance**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_02.png)

3. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, si vous souhaitez configurer l’application en **mode lancé par le fournisseur d’identité**, procédez comme suit et cliquez sur **Suivant** :

    ![Configurer l’authentification unique](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_03.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<company name>.nyansa.com`.

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<company name>.nyansa.com/saml/create/`.

    c. Cliquez sur **Suivant**.

4. Si vous souhaitez configurer l’application en **mode lancé par le fournisseur de service** dans la page de boîte de dialogue **Configurer les paramètres d’application**, cliquez sur **Affichez les paramètres avancés (facultatif)**, saisissez **l’URL de connexion** et cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_04.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<company name>.nyansa.com/`.

    b. Cliquez sur **Next**.

    > [!NOTE]
    > Vous devez mettre à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique Voyance](emaiLto:support@nyansa.com).

5. Dans la page **Configurer l’authentification unique sur Voyance**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur :

    ![Configurer l’authentification unique](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_05.png) 

6. Dans une autre fenêtre de navigateur web, connectez-vous à votre client Voyance en tant qu’administrateur.

7. Accédez au coin supérieur droit de la barre de navigation et cliquez sur la liste déroulante indiquant « **Acme University** ».
    
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_001.png) 

8. Cliquez sur « **Paramètres d’administration** ».

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_002.png)

9. Cliquez sur l’onglet « **Accès utilisateur** »

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_003.png)

10. Cliquez sur le bouton « **SSO est désactivé** » pour configurer Azure AD en tant que fournisseur d’identité à l’aide de SAML 2.0.

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_004.png)

11. Accédez à la section **SAML v2** et suivez les étapes ci-dessous :

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_005.png)

    a. Sélectionnez **Enabled**.

    b. Dans la zone de texte **IdP Login URL** (URL de connexion IdP), copiez la valeur de **l’URL d’authentification unique SAML** indiquée dans l’Assistant Configuration de l’application Azure AD.

    c. Ouvrez votre certificat codé en base64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **IdP Cert** (Certificat IdP).

    d. Cliquez sur le bouton **Enregistrer** .

12. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

    ![Authentification unique Azure AD][10]

13. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
  
    ![Authentification unique Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail classique.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_09.png) 

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_04.png) 

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_05.png) 

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_06.png) 

    a. Dans la zone de texte **First Name**, tapez **Britta**.  

    b. Dans la zone de texte **Last Name**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_08.png) 

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.   



### <a name="creating-a-voyance-test-user"></a>Création d’un utilisateur de test Voyance

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Voyance. Voyance prend en charge l’approvisionnement juste-à-temps, option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à Voyance s’il n’existe pas déjà.

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez [l’équipe du support technique Voyance](emaiLto:support@nyansa.com).


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Voyance.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Voyance, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Voyance**.

    ![Configurer l’authentification unique](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_50.png) 

3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

    ![Affecter des utilisateurs][203] 

4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
    
    ![Affecter des utilisateurs][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la mosaïque Voyance dans le volet d’accès, vous devez vous connecter automatiquement à votre application Voyance.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


