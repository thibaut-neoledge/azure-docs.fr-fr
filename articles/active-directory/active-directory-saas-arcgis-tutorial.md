<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory à ArcGIS | Microsoft Azure" 
    description="Apprenez à utiliser ArcGIS avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
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

#Didacticiel : Intégration d’Azure Active Directory à ArcGIS

L’objectif de ce didacticiel est de montrer comment intégrer Azure et ArcGIS. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement ArcGIS pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à ArcGIS pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise ArcGIS (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour ArcGIS
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-arcgis-tutorial/IC784735.png "Scénario")
##Activation de l’intégration d’applications pour ArcGIS

Cette section décrit l’activation de l’intégration d’applications pour ArcGIS.

###Pour activer l’intégration d’applications pour ArcGIS, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-arcgis-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-arcgis-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-arcgis-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-arcgis-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **ArcGIS**.

    ![Galerie d’applications](./media/active-directory-saas-arcgis-tutorial/IC784736.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **ArcGIS**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![ArcGIS](./media/active-directory-saas-arcgis-tutorial/IC784737.png "ArcGIS")
##Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur ArcGIS avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, puis dans la page d’intégration d’applications **ArcGIS**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-arcgis-tutorial/IC784738.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à ArcGIS**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-arcgis-tutorial/IC784739.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion**, tapez l’URL utilisée par vos utilisateurs pour se connecter à l’aide du modèle « **https://company.maps.arcgis.com*", puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-arcgis-tutorial/IC784740.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur ArcGIS**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées en local sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-arcgis-tutorial/IC784741.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise ArcGIS en tant qu’administrateur.

6.  Cliquez sur **Edit Settings**.

    ![Modifier les paramètres](./media/active-directory-saas-arcgis-tutorial/IC784742.png "Modifier les paramètres")

7.  Cliquez sur **Security**.

    ![Sécurité](./media/active-directory-saas-arcgis-tutorial/IC784743.png "Sécurité")

8.  Sous **Enterprise Logins**, cliquez sur **Set Identity Provider**.

    ![Connexions de l’entreprise](./media/active-directory-saas-arcgis-tutorial/IC784744.png "Connexions de l’entreprise")

9.  Dans la page de configuration **Set Identity Provider**, procédez comme suit :

    ![Définir le fournisseur d’identité](./media/active-directory-saas-arcgis-tutorial/IC784745.png "Définir le fournisseur d’identité")

    1.  Dans la zone de texte Nom, tapez le nom de votre organisation.
    2.  Pour **Metadata for the Enterprise Identity Provider will be supplied using**, sélectionnez **A File**.
    3.  Pour télécharger votre fichier de métadonnées, cliquez sur **Choose file**.
    4.  Cliquez sur **Set Identity Provider**.

10. Dans le portail Azure Active Directory, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-arcgis-tutorial/IC784746.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à ArcGIS, vous devez les approvisionner dans ArcGIS. En l’occurrence, cet approvisionnement est une tâche manuelle.

###Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1.  Connectez-vous à votre locataire **ArcGIS**.

2.  Cliquez sur **Invite Members**.

    ![Inviter des membres](./media/active-directory-saas-arcgis-tutorial/IC784747.png "Inviter des membres")

3.  Sélectionnez **Add members automatically without sending an email**, puis cliquez sur **Next**.

    ![Ajouter des membres automatiquement](./media/active-directory-saas-arcgis-tutorial/IC784748.png "Ajouter des membres automatiquement")

4.  Dans la page de boîte de dialogue **Members**, procédez comme suit :

    ![Ajouter et vérifier](./media/active-directory-saas-arcgis-tutorial/IC784749.png "Ajouter et vérifier")

    1.  Entrez le **prénom**, le **nom** et l’**adresse de messagerie** d’un compte AAD valide que vous voulez approvisionner
    2.  Cliquez sur **Add And Review**.

5.  Passez en revue les données que vous avez entrées, puis cliquez sur **Add Members**.

    ![Ajouter un membre](./media/active-directory-saas-arcgis-tutorial/IC784750.png "Ajouter un membre")

>[AZURE.NOTE]Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par ArcGIS pour approvisionner des comptes d’utilisateur Azure Active Directory.

##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à ArcGIS, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’applications **ArcGIS**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-arcgis-tutorial/IC784751.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-arcgis-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->