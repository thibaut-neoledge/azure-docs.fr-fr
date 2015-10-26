<properties pageTitle="Didacticiel : Intégration d’Azure AD à ShiftPlanning | Microsoft Azure" description="Découvrez comment utiliser ShiftPlanning avec Azure AD pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure AD à ShiftPlanning
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=526797).
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et ShiftPlanning. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement ShiftPlanning pour lequel l’authentification unique est activée
  
À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à ShiftPlanning pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise ShiftPlanning (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour ShiftPlanning
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-shiftplanning-tutorial/IC786612.png "Scénario")
##Activation de l’intégration d’applications pour ShiftPlanning
  
Cette section décrit l’activation de l’intégration d’applications pour ShiftPlanning.

###Pour activer l’intégration d’applications pour ShiftPlanning, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-shiftplanning-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-shiftplanning-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-shiftplanning-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-shiftplanning-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **ShiftPlanning**.

    ![Galerie d’applications](./media/active-directory-saas-shiftplanning-tutorial/IC786613.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **ShiftPlanning**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![ShiftPlanning](./media/active-directory-saas-shiftplanning-tutorial/IC786614.png "ShiftPlanning")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur ShiftPlanning avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans la page d’intégration d’applications **ShiftPlanning** du portail Azure AD, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-shiftplanning-tutorial/IC786615.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à ShiftPlanning**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-shiftplanning-tutorial/IC786616.png "Configurer l’authentification unique")

3.  Dans la zone de texte **URL de connexion à ShiftPlanning**de la page **Configurer l’URL de l’application**, tapez votre URL au format « **https://company.shiftplanning.com/includes/saml/*", puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-shiftplanning-tutorial/IC786617.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur ShiftPlanning**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-shiftplanning-tutorial/IC786618.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise **ShiftPlanning** en tant qu’administrateur.

6.  Dans le menu situé en haut, cliquez sur **Admin**.

    ![Administrateur](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Administrateur")

7.  Sous **Intégration**, cliquez sur **Single Sign-On**.

    ![Authentification unique](./media/active-directory-saas-shiftplanning-tutorial/IC786620.png "Authentification unique")

8.  Dans la section **Single Sign-On**, procédez comme suit :

    ![Authentification unique](./media/active-directory-saas-shiftplanning-tutorial/IC786905.png "Authentification unique")

    1.  Sélectionnez **SAML enabled**.
    2.  Sélectionnez **Allow Password Login**.
    3.  Dans la page **Configurer l’authentification unique sur ShiftPlanning** du portail Azure, copiez la valeur **URL de connexion distante** et collez-la dans la zone de texte **URL de l’émetteur SAML**.
    4.  Dans la page **Configurer l’authentification unique sur ShiftPlanning** du portail Azure, copiez la valeur **URL de déconnexion distante** et collez-la dans la zone de texte **URL de déconnexion distante**.
    5.  Créez un fichier **codé en base 64** à partir du certificat téléchargé.  

        >[AZURE.TIP]Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    6.  Ouvrez votre certificat codé en base 64 dans le bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **Certificat X.509**.
    7.  Cliquez sur **Enregistrer les paramètres**.

9.  Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-shiftplanning-tutorial/IC786621.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Pour se connecter à ShiftPlanning, les utilisateurs d’Azure AD doivent être approvisionnés dans ShiftPlanning. Dans le cas de ShiftPlanning, l’approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous au site d’entreprise **ShiftPlanning** en tant qu’administrateur.

2.  Cliquez sur **Admin**.

    ![Administrateur](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Administrateur")

3.  Cliquez sur **Staff**.

    ![Staff](./media/active-directory-saas-shiftplanning-tutorial/IC786623.png "Staff")

4.  Sous **Actions**, cliquez sur **Add employee**.

    ![Add Employees](./media/active-directory-saas-shiftplanning-tutorial/IC786624.png "Add Employees")

5.  Dans la section **Add employees**, procédez comme suit :

    ![Save Employees](./media/active-directory-saas-shiftplanning-tutorial/IC786625.png "Save Employees")

    1.  Indiquez le prénom, le nom et l’adresse de messagerie du compte AAD valide que vous souhaitez approvisionner dans les zones de texte **First Name**, **Last Name** et **Email**.
    2.  Cliquez sur **Save employees**.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autre outil ou API de création de compte utilisateur, fourni par ScreenSteps, pour approvisionner des comptes d’utilisateur AAD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à ShiftPlanning, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte test.

2.  Dans la page d’intégration d’applications **ShiftPlanning**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-shiftplanning-tutorial/IC786626.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-shiftplanning-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->