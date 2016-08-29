<properties 
    pageTitle="Didacticiel : Intégration d'Azure Active Directory à Dropbox for Business | azure.microsoft.com/ Azure" 
    description="Apprenez à utiliser Dropbox for Business avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !" 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#Didacticiel : Intégration d'Azure Active Directory à Dropbox for Business
  
L'objectif de ce didacticiel est de montrer comment intégrer Azure et Dropbox for Business.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire test dans Dropbox for Business
  
À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Dropbox for Business pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Dropbox for Business (connexion initiée par le prestataire de service) ou à l’aide de la [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l'intégration d'applications pour Dropbox for Business
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769508.png "Scénario")



##Activation de l'intégration d'applications pour Dropbox for Business
  
Cette section décrit l'activation de l'intégration d'applications pour Dropbox for Business.

###Pour activer l'intégration d'applications pour Dropbox for Business, procédez comme suit :

1.  Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, saisissez **Dropbox for Business**.

    ![Galerie d’applications](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701010.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **Dropbox for Business**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Dropbox for Business](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701011.png "Dropbox for Business")

##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s'authentifier sur Dropbox for Business avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

Dans le cadre de cette procédure, vous devez télécharger un certificat codé en base 64 sur votre locataire Dropbox for Business. Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le Portail Azure Classic, dans la page d’intégration d’application **Dropbox for Business**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749323.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Dropbox for Business**, sélectionnez **Authentification unique avec azure.microsoft.com/ Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749327.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, procédez comme suit :

	a. Ouvrez une session sur votre locataire Dropbox for Business.

	![Configurer l’authentification unique](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769509.png "Configurer l’authentification unique")

	b. À gauche du volet de navigation, cliquez sur **Console d’administration**.

	![Configurer l’authentification unique](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769510.png "Configurer l’authentification unique")

	c. Dans la **Console d’administration**, cliquez sur **Authentification** dans le volet de navigation gauche.

	![Configurer l’authentification unique](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769511.png "Configurer l’authentification unique")

	d. Dans la section **Authentification unique**, sélectionnez **Activer l’authentification unique**, puis cliquez sur **Plus** pour développer cette section.

	![Configurer l’authentification unique](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769512.png "Configurer l’authentification unique")

	e. Copiez l’URL en regard de **Les utilisateurs peuvent se connecter en entrant leur adresse e-mail ou accéder directement à **.

	![Configurer l’authentification unique](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769513.png "Configurer l’authentification unique")

	f. Collez l’URL sur le portail Azure, dans la zone de texte d’URL **Connexion DropBox for business**.

	![Configurer l’authentification unique](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769514.png "Configurer l’authentification unique")



4. Dans la page **Configurer l’authentification unique sur Dropbox for Business**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

	![Configurer l’authentification unique](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769515.png "Configurer l’authentification unique")


5. Sur votre locataire Dropbox for Business, dans la section **Authentification unique** de la page **Authentification**, exécutez les opérations suivantes :

	![Configurer l’authentification unique](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "Configurer l’authentification unique")

	a. Cliquez sur **Obligatoire**.

	b. Dans le Portail Azure Classic, dans la page **Configurer l’authentification unique sur Dropbox for Business** de la boîte de dialogue, copiez la valeur **URL de page de connexion**, puis collez-la dans la zone de texte **URL de connexion**.


	c. Créez un fichier **codé en base 64** à partir du certificat téléchargé.

	> [AZURE.TIP] Pour plus d’informations, consultez [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o).


	d. Cliquez sur le bouton **« Choisir un certificat »**, puis recherchez votre **fichier de certificat codé en base 64**.


	e. Cliquez sur le bouton **« Enregistrer les modifications »** pour terminer la configuration de votre locataire DropBox for Business.


6. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

	![Configurer l’authentification unique](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749329.png "Configurer l’authentification unique")



##Configuration de l'approvisionnement des utilisateurs
  
Cette section décrit comment activer l'approvisionnement des utilisateurs des comptes d'utilisateurs Active Directory sur Dropbox for Business.


### Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1. Dans le Portail Azure Classic, dans la page d’intégration d’application **Dropbox for Business**, cliquez sur **Configurer l’approvisionnement des utilisateurs** pour ouvrir la boîte de dialogue **Configurer l’approvisionnement des utilisateurs**.

2. Dans la page Activer l’approvisionnement d’utilisateurs pour DropBox for Business, cliquez sur Activer l’approvisionnement d’utilisateurs pour ouvrir la Connexion à Dropbox afin d’établir un lien avec la boîte de dialogue Azure AD.

	![Approvisionnement d'utilisateurs](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769517.png "Approvisionnement d'utilisateurs")

3. Dans la boîte de dialogue **Se connecter à Dropbox pour établir une connexion à Azure Active Directory**, connectez-vous à votre locataire Dropbox for Business.

	![Approvisionnement d'utilisateurs](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769518.png "Approvisionnement d'utilisateurs")



4. Cliquez sur **Autoriser** pour autoriser Azure AD à accéder à Dropbox.

	![Approvisionnement d'utilisateurs](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769519.png "Approvisionnement d'utilisateurs")



5. Pour terminer la configuration, cliquez sur le bouton **Terminé**.

	![Approvisionnement d'utilisateurs](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769520.png "Approvisionnement d'utilisateurs")




##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Dropbox for Business, procédez comme suit :

1.  Dans le portail Azure Classic, créez un compte de test.

2.  Dans la page d’intégration d’applications **Dropbox for Business**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769521.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-dropboxforbusiness-tutorial/IC767830.png "Oui")
  


Vous devez maintenant attendre 10 minutes et vérifier que le compte a été synchronisé avec Dropbox for Business.

Une première étape de vérification consiste à contrôler l’état d’approvisionnement en cliquant sur le **Tableau de bord** dans la page d’intégration d’applications **Dropbox for Business** dans le Portail Azure Classic.

![Affecter des utilisateurs](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769522.png "Affecter des utilisateurs")


Si le cycle d'approvisionnement d'utilisateur a abouti, l'état associé est indiqué.

![Affecter des utilisateurs](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769523.png "Affecter des utilisateurs")


Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!---HONumber=AcomDC_0817_2016-->