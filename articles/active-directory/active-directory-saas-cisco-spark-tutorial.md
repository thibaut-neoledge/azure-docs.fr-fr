---
title: "Didacticiel : Intégration d’Azure Active Directory avec Cisco Spark | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory Cisco Spark."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 931004d458fd0cbf3e002805ca701d777ddc57c2
ms.openlocfilehash: f0f12b1667b7f45fd164fac658502c93e8afb855
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>Didacticiel : Intégration d’Azure Active Directory avec Cisco Spark
Ce didacticiel explique comment intégrer Cisco Spark avec Azure Active Directory (Azure AD).

L’intégration de Cisco Spark avec Azure AD offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Cisco Spark.
* Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Cisco Spark via l’authentification unique avec leur compte Azure AD.
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD avec Cisco Spark, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement **Cisco Spark** pour lequel l’authentification unique est activée

>[!NOTE]
>Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production. 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir [un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Cisco Spark à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="add-cisco-spark-from-the-gallery"></a>Ajouter Cisco Spark à partir de la galerie
Pour configurer l’intégration de Cisco Spark à Azure AD, vous devez ajouter Cisco Spark, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Cisco Spark à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, tapez **Cisco Spark**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_01.png)
7. Dans le volet de résultats, sélectionnez **Cisco Spark**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Cisco Spark sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Cisco Spark équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et l’utilisateur associé dans Cisco Spark.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Username** dans Cisco Spark. Pour configurer et tester l’authentification unique Azure AD avec Cisco Spark, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Cisco Spark](#creating-a-cisco-spark-test-user)** pour avoir un équivalent de Britta Simon dans Cisco Spark, lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application Cisco Spark.

L’application Cisco Spark s’attend à ce que les assertions SAML contiennent des attributs spécifiques. Configurez les attributs suivants pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de l’onglet **Attributs** de l’application. La capture d’écran suivante montre un exemple :

![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_03.png) 

**Pour configurer l’authentification unique Azure AD avec Cisco Spark, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’application **Cisco Spark**, dans le menu situé en haut, cliquez sur **Attributs**.
   
    ![Configurer l’authentification unique][5]
2. Dans la boîte de dialogue **Attributs du jeton SAML** , procédez comme suit :
  1. Cliquez sur **ajouter un attribut utilisateur** pour ouvrir la boîte de dialogue **Ajouter un attribut utilisateur**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_05.png)
  2. Dans la zone de texte **Nom de l’attribut**, entrez **uid**.
  3. Dans la liste **Valeur d’attribut**, sélectionnez **user.userprincipal**.
  4. Cliquez sur **Terminé**. Puis, cliquez sur **Enregistrer les modifications** en bas de la page.
3. Dans le menu situé en haut, cliquez sur **Démarrage rapide**.
   
    ![Configurer l’authentification unique][6]
4. Dans le portail Classic, dans la page d’intégration d’applications **Cisco Spark**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][7] 
5. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Cisco Spark**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_06.png)
6. Sur la page **Configurer les paramètres d’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_07.png)
  1. Dans la zone de texte URL de connexion, tapez une URL au format suivant : `https://web.ciscospark.com/#/signin`.
  2. Cliquez sur **Suivant**.
7. Dans la page **Configurer l’authentification unique sur Cisco Spark**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_09.png)
8. Connectez-vous à [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) (Gestion de la collaboration dans le cloud Cisco) avec vos informations d’identification d’administrateur complètes.
9. Sélectionnez **Settings** (Paramètres), puis, dans la section **Authentication** (Authentification), cliquez sur **Modify** (Modifier).
   
    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_10.png)
9. Sélectionnez **intégrer un fournisseur d’identité tiers. (Avancé)** et accédez à l’écran suivant.
10. Cliquez sur **Download Metadata File** (Télécharger le fichier de métadonnées), puis enregistrez le fichier sur votre ordinateur.
11. Dans la page **Import Idp Metadata** -(Importer les métadonnées Idp), glissez-déposez le fichier de métadonnées Azure AD sur la page, ou utilisez l’option d’explorateur de fichiers pour localiser et charger le fichier de métadonnées Azure AD. Ensuite, sélectionnez **Require certificate signed by a certificate authority in Metadata (more secure)** (Exiger un certificat signé par une autorité de certification dans les métadonnées (plus sûr)), puis cliquez sur **Next** (Suivant). 
   
   ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_11.png)
12. Sélectionnez **Test SSO Connection** (Tester la connexion SSO), puis, quand un nouvel onglet de navigateur s’ouvre, et authentifiez-vous auprès d’Azure AD en vous connectant.
13. Revenez à l’onglet du navigateur **Cisco Cloud Collaboration Management** (Gestion de la collaboration dans le cloud Cisco). Si le test a réussi, sélectionnez **Ce test a réussi. option Activer l’authentification unique** et cliquez sur **Suivant**.
14. Dans le portail Azure AD Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
15. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
    
     ![Authentification unique Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_09.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_05.png) 
  1. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
  2. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
  3. Cliquez sur **Suivant**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_06.png) 
  1. Dans la zone de texte **First Name**, tapez **Britta**.  
  2. Dans la zone de texte **Last Name**, tapez **Simon**.
  3. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
  4. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
  5. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_08.png) 
  1. Notez la valeur du **Nouveau mot de passe**.
  2. Cliquez sur **Terminé**.   

### <a name="create-a-cisco-spark-test-user"></a>Créer un utilisateur de test Cisco Spark
Dans cette section, vous allez créer un utilisateur nommé Britta Simon dans Cisco Spark. Dans cette section, vous allez créer un utilisateur nommé Britta Simon dans Cisco Spark.

1. Accédez à [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) (Gestion de la collaboration dans le cloud Cisco) avec vos informations d’identification d’administrateur complètes.
2. Cliquez sur **Users** (Utilisateurs), puis sur **Manage Users** (Gérer les utilisateurs).
   
    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_12.png) 
3. Dans la fenêtre **Manage User** (Gérer l’utilisateur), sélectionnez **Manually add or modify users** (Ajouter ou modifier manuellement des utilisateurs), puis cliquez sur **Next** (Suivant).
4. Sélectionnez **Names and Email address** (Noms et adresse de messagerie). Ensuite, complétez la zone de texte comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_13.png) 
  1. Dans la zone de texte **First Name**, tapez **Britta**. 
  2. Dans la zone de texte **Last Name**, tapez **Simon**.
  3. Dans la zone de texte **Adresse électronique**, tapez **britta.simon@contoso.com**.
5. Cliquez sur le signe plus pour ajouter Britta Simon. Cliquez ensuite sur **Suivant**.
6. Dans la fenêtre **Add Services for Users** (Ajouter des services pour les utilisateurs), cliquez sur **Save** (Enregistrer), puis sur **Finish** (Terminer).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD
Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Cisco Spark.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Cisco Spark, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue d’annuaire, cliquez sur l’option **Applications** figurant dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **Cisco Spark**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_14.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203] 
4. Dans la liste Tous les utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="test-single-sign-on"></a>Tester l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Cisco Spark dans le volet d’accès, vous devez être authentifié automatiquement auprès de votre application Cisco Spark.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_205.png

