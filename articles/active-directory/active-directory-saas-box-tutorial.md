<properties 
    pageTitle="Didacticiel : Intégration d'Azure Active Directory à Box | Microsoft Azure" 
    description="Apprenez à utiliser Box avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore." 
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




#Didacticiel : Intégration d'Azure Active Directory à Box


  
L'objectif de ce didacticiel est de montrer comment intégrer Azure et Box. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire de test dans Box
  
À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Box pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Box (connexion initiée par le prestataire de service) ou à l’aide de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l'intégration d'application pour Box
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs et des groupes
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-box-tutorial/IC769537.png "Scénario")



##Activation de l'intégration d'application pour Box
  
Cette section décrit l'activation de l'intégration d'application pour Box.

###Pour activer l'intégration d'application pour Box, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-box-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-box-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-box-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-box-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Box**.

    ![Galerie d’applications](./media/active-directory-saas-box-tutorial/IC701023.png "Galerie d'applications")

7.  Dans le volet de résultats, sélectionnez **Box**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Box](./media/active-directory-saas-box-tutorial/IC701024.png "Box")



##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s'authentifier sur Box avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. <br> Dans le cadre de cette procédure, vous devez charger un certificat vers Box.com.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, dans la page d’intégration d’applications **Box**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-box-tutorial/IC769538.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Box**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-box-tutorial/IC769539.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de locataire Box**, tapez l’URL de votre locataire Box (par exemple : https://<mydomainname>.box.com), puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-box-tutorial/IC669826.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Box**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de données localement sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-box-tutorial/IC669824.png "Configurer l’authentification unique")

5.  Transférez ce fichier de métadonnées à l'équipe de support Box. L’équipe de support en a besoin pour configurer l’authentification unique pour vous.

6.  Sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-box-tutorial/IC769540.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Cette section décrit comment activer l'approvisionnement des comptes d'utilisateurs Active Directory sur Box.

###Pour configurer l’authentification unique, procédez comme suit :

1. Dans le portail de gestion Azure, dans la page d’intégration d’application **Box**, cliquez sur **Configurer l’approvisionnement d’utilisateurs** pour ouvrir la boîte de dialogue **Configurer l’approvisionnement d’utilisateurs**. <br> <br> ![Activer l'approvisionnement de l'utilisateur automatique.](./media/active-directory-saas-box-tutorial/IC769541.png "Activer l'approvisionnement de l'utilisateur automatique.")

2. Dans la boîte de dialogue **Activer l’approvisionnement d’utilisateurs sur Box**, cliquez sur **Activer l’approvisionnement d’utilisateurs**. <br><br> ![Activer l'approvisionnement de l'utilisateur automatique.](./media/active-directory-saas-box-tutorial/IC769544.png "Activer l'approvisionnement de l'utilisateur automatique.")

3. Sur la page **Se connecter pour autoriser l’accès à Box**, saisissez les informations d’identification requises, puis cliquez sur **Autoriser**. <br><br> ![Activer l'approvisionnement de l'utilisateur automatique.](./media/active-directory-saas-box-tutorial/IC769546.png "Activer l'approvisionnement de l'utilisateur automatique.")


4. Cliquez sur **Accorder l’accès à Box** pour autoriser cette opération et retourner au portail de gestion Azure. <br><br> ![Activer l'approvisionnement de l'utilisateur automatique.](./media/active-directory-saas-box-tutorial/IC769549.png "Activer l'approvisionnement de l'utilisateur automatique.")


5. Sur la page **Options d'approvisionnement**, les cases à cocher **Types d'objet à approvisionner** permettent de choisir ou non les objets de groupe approvisionnés dans Box en plus des objets utilisateur. Pour plus d'informations, voir "Affectation d'utilisateurs et de groupes" ci-dessous.


6. Pour terminer la configuration, cliquez sur le bouton Terminé. <br><br> ![Activer l'approvisionnement de l'utilisateur automatique.](./media/active-directory-saas-box-tutorial/IC769551.png "Activer l'approvisionnement de l'utilisateur automatique.")



##Affectation d'un utilisateur de test
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Box, procédez comme suit :

1. Dans le portail Azure AD, créez un compte test.

2. Dans la page d’intégration d’applications **Box **, cliquez sur **Affecter des utilisateurs**. <br><br> ![Affecter des utilisateurs](./media/active-directory-saas-box-tutorial/IC769552.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation. <br><br> ![Oui](./media/active-directory-saas-box-tutorial/IC767830.png "Oui")
  
À présent, patientez 10 minutes et vérifiez que le compte est bien synchronisé avec Box.

Une première étape de vérification consiste à contrôler l'état d'approvisionnement en cliquant sur le tableau de bord dans la page d'intégration d'applications Box dans le Portail de gestion Azure.

<br><br> ![Tableau de bord](./media/active-directory-saas-box-tutorial/IC769553.png "Tableau de bord")

Si le cycle d’approvisionnement d’utilisateur a abouti, l’état associé suivant est indiqué :

<br><br> ![Statut d’intégration](./media/active-directory-saas-box-tutorial/IC769555.png "Statut d’intégration")


Dans votre locataire Box, les utilisateurs synchronisés sont répertoriés sous **Utilisateurs gérés** dans la **Console d’administration**.

<br><br> ![Statut d’intégration](./media/active-directory-saas-box-tutorial/IC769556.png "Statut d’intégration")


##Affectation d'utilisateurs et de groupes

L'onglet **Box > Utilisateurs et groupes** du portail Azure Classic vous permet de spécifier quels utilisateurs et groupes doivent avoir accès à Box. L'affectation d'un utilisateur ou groupe entraîne les événements suivants :

* Azure AD permet à l'utilisateur affecté (par affectation directe ou appartenance au groupe) de s'identifier auprès de Box. Si un utilisateur n'est pas affecté, Azure AD ne lui permettra pas de se connecter à Box et renverra une erreur sur la page de connexion Azure AD.

* Une mosaïque d'application pour Box est ajoutée au [lanceur d'applications](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) de cet utilisateur.

* Si la configuration automatique est activée, les utilisateurs et/ou groupes affectés sont ajoutés à la file d'attente d'approvisionnement automatique.

    * Si seuls des objets utilisateur ont été configurés pour être approvisionnés, tous les utilisateurs directement affectées sont placés dans la file d'attente d'approvisionnement et tous les utilisateurs membres des groupes affectés seront placés dans la file d'attente d'approvisionnement. 
    
    * Si des objets de groupe ont été configurés pour être approvisionnés, tous les objets du groupe affectés sont approvisionnés dans Box, tout comme l'ensemble des utilisateurs membres de ces groupes. Les appartenances des groupes et des utilisateurs sont conservées lors de l'écriture dans Box.
    
Vous pouvez utiliser l'onglet **Attributs > Authentification unique** pour configurer les attributs (ou revendications) de l'utilisateur présentées dans Box pendant l'authentification SAML, et l'onglet **Attributs > Approvisionnement** pour configurer le transfert des attributs utilisateur et groupe d'Azure AD vers Box lors des opérations d'approvisionnement. Consultez les ressources ci-après pour plus d'informations.


## Ressources supplémentaires

* [Personnalisation des revendications émises dans le jeton SAML](active-directory-saml-claims-customization.md)
* [Approvisionnement : personnaliser les mappages d'attributs](active-directory-saas-customizing-attribute-mappings.md)
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!---HONumber=AcomDC_0406_2016-->