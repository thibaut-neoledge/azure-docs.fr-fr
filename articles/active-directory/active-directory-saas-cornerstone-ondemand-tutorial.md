<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory à Cornerstone OnDemand | Microsoft Azure" 
    description="Apprenez à utiliser Cornerstone OnDemand avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#Didacticiel : Intégration d’Azure Active Directory à Cornerstone OnDemand

L’objectif de ce didacticiel est de montrer comment intégrer Azure et Cornerstone OnDemand. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un client Cornerstone OnDemand

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Cornerstone OnDemand pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Cornerstone OnDemand (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration de l’application pour Cornerstone OnDemand
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781593.png "Scénario")
##Activation de l’intégration de l’application pour Cornerstone OnDemand

Cette section décrit l’activation de l’intégration d’applications pour Cornerstone OnDemand.

###Pour activer l’intégration d’applications pour Cornerstone OnDemand, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **cornerstone ondemand**.

    ![Galerie d’applications](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781594.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **Cornerstone OnDemand**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Cornerstone OnDemand](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781595.png "Cornerstone OnDemand")
##Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Cornerstone OnDemand avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, dans la page d’intégration d’applications **Cornerstone OnDemand**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![activation de l'authentification unique](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781596.png "activation de l'authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Cornerstone OnDemand**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Authentification unique avec Microsoft Azure AD](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781597.png "Authentification unique avec Microsoft Azure AD")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion de Cornerstone OnDemand**, tapez votre URL selon le modèle « **http://company.csod.com*", puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781598.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Cornerstone OnDemand**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781599.png "Configurer l’authentification unique")

5.  Envoyez les éléments suivants à l’équipe du support technique Cornerstone OnDemand :

    1.  Certificat téléchargé
    2.  Valeur **URL de connexion distante**
    3.  Valeur **URL de déconnexion distante**

    >[AZURE.NOTE]L’authentification unique doit être configurée par l’équipe du support technique Cornerstone OnDemand. Vous recevrez une notification de la part de l’équipe du support technique une fois la configuration terminée.

6.  Sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781600.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs

Pour se connecter à Cornerstone OnDemand, les utilisateurs d’Azure AD doivent être approvisionnés dans Cornerstone OnDemand. Dans le cas de Cornerstone OnDemand, l’approvisionnement est une tâche manuelle.

###Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1.  Envoyez les informations sur l’utilisateur Azure AD que vous souhaitez approvisionner (par exemple : nom, adresse e-mail) à l’équipe du support technique Cornerstone OnDemand.

>[AZURE.NOTE]Vous pouvez utiliser tout autre outil ou API de création de compte d’utilisateur fourni par Cornerstone OnDemand pour approvisionner des comptes d’utilisateurs AAD.

##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Cornerstone OnDemand, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’applications **Cornerstone OnDemand**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC775564.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Affecter des utilisateurs](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781601.png "Affecter des utilisateurs")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->