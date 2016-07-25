<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory à AirWatch | Microsoft Azure" 
    description="Apprenez à utiliser AirWatch avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore !" 
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
    ms.date="07/11/2016" 
    ms.author="jeedes" />

#Didacticiel : Intégration d’Azure Active Directory à AirWatch

L’objectif de ce didacticiel est de montrer comment intégrer Azure et AirWatch. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement AirWatch pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à AirWatch pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise AirWatch (connexion initiée par le fournisseur du service) ou en s’appuyant sur la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour AirWatch
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791913.png "AirWatch")
##Activation de l’intégration d’application pour AirWatch

Cette section décrit l’activation de l’intégration d’application pour AirWatch.

###Pour activer l’intégration d’application pour AirWatch, procédez comme suit :

1.  Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-airwatch-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-airwatch-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-airwatch-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-airwatch-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **AirWatch**.

    ![Galerie d’applications](./media/active-directory-saas-airwatch-tutorial/IC791914.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **AirWatch**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791915.png "AirWatch")
##Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur AirWatch avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure Classic, dans la page d’intégration d’application **AirWatch**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-airwatch-tutorial/IC791916.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à AirWatch**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-airwatch-tutorial/IC791917.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion AirWatch**, tapez l’URL dont se servent vos utilisateurs pour se connecter à votre application AirWatch (par exemple, « *https:// companycode.awmdm.com/AirWatch/Login?gid=companycode* »), puis cliquez sur**Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-airwatch-tutorial/IC791918.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur AirWatch**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-airwatch-tutorial/IC791919.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise AirWatch en tant qu’administrateur.

6.  Dans le volet de navigation gauche, cliquez sur **Accounts**, puis sur **Administrators**.

    ![Administrateurs](./media/active-directory-saas-airwatch-tutorial/IC791920.png "Administrateurs")

7.  Développez le menu **Settings**, puis cliquez sur **Directory Services**.

    ![Paramètres](./media/active-directory-saas-airwatch-tutorial/IC791921.png "Paramètres")

8.  Cliquez sur l’onglet **User** puis, dans le champ de texte **Base DN**, tapez votre nom de domaine et cliquez sur**Save**.

    ![Utilisateur](./media/active-directory-saas-airwatch-tutorial/IC791922.png "Utilisateur")

9.  Cliquez sur l’onglet **Server**.

    ![Serveur](./media/active-directory-saas-airwatch-tutorial/IC791923.png "Serveur")

10. Procédez comme suit :

    ![Télécharger](./media/active-directory-saas-airwatch-tutorial/IC791924.png "Télécharger")

    1.  Pour **Directory Type**, sélectionnez **None**.
    2.  Sélectionnez **Use SAML For Authentication**.
    3.  Pour charger le certificat téléchargé, cliquez sur **Upload**.

11. Dans la section **Request**, procédez comme suit :

    ![Demande](./media/active-directory-saas-airwatch-tutorial/IC791925.png "Demande")

    1.  Pour **Request Binding Type**, sélectionnez **POST**.
    2.  Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Airwatch** de la boîte de dialogue, copiez la valeur **URL du service d’authentification unique**, puis collez-la dans la zone de texte **Identity Provider Single Sign On URL**.
    3.  Pour **NameID Format**, sélectionnez **Email Address**.
    4.  Cliquez sur **Enregistrer**.

12. Cliquez à nouveau sur l’onglet **User**.

    ![Utilisateur](./media/active-directory-saas-airwatch-tutorial/IC791926.png "Utilisateur")

13. Dans la section **Attribute**, procédez comme suit :

    ![Attribut](./media/active-directory-saas-airwatch-tutorial/IC791927.png "Attribut")

    1.  Dans la zone de texte **Object Identifier**, tapez **http://schemas.microsoft.com/identity/claims/objectidentifier**.
    2.  Dans la zone de texte **Username**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    3.  Dans la zone de texte **Display Name**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    4.  Dans la zone de texte **First Name**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    5.  Dans la zone de texte **Last Name**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
    6.  Dans la zone de texte **Email**, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    7.  Cliquez sur **Enregistrer**.

14. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-airwatch-tutorial/IC791928.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs

Pour se connecter à AirWatch, les utilisateurs d'Azure AD doivent être approvisionnés dans AirWatch. Dans le cas d’AirWatch, cet approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **AirWatch** en tant qu’administrateur.

2.  Dans le volet de navigation situé sur le côté gauche, cliquez sur **Accounts**, puis sur **Users**.

    ![Utilisateurs](./media/active-directory-saas-airwatch-tutorial/IC791929.png "Utilisateurs")

3.  Dans le menu **Users**, cliquez sur **List View**, puis sur **Add > Add User**.

    ![Ajouter un utilisateur](./media/active-directory-saas-airwatch-tutorial/IC791930.png "Ajouter un utilisateur")

4.  Dans la boîte de dialogue **Add / Edit User**, procédez comme suit :

    ![Ajouter un utilisateur](./media/active-directory-saas-airwatch-tutorial/IC791931.png "Ajouter un utilisateur")

    1.  Tapez le nom d’utilisateur, le mot de passe, la confirmation du mot de passe, le prénom, le nom et l’adresse électronique du compte Azure Active Directory valide que vous souhaitez approvisionner dans les champs correspondants, à savoir, **Username**, **Password**, **Confirm Password**, **First Name**, **Last Name** et **Email Address**.
    2.  Cliquez sur **Save**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur AirWatch fourni par ce service pour approvisionner des comptes d’utilisateurs Azure Active Directory.

##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à AirWatch, procédez comme suit :

1.  Dans le portail Azure Classic, créez un compte de test.

2.  Dans la page d’intégration d’application **AirWatch**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-airwatch-tutorial/IC791932.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-airwatch-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->