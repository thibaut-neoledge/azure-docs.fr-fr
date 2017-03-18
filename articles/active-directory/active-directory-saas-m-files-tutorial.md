---
title: "Didacticiel : Intégration d’Azure Active Directory dans M-Files | Documentation Microsoft"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et M-Files."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 4536fd49-3a65-4cff-9620-860904f726d0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4a07f946d396a9263d5b00202cd5229ddc86d1be
ms.openlocfilehash: 78f0a065b675326a9a507e0cf480bc779fd458a9
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Didacticiel : Intégration d’Azure AD à M-Files
Dans ce didacticiel, vous allez apprendre à intégrer M-Files à Azure Active Directory (Azure AD).

L’intégration de M-Files dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à M-Files
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à M-Files via l’authentification unique (SSO) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l'intégration d'Azure AD avec M-Files, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement **M-Files** pour lequel l’authentification unique est activée

>[!NOTE]
>Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production. 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de M-Files à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-m-files-from-the-gallery"></a>Ajout de M-Files à partir de la galerie
Pour configurer l’intégration de M-Files avec Azure AD, vous devez ajouter M-Files à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter M-Files à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, entrez **M-Files**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_01.png)
7. Dans le volet des résultats, sélectionnez **M-Files**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec M-Files avec un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur M-Files équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur M-Files associé doit être établie.

Pour cela, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** dans M-Files. Pour configurer et tester l’authentification unique Azure AD avec M-Files, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l'authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test M-Files](#creating-a-m-file-test-user)** pour avoir un équivalent de Britta Simon dans M-Files lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-sso"></a>Configuration de l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de la configurer dans votre application M-Files.

**Pour configurer l’authentification unique Azure AD avec M-Files, procédez comme suit :**

1. Dans le portail Classic, dans la page d’intégration de l’application **M-Files**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][7] 
2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à M-Files**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_06.png)
3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_07.png)
  1. Dans la zone de texte URL de connexion, tapez une URL au format suivant : `https://<tenant-name>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`
  2. Cliquez sur **Suivant**.
4. Sur la page **Configurer l’authentification unique sur M-Files**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_09.png)
5. Pour obtenir la configuration de l’authentification unique pour votre application, contactez l’équipe de support M-Files à l’adresse <mailto:support@m-files.com> , en lui fournissant les métadonnées téléchargées.
   
   >[!NOTE]
   >Suivez les étapes suivantes si vous souhaitez configurer l’authentification unique pour votre application de bureau M-Files. Aucune étape supplémentaire n’est nécessaire si vous souhaitez configurer l’authentification unique pour la version web de M-Files uniquement.  
   > 
6. Suivez la procédure suivante pour configurer l’application de bureau M-Files de sorte à activer l’authentification unique avec Azure AD. Pour télécharger M-Files, accédez à la page [Téléchargement de M-Files](https://www.m-files.com/en/download-latest-version).
7. Ouvrez la fenêtre **Paramètres de bureau M-Files**. Cliquez ensuite sur **Add**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_10.png)
8. Dans la fenêtre **Propriétés de connexion au coffre de documents**, procédez comme suit :
   
  ![Configurer l’authentification unique](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_11.png)  
    Sous la section **Serveur**, tapez les valeurs comme suit :  
 1. Pour **Nom**, saisissez `<tenant-name>.cloudvault.m-files.com`.  
 2. Pour **Numéro de port**, saisissez **4466**. 
 3. Pour **Protocole**, sélectionnez **HTTPS**. 
 4. Dans le champ **Authentification**, sélectionnez **Utilisateur Windows spécifique**. Ensuite, une page de signature s’affiche. Entrez vos informations d’identification Azure AD. 
 5. Pour **Coffre sur le serveur**,  sélectionnez le coffre correspondant sur le serveur. 
 6. Cliquez sur **OK**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_09.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_05.png) 
 1. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.  
 2. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**. 
 3. Cliquez sur **Suivant**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_06.png)  
 1. Dans la zone de texte **First Name**, tapez **Britta**.   
 2. Dans la zone de texte **Last Name**, tapez **Simon**. 
 3. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**. 
 4. Dans la liste **Rôle**, sélectionnez **Utilisateur**. 
 5. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_08.png)  
 1. Notez la valeur du **Nouveau mot de passe**. 
 2. Cliquez sur **Terminé**.   

### <a name="create-a-m-files-test-user"></a>Créer un utilisateur de test M-Files
Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans M-Files. Si vous ne savez pas comment créer un utilisateur dans M-Files, contactez l’assistance technique de M-Files à l’adresse <mailto:support@m-files.com>.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD
Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à M-Files.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à M-Files, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **M-Files**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_12.png)
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203] 
4. Dans la liste Tous les utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="test-single-sign-on"></a>Tester l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque M-Files dans le volet d'accès, vous êtes connecté automatiquement à votre application M-Files.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-m-files-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_205.png

