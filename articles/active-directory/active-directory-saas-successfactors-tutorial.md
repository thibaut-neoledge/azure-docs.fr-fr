---
title: "Didacticiel : Intégration d’Azure Active Directory à SuccessFactors | Microsoft Docs"
description: "Découvrez comment utiliser SuccessFactors avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0837cb33bf438fb7fd9665d21d411f0170cdd393
ms.openlocfilehash: a0a101c34ffd4adb6816d9db8313d10b0ffbb3ae
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Didacticiel : Intégration d’Azure Active Directory à SuccessFactors
L’objectif de ce didacticiel est de vous montrer comment intégrer SuccessFactors dans Azure Active Directory (Azure AD).

L’intégration de SuccessFactors dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à SuccessFactors.
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à SuccessFactors (via l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD à SuccessFactors, vous avez besoin des éléments suivants :

* Un abonnement Azure valide
* Un locataire SuccessFactors

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.
> 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SuccessFactors à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-successfactors-from-the-gallery"></a>Ajout de SuccessFactors à partir de la galerie
Pour configurer l’intégration de SuccessFactors à Azure AD, vous devez ajouter SuccessFactors à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter SuccessFactors à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
    ![Configuration de l'authentification unique][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Configuration de l'authentification unique][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Configuration de l'authentification unique][4]
6. Dans la **zone de recherche**, tapez **SuccessFactors**.
   
    ![Configuration de l'authentification unique][5]
7. Dans le volet des résultats, sélectionnez **SuccessFactors**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Configuration de l'authentification unique][6]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec SuccessFactors avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SuccessFactors équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur SuccessFactors associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans SuccessFactors.

Pour configurer et tester l’authentification unique Azure AD avec SuccessFactors, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test SuccessFactors](#creating-a-successfactors-test-user)** pour avoir un équivalent de Britta Simon dans SuccessFactors lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application SuccessFactors.

**Pour configurer l’authentification unique Azure AD avec SuccessFactors, procédez comme suit :**

1. Sur la page d’intégration d’application **SuccessFactors** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configuration de l'authentification unique][7]
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à SuccessFactors**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configuration de l'authentification unique][8]
3. Dans la page **Configurer l’URL de l’application**, procédez comme suit, puis cliquez sur **Suivant**.
   
    ![Configuration de l'authentification unique][9]
   
    a. Dans la zone de texte **URL de connexion** , tapez une URL en respectant l’un des formats suivants : 
   
    |  |
    | --- |
    | `https://<company name>.successfactors.com/<company name>` |
    | `https://<company name>.sapsf.com/<company name>` |
    | `https://<company name>.successfactors.eu/<company name>` |
    | `https://<company name>.sapsf.eu` |
   
    b. Dans la zone de texte **URL de réponse** , tapez une URL en respectant l’un des formats suivants : 
   
    |  |
    | --- |
    | `https://<company name>.successfactors.com/<company name>` |
    | `https://<company name>.sapsf.com/<company name>` |
    | `https://<company name>.successfactors.eu/<company name>` |
    | `https://<company name>.sapsf.eu` |
    | `https://<company name>.sapsf.eu/<company name>` |
   
    c. Cliquez sur **Suivant**. 

    > [!NOTE]
    > Notez qu’il ne s’agit pas des valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL de connexion et l’URL de réponse réelles. Pour obtenir ces valeurs, contactez [l’équipe du support technique SuccessFactors](https://www.successfactors.com/en_us/support.html).

1. Dans la page **Configurer l’authentification unique sur SuccessFactors**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.
   
    ![Configuration de l'authentification unique][10]

2. Dans une autre fenêtre de navigateur web, connectez-vous à votre **portail d’administration SuccessFactors** en tant qu’administrateur.

3. Dans **Application Security** (Sécurité des applications), accédez à la **fonctionnalité d’authentification unique**. 

4. Indiquez une valeur dans le champ **Reset Token** (Réinitialiser le jeton), puis cliquez sur **Save Token** (Enregistrer le jeton) pour activer l’authentification unique SAML.
   
    ![Configuration de l’authentification unique côté application][11]

    > [!NOTE] 
    > Cette valeur est uniquement utilisée en tant que commutateur marche/arrêt. Si une valeur est enregistrée, l’authentification unique SAML est activée. Si aucune valeur n’est enregistrée, l’authentification unique SAML est désactivée.

1. Accédez à la capture d’écran ci-dessous et effectuez les actions suivantes.
   
    ![Configuration de l’authentification unique côté application][12]
   
    a. Sélectionnez la case d’option **SAML v2 SSO** .
   
    b. Définissez le nom de partie de confiance SAML (par exemple, émetteur SAML + nom de l’entreprise).
   
    c. Dans la zone de texte **SAML Issuer** (Émetteur SAML), copiez la valeur de **l’URL de l’émetteur** indiquée dans l’Assistant Configuration de l’application Azure AD.
   
    d. Sélectionnez **Response(Customer Generated/IdP/AP)** (Réponse (générée par le client/fournisseur d’identité/point d’accès)) pour **Require Mandatory Signature** (Exiger une signature obligatoire).
   
    e. Sélectionnez **Enabled** (Activé) dans le champ **Enable SAML Flag** (Activer l’indicateur SAML).
   
    f. Sélectionnez **No** (Non) dans le champ **Login Request Signature(SF Generated/SP/RP)** (Signature de la demande de connexion (générée par SF/fournisseur de services/fournisseur de ressources)).
   
    g. Sélectionnez **Browser/Post Profile** (Navigateur/Post-profilage) en tant que **profil SAML**.
   
    h. Sélectionnez **No** (Non) dans le champ **Enforce Certificate Valid Period** (Appliquer la période valide du certificat).
   
    i. Copiez le contenu du fichier de certificat téléchargé, puis collez-le dans la zone de texte **SAML Verifying Certificate** (Certificat de vérification SAML).

    > [!NOTE] 
    > Le contenu du certificat doit comporter des balises de début et de fin de certificat.

1. Accédez à SAML V2, puis procédez comme suit :
   
    ![Configuration de l’authentification unique côté application][13]
   
    a. Sélectionnez **Yes** (Oui) dans **Support SP-initiated Global Logout** (Prendre en charge la déconnexion globale initiée par le fournisseur de services).
   
    b. Dans la zone de texte **Global Logout Service URL (LogoutRequest destination)** (URL de service de déconnexion globale (destination LogoutRequest)), copiez la valeur de **l’URL de déconnexion distante** indiquée dans l’Assistant Configuration de l’application Azure AD.
   
    c. Sélectionnez **No** (Non) dans **Require sp must encrypt all NameID element** (Exiger que le fournisseur de services chiffre tous les éléments NameID).
   
    d. Sélectionnez **unspecified** (non spécifié) dans **NameID Format** (Format NameID).
   
    e. Sélectionnez **Yes** (Oui) dans **Enable sp initiated login (AuthnRequest)** (Activer la connexion initiée par le fournisseur de services (AuthnRequest)).
   
    f. Dans la zone de texte **Send request as Company-Wide issuer** (Envoyer la demande en tant qu’émetteur au niveau de l’entreprise), copiez la valeur de **l’URL de connexion distante** indiquée dans l’Assistant Configuration de l’application Azure AD.
2. Procédez comme suit si vous souhaitez que les noms d’utilisateur de connexion ne respectent pas la casse :
   
    a. Accédez à **Company Settings** (Paramètres de l’entreprise) (en bas).
   
    b. Sélectionnez la case à cocher près de **Enable Non-Case-Sensitive Username**(Activer le non-respect de la casse pour les noms d’utilisateur).
   
    c. Cliquez sur **Enregistrer**.
   
    ![Configurer l’authentification unique][29]

    > [!NOTE] 
    > Si vous essayez d’activer cette option, le système vérifie si un nom de connexion SAML est créé en double. Par exemple, si le client possède les noms d’utilisateur User1 et user1. Si vous désactivez le respect de la casse, ces noms deviennent des doublons. Le système vous transmettra un message d’erreur et n’activera pas la fonctionnalité. Le client devra modifier l’un des noms d’utilisateur afin qu’il soit orthographié différemment. 

1. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Applications][14]
2. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
   
    ![Applications][15]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail classique.

![Créer un utilisateur Azure AD][16]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD][17]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD][18]
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD][19]
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD][20]
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD][21]
   
    a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
    b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
    e. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD][22]
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD][23]
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.  

### <a name="creating-a-successfactors-test-user"></a>Création d’un utilisateur de test SuccessFactors
Pour se connecter à SuccessFactors, les utilisateurs d’Azure AD doivent être approvisionnés dans SuccessFactors.  
Dans le cas de SuccessFactors, l’approvisionnement est une tâche manuelle.

Pour créer des utilisateurs dans SuccessFactors, vous devez contacter [l’équipe de support technique SuccessFactors](https://www.successfactors.com/en_us/support.html).

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à SuccessFactors.

![Affecter des utilisateurs][24]

**Pour affecter Britta Simon à SuccessFactors, procédez comme suit :**

1. Pour ouvrir l’affichage des applications dans le portail Classic, dans l’affichage de l’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][25]
2. Dans la liste des applications, sélectionnez **SuccessFactors**.
   
    ![Configurer l’authentification unique][26]
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][27]
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][28]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque SuccessFactors dans le volet d’accès, vous devez être connecté automatiquement à votre application SuccessFactors.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_00.png
[1]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_01.png
[6]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_02.png
[7]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_03.png
[8]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_04.png
[9]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_05.png
[10]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_06.png

[11]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_09.png
[14]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_05.png
[15]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_06.png

[16]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_00.png
[17]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_01.png
[18]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_02.png
[19]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_03.png
[20]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_04.png
[21]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_05.png
[22]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_06.png
[23]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_07.png

[24]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_07.png
[25]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_08.png
[26]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_11.png
[27]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_09.png
[28]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_10.png
[29]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_10.png

