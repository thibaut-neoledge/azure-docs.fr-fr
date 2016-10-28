<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Huddle | Microsoft Azure" 
    description="Apprenez à utiliser Huddle avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="07/09/2016" 
    ms.author="jeedes" />

#Didacticiel : Intégration d’Azure Active Directory avec Huddle
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Huddle. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Huddle pour lequel l’authentification unique est activée
  
À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Huddle pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Huddle (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour Huddle
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Configurer l’authentification unique](./media/active-directory-saas-huddle-tutorial/IC787830.png "Configurer l’authentification unique")
##Activation de l’intégration d’application pour Huddle
  
Cette section décrit l’activation de l’intégration d’application pour Huddle.

###Pour activer l’intégration d’application pour Huddle, procédez comme suit :

1.  Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-huddle-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-huddle-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-huddle-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-huddle-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **Huddle**.

    ![Galerie d’applications](./media/active-directory-saas-huddle-tutorial/IC787831.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **Huddle**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Huddle](./media/active-directory-saas-huddle-tutorial/IC787832.png "Huddle")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Huddle avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Sur la page d’intégration d’applications **Huddle** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-huddle-tutorial/IC787833.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Huddle**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-huddle-tutorial/IC787834.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion de Huddle**, tapez l’URL de votre locataire Huddle selon le modèle suivant « *http://company.huddle.com*", puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-huddle-tutorial/IC787835.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Huddle**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-huddle-tutorial/IC787836.png "Configurer l’authentification unique")

    1.  Cliquez sur **Télécharger le certificat**, puis enregistrez le certificat sur votre ordinateur.
    2.  Copiez la valeur **URL de l’émetteur**, la valeur **URL SSO SAML** et le certificat téléchargé, puis envoyez-les à l’équipe du support technique Huddle.

    >[AZURE.NOTE] L’authentification unique doit être activée par l’équipe du support technique Huddle. Vous recevrez une notification dès la configuration terminée.

5.  Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-huddle-tutorial/IC787837.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Pour se connecter à Huddle, les utilisateurs d’Azure AD doivent être approvisionnés dans Huddle. Dans le cas de Huddle, l’approvisionnement est une tâche manuelle.

###Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **Huddle** en tant qu’administrateur.

2.  Cliquez sur **Workspace**.

3.  Cliquez sur **People > Invite People**.

    ![Personnes](./media/active-directory-saas-huddle-tutorial/IC787838.png "Personnes")

4.  Dans la section **Create a new invitation**, procédez comme suit :

    ![Nouvelle invitation](./media/active-directory-saas-huddle-tutorial/IC787839.png "Nouvelle invitation")

    1.  Dans la liste **Choose a team to invite people to join**, sélectionnez **team**.
    2.  Indiquez le l’**adresse de messagerie** d’un compte Azure Active Directory valide que vous souhaitez approvisionner dans la zone de texte correspondante.
    3.  Cliquez sur **Invite**.

    >[AZURE.NOTE] Le titulaire du compte Azure AD reçoit alors un message électronique contenant un lien pour confirmer le compte avant qu’il ne soit activé.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur Huddle fourni par ce site pour approvisionner des comptes d’utilisateurs AAD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Huddle, procédez comme suit :

1.  Dans le portail Azure Classic, créez un compte de test.

2.  Dans la page d’intégration d’application **Huddle**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-huddle-tutorial/IC787840.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-huddle-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->