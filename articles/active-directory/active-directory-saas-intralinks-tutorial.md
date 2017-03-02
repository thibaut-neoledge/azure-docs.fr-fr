---
title: "Didacticiel : intégration d’Azure Active Directory à Intralinks | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Intralinks."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 147f2bf9-166b-402e-adc4-4b19dd336883
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ed2fc2b34ff10acc806daec84986f8db58e713c3
ms.openlocfilehash: 2112f4b129ff8735955d440341174a44a6a1469c
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-intralinks"></a>Didacticiel : intégration d’Azure Active Directory à Intralinks
Dans ce didacticiel, vous allez apprendre à intégrer Intralinks à Azure Active Directory (Azure AD).

L’intégration d’Intralinks à Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez déterminer qui a accès à Intralinks.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Intralinks par le biais de l’authentification unique (SSO) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD à Intralinks, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement Intralinks pour lequel l’authentification unique est activée

>[!NOTE]
>Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production. 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Intralinks à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="add-intralinks-from-the-gallery"></a>Ajouter Intralinks à partir de la galerie
Pour configurer son intégration à Azure AD, vous devez ajouter Intralinks à votre liste d’applications SaaS gérées, à partir de la galerie.

**Pour ajouter Intralinks à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, saisissez **Intralinks**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_01.png)
7. Dans le volet de résultats, sélectionnez **Intralinks**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_02.png)

## <a name="configure-and-testing-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Intralinks, en tirant parti d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Intralinks équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur Intralinks associé.

>[!NOTE]
>Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Intralinks.
>

Pour configurer et tester l’authentification unique Azure AD avec Intralinks, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Intralinks](#creating-an-intralinks-test-user)** pour avoir un équivalent de l’utilisateur Britta Simon dans Intralinks lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#testing-single-sign-on)** pour vérifier que la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD
Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application Intralinks.

**Pour configurer l’authentification unique Azure AD avec Intralinks, procédez comme suit :**

1. Dans le portail Classic, sur la page d’intégration d’applications **Intralinks**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Intralinks ?**, sélectionnez **Authentification unique avec Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_03.png) 
3. Sur la page de boîte de dialogue **Configurer les paramètres de l’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_04.png) 
   
    1. Dans la zone de texte **URL de connexion**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Intralinks, au format suivant : **https://\<nom entreprise\>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/\<ID client Azure AD\>/**.
    2. Cliquez sur **Suivant**.
4. Sur la page **Configurer l’authentification unique sur Intralinks** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_05.png)
   
    1. Cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.
    2. Cliquez sur **Suivant**.
5. Pour configurer l’authentification unique pour votre application, contactez l’équipe du support technique Intralinks et joignez le fichier de métadonnées téléchargé à votre e-mail.
6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.

* Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_09.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur**, procédez comme suit :  ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_05.png) 
   
    1. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation. 
    2. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
    3. Cliquez sur **Suivant**.
6. Sur la page de boîte de dialogue **Profil utilisateur**, procédez comme suit : ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_06.png) 
   
   1. Dans la zone de texte **First Name**, tapez **Britta**.  
   2. Dans la zone de texte **Last Name**, tapez **Simon**.
   3. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.   
   4. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   5. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_08.png) 
   
    1. Notez la valeur du **Nouveau mot de passe**.
    2. Cliquez sur **Terminé**.   

### <a name="create-an-intralinks-test-user"></a>Créer un utilisateur de test Intralinks
Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Intralinks. Collaborez avec l’équipe du support technique Intralinks pour ajouter des utilisateurs sur la plateforme Intralinks.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD
Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Intralinks.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Intralinks, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **Intralinks**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_50.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="add-intralinks-via-or-elite-application"></a>Ajouter une application Intralinks VIA ou Elite
Intralinks utilise la même plateforme d’identité pour l’authentification unique quelle que soit l’application Intralinks, à l’exception de Dealnexus. Par conséquent, si vous envisagez d’utiliser une autre application Intralinks, vous devez d’abord configurer l’authentification unique pour une application Intralinks principale, en suivant la procédure décrite ci-dessus.

Après cela, vous pouvez suivre la procédure ci-dessous pour ajouter une autre application Intralinks dans votre client, afin de pouvoir tirer parti de cette application principale pour l’authentification unique. 

>[!NOTE]
>Cette fonctionnalité est uniquement disponible pour les clients de la référence SKU Azure AD Premium. Les clients dotés de la SKU gratuite ou de base du logiciel ne peuvent pas l’utiliser.
> 

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans l’onglet de gauche, cliquez sur l’onglet **Personnalisé**.
   
    ![Ajout d’une application Intralinks VIA ou Elite](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_51.png)
7. Donnez un nom approprié à l’application, par exemple **Intralinks Elite**, puis cliquez sur le bouton Terminer.
8. Cliquez sur le bouton **Configurer l’authentification unique**.
9. Sélectionnez l’option **Authentification unique existante**.
   
    ![Ajout d’une application Intralinks VIA ou Elite](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_52.png)
10. Pour obtenir l’URL d’authentification unique initiée par le fournisseur de services pour l’autre application Intralinks, contactez l’équipe Intralinks. Saisissez l’URL ainsi obtenue comme indiqué ci-dessous. 
    
    ![Ajout d’une application Intralinks VIA ou Elite](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_53.png)
    
    * Dans la zone de texte URL de connexion, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Intralinks, au format suivant : **https://\<NomEntreprise\>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/\<IDClientAzureAD\>/**
11. Cliquez sur **Suivant**.
12. Affectez l’application à un utilisateur ou à un groupe, comme l’indique la section **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)**.

### <a name="test-single-sign-on"></a>Tester l’authentification unique
Dans cette section, vous allez tester la configuration SSO Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Intralinks dans le volet d’accès, vous devez être connecté automatiquement à votre application Intralinks.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_205.png

