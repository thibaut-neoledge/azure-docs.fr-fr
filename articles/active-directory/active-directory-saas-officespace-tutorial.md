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
ms.date: 02/01/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: b8f354b34e1a3a581dd2e41df4b80cbdbcd9a705
ms.openlocfilehash: 3033df7c69a1c4211c906c6f3bdcd7868dedde13
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Didacticiel : Intégration d’Azure Active Directory avec OfficeSpace Software

Dans ce didacticiel, vous allez apprendre à intégrer OfficeSpace Software à Azure Active Directory (Azure AD).

L’intégration d’OfficeSpace Software à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à OfficeSpace Software
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à OfficeSpace Software (authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes de manière centralisée dans le portail de gestion Azure.

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

1. Dans le panneau de navigation gauche du **[Portail de gestion Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Puis à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **OfficeSpace Software**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_001.png)

5. Dans le volet des résultats, sélectionnez **OfficeSpace Software**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

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

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail de gestion Azure et configurer l’authentification unique dans votre application OfficeSpace Software.

**Pour configurer l’authentification unique Azure AD avec OfficeSpace Software, procédez comme suit :**

1. Dans le portail de gestion Azure, sur la page d’intégration de l’application **OfficeSpace**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, sous **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_01.png)

3. Dans la section **Domaine et URL OfficeSpace Software**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_02.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. Dans la zone de texte **Identificateur**, tapez une valeur au format suivant : `<company name>.officespacesoftware.com`

    > [!NOTE] 
    > Notez qu’il ne s’agit pas des valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Contactez [l’équipe de support d’OfficeSpace Software](mailto:support@officespacesoftware.com) pour obtenir ces valeurs. 

4. Votre application OfficeSpace Software attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. La capture d’écran suivante montre un exemple :
    
    ![Configurer l’authentification unique](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_03.png)

5. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, sélectionnez **user.mail** en tant **qu’identificateur d’utilisateur**, et pour chaque ligne indiquée dans le tableau ci-dessous, procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | --- | --- |    
    | email | user.mail |
    | name | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configurer l’authentification unique](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_04.png)

    ![Configurer l’authentification unique](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_05.png)
    
    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.
    
    d. Cliquez sur **OK**.

6. Dans la section **Certificat de signature SAML**, cliquez sur **Créer un certificat**.

    ![Configurer l’authentification unique](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_06.png)     

7. Dans la boîte de dialogue **Créer un certificat**, cliquez sur l’icône de calendrier et sélectionnez une **date d’expiration**. Puis cliquez sur le bouton **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-officespace-tutorial/tutorial_general_300.png)

8. Dans la section **Certificat de signature SAML**, sélectionnez **Activer le nouveau certificat** et cliquez sur **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_07.png)

9. Dans la fenêtre contextuelle **Certificat de substitution**, cliquez sur **OK**.

    ![Configurer l’authentification unique](./media/active-directory-saas-officespace-tutorial/tutorial_general_400.png)

10. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_08.png) 

11. Dans **Configuration de OfficeSpace Software**, cliquez sur **Configurer OfficeSpace Software** pour ouvrir la fenêtre **Configurer l’authentification**.

    ![Configurer l’authentification unique](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_09.png) 

    ![Configurer l’authentification unique](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_10.png)

12. Dans une autre fenêtre de navigateur web, connectez-vous à votre locataire OfficeSpace Software en tant qu’administrateur.

13. Accédez à **Paramètres** et cliquez sur **Connecteurs**.

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_002.png)

14. Cliquez sur **SAML Authorization**.

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_003.png)

15. Dans la section **SAML Authentication** , procédez comme suit :

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_004.png)

    a. Dans la zone de texte **URL du fournisseur de déconnexion**, copiez la valeur **URL du service de déconnexion** indiquée dans la fenêtre Configuration de l’application Azure AD.

    b. Dans la zone de texte **URL cible de l’IdP client**, copiez la valeur de **URL du service d’authentification unique SAML** indiquée dans la fenêtre Configuration de l’application Azure AD.

    c. Copiez la valeur **Empreinte** du certificat téléchargé, puis collez-la dans la zone de texte **Client idp cert fingerprint**. 

    d. Cliquez sur **Save Settings**.

    > [!NOTE]
    > Pour plus d’informations, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI) 
  

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail de gestion Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-officespace-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, tapez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse de messagerie** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**. 



### <a name="creating-an-officespace-software-test-user"></a>Créer un utilisateur de test OfficeSpace Software

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans OfficeSpace Software. OfficeSpace Software prend en charge l’approvisionnement juste-à-temps, option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à OfficeSpace Software s’il n’existe pas déjà.

> [!NOTE]
> Si vous devez créer un utilisateur manuellement, contactez [l’équipe de support d’OfficeSpace Software](mailto:support@officespacesoftware.com).


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à OfficeSpace Software.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à OfficeSpace Software, procédez comme suit :**

1. Dans le Portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires, allez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **OfficeSpace Software**.

    ![Configurer l’authentification unique](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_50.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    


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

[100]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_203.png
