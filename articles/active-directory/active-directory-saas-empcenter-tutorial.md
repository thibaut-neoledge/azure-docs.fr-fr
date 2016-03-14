<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec EmpCenter | Microsoft Azure" 
    description="Apprenez à utiliser EmpCenter avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
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
    ms.date="02/29/2016" 
    ms.author="jeedes" />

#Didacticiel : Intégration d’Azure Active Directory à EmpCenter
  
L'objectif de ce didacticiel est de montrer comment intégrer Azure et EmpCenter. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement EmpCenter pour lequel l’authentification unique est activée
  
À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à EmpCenter pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise EmpCenter (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour EmpCenter
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-empcenter-tutorial/IC802916.png "Scénario")
##Activation de l’intégration d’applications pour EmpCenter
  
Cette section décrit l'activation de l'intégration de l'application pour EmpCenter.

###Pour activer l'intégration d’applications pour EmpCenter, procédez comme suit :

1.  Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-empcenter-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-empcenter-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-empcenter-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-empcenter-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **EmpCenter**.

    ![Galerie d’applications](./media/active-directory-saas-empcenter-tutorial/IC802917.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **EmpCenter**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![EmpCentral](./media/active-directory-saas-empcenter-tutorial/IC802918.png "EmpCentral")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s'authentifier sur EmpCenter avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure Classic puis sur la page d’intégration d’applications **EmpCenter**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-empcenter-tutorial/IC802919.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à EmpCenter**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-empcenter-tutorial/IC802920.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les paramètres de l’application**, procédez comme suit :

    ![Configurer les paramètres d’application](./media/active-directory-saas-empcenter-tutorial/IC802921.png "Configurer les paramètres d’application")

    1.  Dans la zone de texte **URL d’authentification**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre application EmpCenter (par exemple : **https://partner-authenticati.empcenter.com/workforce/SSO.do*).
2.  Cliquez sur **Suivant**

4.  Dans la page **Configurer l’authentification unique sur EmpCenter**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-empcenter-tutorial/IC802922.png "Configurer l’authentification unique")

5.  Envoyez le fichier de métadonnées téléchargé à votre équipe de support technique EmpCenter.

    >[AZURE.NOTE] Votre équipe de support technique EmpCenter doit se charger de la configuration de l’authentification unique. Vous recevrez une notification lorsque l’authentification unique aura été activée pour votre abonnement.

6.  Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-empcenter-tutorial/IC802923.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Pour permettre aux utilisateurs Azure AD de se connecter à EmpCenter, vous devez les approvisionner dans EmpCenter. Dans le cas d’EmpCenter, les comptes d'utilisateur doivent être créés par votre équipe de support EmpCenter.

>[AZURE.NOTE] Vous pouvez utiliser n'importe quel outil ou API de création de compte utilisateur, fourni par EmpCenter, pour approvisionner des comptes utilisateur Azure Active Directory.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à EmpCenter, procédez comme suit :

1.  Dans le portail Azure Classic, créez un compte de test.

2.  Dans la page d’intégration d’applications **EmpCenter**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-empcenter-tutorial/IC802924.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-empcenter-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0302_2016-->