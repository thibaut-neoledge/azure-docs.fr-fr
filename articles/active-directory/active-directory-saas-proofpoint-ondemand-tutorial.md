---
title: "Didacticiel : Intégration d’Azure Active Directory à Proofpoint on Demand | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Proofpoint on Demand."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 773e7f7d-ec31-411b-860d-6a6633335d43
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 7aa746b8c6f4b747a7dca0e9bf75e61b2707b7e1


---
# <a name="tutorial-azure-active-directory-integration-with-proofpoint-on-demand"></a>Didacticiel : Intégration d’Azure Active Directory avec Proofpoint on Demand
Dans ce didacticiel, vous allez apprendre à intégrer Proofpoint on Demand à Azure Active Directory (Azure AD).

L’intégration de Proofpoint on Demand dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Proofpoint on Demand.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Proofpoint on Demand (via l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD avec Proofpoint on Demand, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement Proofpoint on Demand avec authentification unique

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

* N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajoutez Proofpoint on Demand à partir de la galerie.
2. Configurez et testez l’authentification unique Azure AD.

## <a name="add-proofpoint-on-demand-from-the-gallery"></a>Ajouter Proofpoint on Demand à partir de la galerie
Pour configurer l’intégration de Proofpoint on Demand à Azure AD, vous devez ajouter Proofpoint on Demand à partir de la galerie à votre liste d’applications SaaS gérées.

1. Dans le volet de navigation de gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
    ![Icône Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d’annuaire, cliquez sur **APPLICATIONS** dans le menu du haut.
   
    ![Élément du menu APPLICATIONS][2]
4. Cliquez sur **AJOUTER** en bas de la page.
   
    ![Bouton AJOUTER][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Choix de l’ajout d’une application à partir de la galerie][4]
6. Dans la zone de recherche, tapez **Proofpoint on Demand**.
   
    ![Zone dans laquelle taper « Proofpoint on Demand »](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_01.png)
7. Dans le volet des résultats, sélectionnez **Proofpoint on Demand**, puis cliquez sur **Terminer** pour ajouter l’application.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Proofpoint on Demand avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Proofpoint on Demand équivalent dans Azure AD. En d’autres termes, vous devez établir une relation entre l’utilisateur Azure AD et l’utilisateur Proofpoint on Demand associé.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Proofpoint on Demand.

Pour configurer et tester l’authentification unique Azure AD avec Proofpoint on Demand, vous devez suivre la procédure ci-dessous :

1. [Configurer l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. [Créer un utilisateur de test Azure AD](#creating-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec Britta Simon.
3. [Créer un utilisateur de test Proofpoint on Demand](#creating-a-proofpoint-ondemand-test-user) pour avoir un équivalent de Britta Simon dans Proofpoint on Demand lié à la représentation Azure AD associée.
4. [Affecter l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user) pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. [Tester l’authentification unique](#testing-single-sign-on) pour vérifier que la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD
Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail classique et configurer l’authentification unique dans votre application Proofpoint on Demand.

1. Dans le portail Classic, dans la page d’intégration de l’application **Proofpoint on Demand**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Bouton Configurer l’authentification unique][6]
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Proofpoint on Demand ?**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
    ![Option Authentification unique Microsoft Azure AD](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_03.png)
3. Dans la page **Configurer les paramètres de l’application** , procédez comme suit :
   
    ![Page Configurer les paramètres de l’application avec les zones renseignées](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_04.png)
   
    a. Dans la zone **URL DE CONNEXION**, tapez l’URL via laquelle les utilisateurs se connectent à votre application Proofpoint on Demand. Utilisez le modèle suivant : **https://\<nomhôte\>.pphosted.com/ppssamlsp_nomhôte**
   
    b. Dans la zone **IDENTIFICATEUR**, tapez l’URL en utilisant le modèle suivant : **https://\<nomhôte/>.pphosted.com/ppssamlsp**
   
    c. Dans la zone **URL DE RÉPONSE**, tapez l’URL en utilisant le modèle suivant : **https://\<nomhôte/>.pphosted.com:numéroport/v1/samlauth/samlconsumer**
   
    d. Cliquez sur **Next**.
4. Sur la page **Configurer l’authentification unique sur Proofpoint on Demand**, procédez comme suit :
   
    ![Page Configurer l’authentification unique sur Proofpoint on Demand avec le bouton Télécharger le certificat](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_05.png)
   
    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.
   
    b. Cliquez sur **Next**.
5. Pour obtenir la configuration de l’authentification unique pour votre application, contactez l’équipe de support Proofpoint on Demand et fournissez-lui les éléments suivants :
   
    • Le certificat téléchargé
   
    • L’ID d’entité
   
    • L’URL d’authentification unique SAML
6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Case à cocher confirmant que vous avez configuré l’authentification unique][10]
7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Page Confirmation][11]

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test nommé Britta Simon dans le portail Classic.

![L’utilisateur de test dans la liste des utilisateurs][20]

1. Dans le volet de navigation de gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
    ![Icône Active Directory](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_09.png)
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **UTILISATEURS**.
   
    ![Élément du menu UTILISATEURS](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_03.png)
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **AJOUTER UN UTILISATEUR** figurant dans la barre d’outils en bas.
   
    ![Bouton AJOUTER UN UTILISATEUR](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_04.png)
5. Dans la page **Dites-nous en plus sur cet utilisateur**, suivez la procédure ci-dessous :  ![Page Dites-nous en plus sur cet utilisateur avec les zones renseignées](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_05.png)
   
    a. Dans la zone **TYPE D’UTILISATEUR**, sélectionnez **Nouvel utilisateur dans votre organisation**.
   
    b. Dans la zone **NOM D’UTILISATEUR**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
6. Dans la page **profil utilisateur**, suivez la procédure ci-dessous : ![Page Profil utilisateur avec les zones renseignées](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_06.png)
   
   a. Dans la zone **PRÉNOM**, tapez **Britta**.  
   
   b. Dans la zone **NOM**, tapez **Simon**.
   
   c. Dans la zone **NOM D’AFFICHAGE**, entrez **Britta Simon**.
   
   d. Dans la liste **RÔLE**, sélectionnez **Utilisateur**.
   
   e. Cliquez sur **Next**.
7. Dans la page **Obtenir un mot de passe temporaire**, cliquez sur **Créer**.
   
    ![Bouton de création d’un mot de passe temporaire](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_07.png)
8. Dans la page **Obtenir un mot de passe temporaire**, suivez la procédure ci-dessous :
   
    ![Page Obtenir un mot de passe temporaire avec les informations de mot de passe](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_08.png)
   
    a. Notez la valeur dans la zone **NOUVEAU MOT DE PASSE**.
   
    b. Cliquez sur **Terminé**.   

### <a name="create-a-proofpoint-on-demand-test-user"></a>Créer un utilisateur de test Proofpoint on Demand
Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Proofpoint on Demand. Collaborez avec l’équipe de support technique de Proofpoint on Demand pour ajouter des utilisateurs dans la plateforme Proofpoint on Demand.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD
Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Proofpoint on Demand.

![Informations utilisateur, montrant l’activation de l’accès via la méthode directe][200]

1. Dans l’affichage de l’annuaire au sein du portail Classic, cliquez sur l’option **APPLICATIONS** figurant dans le menu du haut pour ouvrir la vue des applications.
   
    ![Élément du menu APPLICATIONS][201]
2. Dans la liste des applications, sélectionnez **Proofpoint on Demand**.
   
    ![Liste des applications avec Proofpoint on Demand sélectionné](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_50.png)
3. Dans le menu du haut, cliquez sur **UTILISATEURS**.
   
    ![Élément du menu UTILISATEURS][203]
4. Dans la liste des utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils en bas, cliquez sur **ATTRIBUER**.
   
    ![Bouton Attribuer][205]

### <a name="test-single-sign-on"></a>Tester l’authentification unique
Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque **Proofpoint on Demand** dans le volet d’accès, vous devez être automatiquement connecté à votre application Proofpoint on Demand.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->


