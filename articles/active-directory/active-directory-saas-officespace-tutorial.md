---
title: "Didacticiel : Intégration d’Azure Active Directory avec OfficeSpace Software | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et OfficeSpace Software."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 01e2bcf3fa32546a7952ddc919f99b46a74755a2
ms.openlocfilehash: e0933b1a7e19fc70f9c5e81225b296412837385e


---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Didacticiel : Intégration d’Azure Active Directory avec OfficeSpace Software

Dans ce didacticiel, vous allez apprendre à intégrer OfficeSpace Software à Azure Active Directory (Azure AD).

L’intégration d’OfficeSpace Software à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à OfficeSpace Software
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à OfficeSpace Software (authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec OfficeSpace Software, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement OfficeSpace Software pour lequel l’authentification unique est activée


> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’OfficeSpace Software à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## <a name="adding-officespace-software-from-the-gallery"></a>Ajout d’OfficeSpace Software à partir de la galerie
Pour configurer l’intégration d’OfficeSpace Software à Azure AD, vous devez ajouter OfficeSpace Software, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter OfficeSpace Software à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone de recherche, tapez **OfficeSpace Software**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_001.png)

7. Dans le volet des résultats, sélectionnez **OfficeSpace Software**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec OfficeSpace Software avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur OfficeSpace Software équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur OfficeSpace Software associé doit être établi.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Nom d’utilisateur** dans OfficeSpace Software.

Pour configurer et tester l’authentification unique Azure AD avec OfficeSpace Software, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test OfficeSpace Software](#creating-an-officespace-software-test-user)** pour avoir un équivalent de Britta Simon dans OfficeSpace Software qui soit lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le Portail Azure Classic et de configurer l’authentification unique dans votre application OfficeSpace Software.

Votre application OfficeSpace Software attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de l’onglet**Attribut**de l’application. La capture d’écran suivante montre un exemple : 

![Configurer l’authentification unique](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_01.png)

**Pour configurer l’authentification unique Azure AD avec OfficeSpace Software, procédez comme suit :**

1. Dans le Portail Azure Classic, sur la page d’intégration d’application **OfficeSpace Software**, dans le menu situé en haut, cliquez sur **Attributs**.

    ![Configurer l’authentification unique](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_02.png)

2. Dans la boîte de dialogue **Attributs du jeton SAML** , pour chaque ligne indiquée dans le tableau ci-dessous, procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | --- | --- |    
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier | user.mail |
    | email | user.mail |
    | name | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

    a. Cliquez sur **ajouter un attribut utilisateur** pour ouvrir la boîte de dialogue **Ajouter un attribut utilisateur**.

    ![Configurer l’authentification unique](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_03.png)
    
    b. Dans la zone de texte **Nom de l’attribut** , indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la liste **Valeur de l’attribut**, entrez la valeur d’attribut pour cette ligne.
    
    d. Cliquez sur **Terminé**

3. Dans le menu situé en haut, cliquez sur **Démarrage rapide**.

    ![Configurer l’authentification unique](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_04.png) 

4. Dans le portail Classic, sur la page d’intégration d’application **OfficeSpace Software**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_05.png)

5. Sur la page **Comment voulez-vous que les utilisateurs se connectent à OfficeSpace Software**, sélectionnez **Authentification unique avec Azure AD**, puis cliquez sur **Suivant**.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_06.png)

6. Sur la page **Configurer les paramètres d’application** , procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_07.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<company name>.officespace.com/users/sign_in/saml`

    b. Cliquez sur **Suivant**.

    > [!NOTE] 
    > Notez qu’il ne s’agit pas de la valeur réelle. Vous devez mettre à jour la valeur avec l’URL de connexion réelle. Contactez [l’équipe de support d’OfficeSpace Software](emaiLto:support@officespacesoftware.com) pour obtenir cette valeur.

7. Sur la page **Configurer l’authentification unique sur OfficeSpace Software**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur :

    ![Configurer l’authentification unique](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_08.png) 

8. Dans une autre fenêtre de navigateur web, connectez-vous à votre locataire OfficeSpace Software en tant qu’administrateur.

9. Accédez à **ADMINISTRATEUR** et cliquez sur **Connecteurs**.

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_002.png)

10. Cliquez sur **SAML Authorization**.

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_003.png)

11. Dans la section **SAML Authentication** , procédez comme suit :

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_004.png)

    a. Dans la zone de texte **URL du fournisseur de déconnexion**, copiez la valeur de **l’URL de connexion à distance** indiquée dans l’Assistant Configuration de l’application Azure AD.

    b. Dans la zone de texte **URL cible de l’IdP client**, copiez la valeur de **l’URL de déconnexion distante** indiquée dans l’Assistant Configuration de l’application Azure AD.

    c. Copiez la valeur **Empreinte** du certificat téléchargé, puis collez-la dans la zone de texte **Client idp cert fingerprint**. 

    d. Cliquez sur **Save Settings**.

    > [!NOTE]
    > Pour plus d’informations, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

12. Dans le portail classique, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

    ![Authentification unique Azure AD][10]

13. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
  
    ![Authentification unique Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail classique.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_09.png) 

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_04.png) 

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_05.png) 

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_06.png) 

    a. Dans la zone de texte **First Name**, tapez **Britta**.  

    b. Dans la zone de texte **Last Name**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_08.png) 

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.   



### <a name="creating-an-officespace-software-test-user"></a>Créer un utilisateur de test OfficeSpace Software

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans OfficeSpace Software. OfficeSpace Software prend en charge l’approvisionnement juste-à-temps, option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à OfficeSpace Software s’il n’existe pas déjà.

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support d’OfficeSpace Software](emaiLto:support@officespacesoftware.com).


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à OfficeSpace Software.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à OfficeSpace Software, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **OfficeSpace Software**.

    ![Configurer l’authentification unique](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_50.png) 

3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

    ![Affecter des utilisateurs][203] 

4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
    
    ![Affecter des utilisateurs][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette OfficeSpace Software dans le volet d’accès, vous êtes automatiquement connecté à votre application OfficeSpace Software.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->


