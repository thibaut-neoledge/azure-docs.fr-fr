---
title: "Tutoriel : Intégration d’Azure Active Directory à LinkedIn Lookup | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et LinkedIn Lookup."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a2757a39-1ead-4a3e-91e4-270be3055683
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: f343d8dbbd80de1a50b625e949377aa7c48d0cae
ms.contentlocale: fr-fr
ms.lasthandoff: 04/29/2017


---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-lookup"></a>Tutoriel : Intégration d’Azure Active Directory à LinkedIn Lookup

Dans ce tutoriel, vous allez apprendre à intégrer LinkedIn Lookup à Azure Active Directory (Azure AD).

L’intégration de LinkedIn Lookup à Azure AD vous fait bénéficier des avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à LinkedIn Lookup
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à LinkedIn Lookup (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes depuis un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à LinkedIn Lookup, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement LinkedIn Lookup pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de LinkedIn Lookup à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-linkedin-lookup-from-the-gallery"></a>Ajout de LinkedIn Lookup à partir de la galerie
Pour configurer l’intégration de LinkedIn Lookup à Azure AD, vous devez ajouter LinkedIn Lookup à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter LinkedIn Lookup à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter la nouvelle application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **LinkedIn Lookup**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_search.png)

5. Dans le volet des résultats, sélectionnez **LinkedIn Lookup**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec LinkedIn Lookup, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur LinkedIn Lookup équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur LinkedIn Lookup associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans LinkedIn Lookup.

Pour configurer et tester l’authentification unique Azure AD avec LinkedIn Lookup, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l'authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test LinkedIn Lookup](#creating-an-linkedin-lookup-test-user)** pour avoir un équivalent de Britta Simon dans LinkedIn Lookup lié à la représentation Azure AD.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d'utiliser l'authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application LinkedIn Lookup.

**Pour configurer l’authentification unique Azure AD avec LinkedIn Lookup, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **LinkedIn Lookup**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_samlbase.png)

3. Dans une autre fenêtre de navigateur web, connectez-vous à votre site web **LinkedIn Lookup** en tant qu’administrateur.

4. Dans le **Centre des comptes**, cliquez sur **Paramètres globaux** sous **Paramètres**. De même, sélectionnez **Lookup** dans la liste déroulante.

    ![Configurer l’authentification unique](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_LinkedIn_admin_011.png)

5. Cliquez sur  **OU cliquez ici pour charger et copier les champs individuels du formulaire**, puis copiez l’**ID d’entité** et l’**URL ACS (Assertion Consumer Access)**.

    ![Configurer l’authentification unique](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_LinkedIn_admin_032.png)

6. Dans la section **Domaines et URL LinkedIn Lookup** du portail Azure, effectuez les étapes suivantes si vous voulez configurer l’application en mode initié par **IDP** :

    ![Configurer l’authentification unique](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_url.png)

    a. Dans la zone de texte **Identificateur**, entrez **l’ID d’entité** copié à partir de LinkedIn Portal 

    b. Dans la zone de texte **URL de réponse**, entrez **l’URL ACS** copiée à partir du portail LinkedIn

7. Cochez **Afficher les paramètres d’URL avancés** si vous souhaitez configurer l’application en mode lancé par le **fournisseur de service** :

    ![Configurer l’authentification unique](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL selon le modèle suivant : `https://www.linkedIn.com/checkpoint/enterprise/login/<AccountId>?application=lookup`
     
    > [!NOTE] 
    > Notez qu’il ne s’agit pas de la valeur réelle. L’utilisateur doit mettre à jour ces valeurs avec l’URL de connexion réelle. Contactez l’[équipe de support technique LinkedIn Lookup](https://business.LinkedIn.com/lookup) pour obtenir cette valeur.

8. Votre application **LinkedIn Lookup** attend les assertions SAML dans un format spécifique. L’utilisateur doit ajouter des mappages d’attributs personnalisés à la configuration d’attributs du jeton SAML. La capture d’écran suivante montre un exemple. La valeur par défaut pour **Identificateur d’utilisateur** est **user.userprincipalname**, mais LinkedIn Lookup s’attend à ce qu’elle soit mappée avec l’adresse de messagerie de l’utilisateur. Vous pouvez utiliser l’attribut **user.mail** de la liste ou utiliser la valeur d’attribut appropriée en fonction de la configuration de votre organisation. 

    ![Configurer l’authentification unique](./media/active-directory-saas-LinkedInlookup-tutorial/updateusermail.png)
    
9. Dans **Attributs utilisateur**, cliquez sur **Afficher et modifier tous les autres attributs utilisateur** et définissez les attributs. L’utilisateur doit ajouter une autre revendication nommée **department**, et la valeur doit être mappée avec **user.department**.

    | Nom de l'attribut | Valeur de l’attribut |
    | --- | --- |    
    | department| user.department |

       ![Creating an Azure AD test user](./media/active-directory-saas-LinkedInlookup-tutorial/userattribute.png)

       a. Click **Add attribute** to open the attribute details page add the department attribute as shown below-

       ![Creating an Azure AD test user](./media/active-directory-saas-LinkedInlookup-tutorial/adduserattribute.png)
   
       b. Click **Ok** to save the attribute.

10. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier XML sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_certificate.png) 

11. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_400.png)

12. Accédez à la section **Paramètres de l’administrateur LinkedIn**. Chargez le fichier XML que vous avez téléchargé à partir du portail Azure en cliquant sur l’option **Télécharger fichier XML**.

    ![Configurer l’authentification unique](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedIn_metadata_03.png)

13. Cliquez sur **Activer** pour activer l’authentification unique. L’état de l’authentification unique passe de **Non connecté** à **Connecté**.

    ![Configurer l’authentification unique](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedIn_admin_05.png)

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_02.png) 

3. Cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Name**, entrez **Britta Simon**.

    b. Dans la zone de texte **Nom d’utilisateur** , tapez l’**adresse de messagerie** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-an-linkedin-lookup-test-user"></a>Création d’un utilisateur de test LinkedIn Lookup

L’application LinkedIn Lookup prend en charge la configuration d’utilisateur juste à temps (JIT), et après authentification, les utilisateurs sont créés automatiquement dans l’application. Activez **Affecter automatiquement les licences** pour affecter une licence à l’utilisateur.
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedin_admin_license.png)

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à LinkedIn Lookup.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à LinkedIn Lookup, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **LinkedIn Lookup**.

    ![Configurer l’authentification unique](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la mosaïque LinkedIn Lookup dans le volet d’accès, vous êtes normalement redirigé vers la page d’organisation où vous devez fournir vos informations de compte personnel LinkedIn. Votre compte personnel est ainsi lié à votre compte professionnel LinkedIn. 

Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_203.png


