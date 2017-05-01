---
title: "Didacticiel : intégration d’Azure Active Directory à Deputy | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Deputy."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 5665c3ac-5689-4201-80fe-fcc677d4430d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: c3c0079ef9ef7b3be7c8df9280e6ebfb34902801
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>Didacticiel : Intégration d’Azure Active Directory avec Deputy
L’objectif de ce didacticiel est de vous montrer comment intégrer Deputy dans Azure Active Directory (Azure AD).

L’intégration de Deputy dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Deputy
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Deputy (via l’authentification unique) avec leur compte Azure AD
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD avec Deputy, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement Deputy pour lequel l’authentification unique (SSO) est activée

>[!NOTE]
>Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.
> 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Deputy depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-deputy-from-the-gallery"></a>Ajout de Deputy depuis la galerie
Pour configurer l’intégration de Deputy avec Azure AD, vous devez ajouter Deputy, à partir de la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Deputy à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **Deputy**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_01.png)
7. Dans le volet des résultats, sélectionnez **Deputy**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Sélection de l’application dans la galerie](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_0001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Deputy, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Deputy équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Deputy associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Deputy.

Pour configurer et tester l’authentification unique Azure AD avec Deputy, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Deputy](#creating-a-deputy-test-user)** pour avoir un équivalent de Britta Simon dans Deputy lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d'utiliser l'authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD
Dans cette section, vous activez l’authentification unique Azure AD dans le portail classique et vous configurez l’authentification unique dans votre application Deputy.

**Pour configurer l’authentification unique Azure AD avec Deputy, procédez comme suit :**

1. Dans le portail classique, dans la page d’intégration d’applications **Deputy**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Deputy**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_03.png)
3. Dans la page de boîte de dialogue **Configurer les paramètres d’application**, si vous souhaitez configurer l’application en **mode lancé par le fournisseur d’identité**, procédez comme suit et cliquez sur **Suivant** :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_04.png)
  1. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<your-subdomain>.<region>.deputy.com`.
  2. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<your-subdomain>.<region>.deputy.com/exec/devapp/samlacs`.
  3. Cliquez sur **Next**.
4. Si vous souhaitez configurer l’application en **mode lancé par le fournisseur de service** dans la page de boîte de dialogue **Configurer les paramètres d’application**, cliquez sur **Affichez les paramètres avancés (facultatif)**, saisissez **l’URL de connexion** et cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_05.png)
   1. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<your-subdomain>.<region>.deputy.com`.
   2. Cliquez sur **Next**.
   
     >[!NOTE]
     > Le suffixe de région Deputy est facultatif ou il doit utiliser l’une des valeurs suivantes : au | na | eu |as |la |af |an |ent-au |ent-na |ent-eu |ent-as | ent-la | ent-af | ent-an
     > 

5. Dans la page **Configurer l’authentification unique sur Deputy**, procédez comme suit et cliquez sur **Suivant** :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_06.png)
   *  Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.
6. Accédez à l’URL suivante : https://(votre-sous-domaine).deputy.com/exec/config/system_config. Accédez aux **Paramètres de sécurité** et cliquez sur **Modifier**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_004.png)
7. Dans le portail Azure Classic, sur la page Configurer l’authentification unique à Deputy, copiez l’URL SAML SSO. 
8. Sur cette page **Paramètres de sécurité** , procédez comme suit.
   
![Configurer l’authentification unique](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_005.png)

   1. Activez la **connexion à partir de réseaux sociaux**.
   2. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat OpenSSL**.
   3. Dans la zone de texte URL SAM SSO, tapez `https://<your subdomain>.deputy.com/exec/devapp/samlacs?dpLoginTo=<saml sso url>`
   4. Dans la zone de texte URL SAM SSO, remplacez `<your subdomain>` par votre sous-domaine.
   5. Dans la zone de texte URL SAM SSO, remplacez `<saml sso url>` par l’URL SAML SSO que vous avez copiée à partir du portail Azure Classic.
   6. Cliquez sur **Save Settings**.
9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
10. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
    
    ![Authentification unique Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail classique.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_09.png)
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
       ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_03.png)
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_04.png)
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_05.png)

   1. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   2. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   3. Cliquez sur **Suivant**.6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_06.png)
   
   1. Dans la zone de texte **First Name**, tapez **Britta**.  
   2. Dans la zone de texte **Last Name**, tapez **Simon**.
   3. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   4. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   5. Cliquez sur **Next**.
   
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_07.png)
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_08.png)
   
   1. Notez la valeur du **Nouveau mot de passe**.
   2. Cliquez sur **Terminé**.   

### <a name="create-a-deputy-test-user"></a>Créer un utilisateur de test Deputy
Pour se connecter à Deputy, les utilisateurs d’Azure AD doivent être approvisionnés dans Deputy. Dans le cas de Deputy, l’approvisionnement est une tâche manuelle.

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous à votre site d’entreprise Deputy en tant qu’administrateur.
2. Dans le volet de navigation situé en haut, cliquez sur **People**(Personnes).
   
   ![Personnes](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_001.png "Personnes")
3. Cliquez sur le bouton **Add People** (Ajouter des personnes), puis cliquez sur **Add a single person** (Ajouter une seule personne).
   
   ![Ajouter des personnes](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_002.png "ajouter des personnes")
4. Effectuez les opérations suivantes, puis cliquez sur **Save & Invite** (Enregistrer et inviter).
   
   ![Nouvel utilisateur](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_003.png "Nouvel utilisateur")
   
  1. Dans la zone de texte **Name**, entrez **Britta** et **Simon**.  
  2. Dans la zone de texte **Email** , tapez l’adresse de messagerie du compte Azure AD que vous souhaitez approvisionner.
  3. Dans la zone de texte **Work at** , tapez le nom de l’entreprise.
  4. Cliquez sur le bouton **Save & Invite**.

5. Le détenteur du compte AAD recevra un message électronique et suivra un lien pour confirmer le compte avant qu’il ne soit activé. Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur fourni par Deputy pour approvisionner des comptes d’utilisateurs AAD.
    
### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Deputy.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à Deputy, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue de répertoire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][201]
2. Dans la liste des applications, sélectionnez **Deputy**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_50.png)
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="test-single-sign-on"></a>Tester l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Deputy dans le volet d’accès, vous devez être connecté automatiquement à votre application Deputy.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_205.png

