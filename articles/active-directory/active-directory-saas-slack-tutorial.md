---
title: "Didacticiel : Intégration d’Azure Active Directory à Slack | Microsoft Docs"
description: "Découvrez comment utiliser Slack avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c7a0b761-75b7-4e6b-9980-71d645643a78
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/21/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4d5144c6a690c2338dec45b27dcb308328b6fecb
ms.openlocfilehash: 37440a8ba397c4dc227a448dfa574cebd14be49c


---

# <a name="tutorial-azure-active-directory-integration-with-slack"></a>Didacticiel : Intégration d’Azure AD avec Slack

L’objectif de ce didacticiel est de vous montrer comment intégrer Slack dans Azure Active Directory (Azure AD).

L’intégration de Slack dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Slack
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Slack (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Slack, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Slack pour lequel l’authentification unique est activée


> [!NOTE]
>  Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Slack à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## <a name="adding-slack-from-the-gallery"></a>Ajout de Slack à partir de la galerie
Pour configurer l’intégration de Slack avec Azure AD, vous devez ajouter Slack à partir de la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Slack à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
    
    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.
    
    ![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone de recherche, tapez **Slack**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-slack-tutorial/tutorial_slack_01.png)

7. Dans le volet des résultats, sélectionnez **Slack**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Sélection de l’application dans la galerie](./media/active-directory-saas-slack-tutorial/tutorial_slack_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Slack, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Slack équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Slack associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Slack.

Pour configurer et tester l’authentification unique Azure AD avec Slack, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Slack](#creating-a-slack-test-user)** pour avoir un équivalent de Britta Simon dans Slack, lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application Slack.

L’application Slack attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de l’onglet**Attribut**de l’application. La capture d’écran suivante montre un exemple : 

![Configurer l’authentification unique](./media/active-directory-saas-slack-tutorial/tutorial_slack_09.png)

**Pour configurer l’authentification unique Azure AD avec Slack, procédez comme suit :**

1. Sur la page d’intégration d’application **Slack** du portail Azure Classic, dans le menu situé en haut, cliquez sur **Attributs**.

    ![Configurer l’authentification unique](./media/active-directory-saas-slack-tutorial/tutorial_slack_03.png)

2. Dans la boîte de dialogue **Attributs du jeton SAML** , pour chaque ligne indiquée dans le tableau ci-dessous, procédez comme suit :

    | Nom de l'attribut | Valeur de l’attribut |
    | --- | --- |    
    | User.Email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Username | extractmailprefix([userprincipalname]) |

    a. Cliquez sur **ajouter un attribut utilisateur** pour ouvrir la boîte de dialogue **Ajouter un attribut utilisateur**.

    ![Configurer l’authentification unique](./media/active-directory-saas-slack-tutorial/tutorial_slack_04.png)
    
    b. Dans la zone de texte **Nom de l’attribut** , indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la liste **Valeur de l’attribut**, entrez la valeur d’attribut pour cette ligne.
    
    d. Cliquez sur **Terminé**

3. Dans le menu situé en haut, cliquez sur **Démarrage rapide**.

    ![Configurer l’authentification unique](./media/active-directory-saas-slack-tutorial/tutorial_slack_05.png) 

4. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Slack**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-slack-tutorial/tutorial_slack_06.png)

5. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, procédez comme suit et cliquez sur **Suivant** :

    ![Configurer l’authentification unique](./media/active-directory-saas-slack-tutorial/tutorial_slack_07.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<company name>.slack.com`.

    b. Cliquez sur **Next**.

    > [!NOTE] 
    > Notez que vous devez mettre à jour la valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez l’équipe de support technique de Slack.

6. Dans la page **Configurer l’authentification unique sur Slack**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-slack-tutorial/tutorial_slack_08.png)

7.  Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Slack en tant qu’administrateur.

8.  Accédez à **Microsoft Azure AD**, puis à **Team Settings**.

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

9.  Dans la section **Team Settings**, cliquez sur l’onglet **Authentication** puis sur **Change Settings**.

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

10. Dans la boîte de dialogue **SAML Authentication Settings** , procédez comme suit :

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)

    a.  Dans la zone de texte **SAML 2.0 Endpoint (HTTP)**, copiez la valeur de **l’URL d’authentification unique SAML** indiquée dans l’Assistant Configuration de l’application Azure AD.

    b.  Dans la zone de texte **Identity Provider Issuer** (Émetteur du fournisseur d’identité), copiez la valeur de **URL de l’émetteur** indiquée dans l’Assistant Configuration de l’application Azure AD.

    c.  Ouvrez votre fichier de certificat téléchargé dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat public**.

    d.  Décochez la case **Autoriser les utilisateurs à modifier leur adresse de messagerie**.

    e.  Cochez la case **Autoriser les utilisateurs à choisir leur propre nom d’utilisateur**.

    f.  Dans **L’authentification de votre équipe doit être utilisée par**, sélectionnez **C’est facultatif**.

    g.  Cliquez sur **Enregistrer la configuration**.

11. Dans le portail Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
    
    ![Authentification unique Azure AD][10]

12. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
    
    ![Authentification unique Azure AD][11]



### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail classique.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_09.png)

2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png)

5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_05.png)

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_06.png)

    a. Dans la zone de texte **First Name**, tapez **Britta**.  

    b. Dans la zone de texte **Last Name**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_07.png)

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_08.png)

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.   



### <a name="creating-a-slack-test-user"></a>Création d’un utilisateur de test Slack

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Slack. Slack prend en charge l’approvisionnement juste-à-temps, option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à Slack s’il n’existe pas déjà.

> [!NOTE] 
> Si vous devez créer un utilisateur manuellement, contactez l’équipe du support technique de Slack.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Slack.
    
![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à Slack, procédez comme suit :**

1. Pour ouvrir l’affichage des applications dans le portail Classic, dans l’affichage de l’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
    
    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Slack**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-slack-tutorial/tutorial_slack_50.png)

3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
    
    ![Affecter des utilisateurs][203]

4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
    
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.
 
Lorsque vous cliquez sur la mosaïque Slack dans le volet d’accès, vous devez être connecté automatiquement à votre application Slack.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-slack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-slack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-slack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-slack-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-slack-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-slack-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-slack-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-slack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-slack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-slack-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-slack-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-slack-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-slack-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO4-->


