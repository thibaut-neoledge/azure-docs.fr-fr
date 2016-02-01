<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Jitbit Helpdesk | Microsoft Azure" 
    description="Découvrez comment utiliser Jitbit Helpdesk avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore." 
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

#Didacticiel : Intégration d’Azure Active Directory avec Jitbit Helpdesk
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Jitbit Helpdesk. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Jitbit Helpdesk
  
À l’issue de ce didacticiel, les utilisateurs d’Azure Active Directory que vous avez affectés à Jitbit Helpdesk pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Jitbit Helpdesk (connexion initiée par le fournisseur de services) ou à l’aide de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour Jitbit Helpdesk
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777676.png "Scénario")
##Activation de l’intégration d’application pour Jitbit Helpdesk
  
Cette section décrit l’activation de l’intégration d’application pour Jitbit Helpdesk.

###Pour activer l’intégration d’application pour Jitbit Helpdesk, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **Jitbit Helpdesk**.

    ![Galerie d’applications](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777677.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Jitbit Helpdesk**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![JitBit](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC781008.png "JitBit")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Jitbit Helpdesk avec leur compte Azure Active Directory en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

>[AZURE.IMPORTANT]Pour être en mesure de configurer l’authentification unique sur votre locataire Jitbit Helpdesk, vous devez au préalable contacter le support technique Jitbit Helpdesk pour faire activer cette fonctionnalité.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, puis dans la page d’intégration d’application **Jitbit Helpdesk**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777678.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Jitbit Helpdesk**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777679.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion de Jitbit Helpdesk**, tapez votre URL selon le modèle suivant « *https://\<nom-locataire>.Jitbit.com* », puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777528.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Jitbit Helpdesk**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sous **c:\\Jitbit Helpdesk.cer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777680.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Jitbit Helpdesk en tant qu’administrateur.

6.  Dans la barre d’outils située dans la partie supérieure, cliquez sur **Administration**.

    ![Administration](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administration")

7.  Cliquez sur **General settings**.

    ![Utilisateurs, entreprises et autorisations](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Utilisateurs, entreprises et autorisations")

8.  Dans la section de configuration **Authentication settings**, procédez comme suit :

    ![Paramètres d’authentification](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777683.png "Paramètres d’authentification")

    1.  Sélectionnez **Enable SAML 2.0 single sign on** avec **OneLogin**.
    2.  Dans le portail Azure, dans la page **Configurer l’authentification unique sur Jitbit Helpdesk**, copiez la valeur **Point de terminaison initié du fournisseur de services**, puis collez-la dans la zone de texte **EndPoint URL**.
    3.  Créez un fichier **codé en base 64** à partir du certificat téléchargé.
        
		>[AZURE.TIP]Pour plus d’informations, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    4.  Ouvrez votre certificat codé en base 64, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **X.509 Certificate**.
    5.  Cliquez sur **Save changes**.

9.  Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777684.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Pour pouvoir se connecter à Jitbit Helpdesk, les utilisateurs d’Azure Active Directory doivent être approvisionnés dans Jitbit Helpdesk. Dans le cas de Jitbit Helpdesk, l’approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre locataire **Jitbit Helpdesk**.

2.  Dans le menu situé dans la partie supérieure, cliquez sur **Administration**.

    ![Administration](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administration")

3.  Cliquez sur **Users, companies and permissions**.

    ![Utilisateurs, entreprises et autorisations](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Utilisateurs, entreprises et autorisations")

4.  Cliquez sur **Add User**.

    ![Ajouter un utilisateur](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777685.png "Ajouter un utilisateur")

5.  Dans la section Create, indiquez les données du compte Azure AD que vous voulez approvisionner, dans les zones de texte suivantes : **Username**, **Email**, **Firstname** et **Last Name**.

    ![Créer](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777686.png "Créer")

6.  Cliquez sur **Create**.

>[AZURE.NOTE]Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Jitbit Helpdesk pour approvisionner des comptes d’utilisateurs Azure Active Directory.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Jitbit Helpdesk, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’application **Jitbit Helpdesk**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777687.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->