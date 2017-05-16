---
title: "Didacticiel : Intégration d’Azure Active Directory à PerformanceCentre | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et PerformanceCentre."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 65288c32-f7e6-4eb3-a6dc-523c3d748d1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 3b1a347322e2307aadbd287cb2235e39e11f1e79
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Didacticiel : Intégration d’Azure Active Directory à PerformanceCentre
L’objectif de ce didacticiel est de vous montrer comment intégrer PerformanceCentre dans Azure AD (Azure Active Directory).  

L’intégration de PerformanceCentre dans Azure AD vous offre les avantages suivants : 

* Dans Azure AD, vous pouvez contrôler qui a accès à PerformanceCentre. 
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à PerformanceCentre via l’authentification unique avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure Active Directory Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD avec PerformanceCentre, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement PerformanceCentre pour lequel l’authentification unique est activée

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

*  Ajout de PerformanceCentre à partir de la galerie 
*  Configuration et test de l’authentification unique Azure AD

## <a name="add-performancecentre-from-the-gallery"></a>Ajouter PerformanceCentre à partir de la galerie
Pour configurer l’intégration de PerformanceCentre avec Azure AD, vous devez ajouter PerformanceCentre à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter PerformanceCentre à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, entrez **PerformanceCentre**.
   
    ![Applications][5]
7. Dans le volet des résultats, sélectionnez **PerformanceCentre**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Applications][500]

## <a name="configure-and-test-azure-ad-sso"></a>Configurer et tester l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec PerformanceCentre, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir qui est l’utilisateur PerformanceCentre équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur PerformanceCentre associé doit être établie.  

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans PerformanceCentre.

**Pour configurer et tester l’authentification unique Azure AD avec PerformanceCentre, vous avez besoin de suivre les indications des sections suivantes :**

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test PerformanceCentre](#creating-a-halogen-software-test-user)** pour avoir un équivalent de Britta Simon dans PerformanceCentre qui soit lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure AD Classic et de la configurer dans votre application PerformanceCentre.

**Pour configurer l’authentification unique Azure AD avec PerformanceCentre, procédez comme suit :**

1. Dans la page d’intégration d’applications **PerformanceCentre** du portail Azure AD Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à PerformanceCentre**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][7] 
3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :
   
    ![Authentification unique Azure AD][8] 
   
   1. Dans la zone de texte **URL d’authentification**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre site PerformanceCentre (par exemple, *http://nomentreprise.performancecentre.com/saml/SSO*).
   2. Cliquez sur **Next**.
4. Sur la page **Configurer l’authentification unique sur PerformanceCentre** , procédez comme suit :
   
    ![Authentification unique Azure AD][9] 
   
  * Cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.
5. Connectez-vous à votre site d’entreprise **PerformanceCentre** en tant qu’administrateur.
6. Dans l’onglet sur le côté gauche, cliquez sur **Configure**.
   
    ![Authentification unique Azure AD][10]
7. Dans l’onglet sur le côté gauche, cliquez sur **Miscellaneous**, puis cliquez sur **Single Sign On**.
   
    ![Authentification unique Azure AD][11]
8. Pour **Protocol**, sélectionnez **SAML**.
   
    ![Authentification unique Azure AD][12]
9. Ouvrez votre fichier de métadonnées téléchargé dans le Bloc-notes, copiez son contenu, collez-le dans la zone de texte **Identity Provider Metadata**, puis cliquez sur **Save**.
   
    ![Authentification unique Azure AD][13]
10. Vérifiez que les valeurs **Entity Base URL** et **Entity ID URL** sont correctes.
    
     ![Authentification unique Azure AD][14]
11. Dans le portail Azure AD Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**. 
    
     ![Authentification unique Azure AD][15]
12. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
    
     ![Authentification unique Azure AD][16]

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.  

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_09.png)  
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas. 
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit : 
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_05.png)  
   
   1. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   2. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   3. Cliquez sur **Suivant**.
   
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit : 
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_06.png) 
   
   1. Dans la zone de texte **First Name**, tapez **Britta**.  
   2. Dans la zone de texte **Last Name**, tapez **Simon**.
   3. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   4. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   5. Cliquez sur **Next**.
   
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_08.png) 
   
   1. Notez la valeur du **Nouveau mot de passe**.
   2. Cliquez sur **Terminé**.   

### <a name="create-a-performancecentre-test-user"></a>Créer un utilisateur de test PerformanceCentre
L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans PerformanceCentre.

**Pour créer un utilisateur appelé Britta Simon dans PerformanceCentre, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise PerformanceCentre en tant qu’administrateur.
2. Dans le menu de gauche, cliquez sur **Interrelate**, puis cliquez sur **Create Participant**.
   
    ![Create User][400]
3. Dans la boîte de dialogue **Interrelate - Create Participant** , effectuez les opérations suivantes :
   
    ![Create User][401]
   
   1. Entrez les attributs requis pour Britta Simon dans les zones de texte correspondantes.

    >[!IMPORTANT]
    >L’attribut de nom d’utilisateur de Britta dans PerformanceCentre doit être le même que le nom d’utilisateur dans Azure AD.
    > 
    > 
 
   2. Sélectionnez **Client Administrator** pour **Choose Role**.
   3. Cliquez sur **Save**.   


### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à PerformanceCentre.

![Affecter des utilisateurs][200] 

**Pour attribuer Britta Simon à PerformanceCentre, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.
   
    ![Affecter des utilisateurs][201]
2. Dans la liste des applications, sélectionnez **PerformanceCentre**.
   
    ![Affecter des utilisateurs][202]
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="test-single-sign-on"></a>Tester l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.  

Lorsque vous cliquez sur la vignette PerformanceCentre dans le volet d’accès, vous devez être connecté automatiquement à votre application PerformanceCentre.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_01.png
[500]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_02.png

[6]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_03.png
[8]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_04.png
[9]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_05.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png
[15]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_06.png
[16]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_50.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png
[402]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_402.png




