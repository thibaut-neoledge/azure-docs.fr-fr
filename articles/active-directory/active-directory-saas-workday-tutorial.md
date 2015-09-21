<properties pageTitle="Didacticiel : Intégration d’Azure AD à Workday | Microsoft Azure" description="Découvrez comment utiliser Workday avec Azure AD pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure AD à Workday
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=330042).
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Workday. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire dans Workday
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Workday
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Configuration de l'approvisionnement des utilisateurs

![Scénario](./media/active-directory-saas-workday-tutorial/IC782919.png "Scénario")

##Activation de l’intégration d’applications pour Workday
  
Cette section décrit l’activation de l’intégration d’application pour Workday.

###Pour activer l’intégration d’application pour Workday, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-workday-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-workday-tutorial/IC700994.png "Applications")

4.  Pour ouvrir la **Galerie d’applications**, cliquez sur **Ajouter une application**, puis sur **Ajouter une application utilisable par mon organisation**.

    ![Que voulez-vous faire ?](./media/active-directory-saas-workday-tutorial/IC700995.png "Que voulez-vous faire ?")

5.  Dans la **zone de recherche**, tapez **Workday**.

    ![Workday](./media/active-directory-saas-workday-tutorial/IC701021.png "Workday")

6.  Dans le volet de résultats, sélectionnez **Workday**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Workday](./media/active-directory-saas-workday-tutorial/IC701022.png "Workday")

##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Workday avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez créer un certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans la page d’intégration d’applications **Workday**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-workday-tutorial/IC782920.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Workday**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-workday-tutorial/IC782921.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, procédez comme suit, puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-workday-tutorial/IC782957.png "Configurer l’URL de l’application")

    1.  Dans la zone de texte **URL de connexion**, tapez l’URL utilisée par vos utilisateurs pour se connecter à Workday (par ex. : *https://impl.workday.com/\<client>/login-saml2.htmld*).
    2.  Dans la zone de texte **URL de réponse de Workday**, tapez l’URL appropriée (par ex. : *https://impl.workday.com/\<client>/login-saml.htmld*).

        >[AZURE.NOTE]Votre URL de réponse doit disposer d'un sous-domaine (par exemple, www, wd2, wd3, wd3-impl, wd5, wd5-impl). Une solution telle que "**http://www.myworkday.com*" fonctionne, mais pas "**http://myworkday.com*".
 
4.  Dans la page **Configurer l'authentification unique sur Workday**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-workday-tutorial/IC782922.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Workday en tant qu’administrateur.

6.  Accédez à **Menu > Workbench**.

    ![Workbench](./media/active-directory-saas-workday-tutorial/IC782923.png "Workbench")

7.  Accédez à **Administration des comptes**.

    ![Administration des comptes](./media/active-directory-saas-workday-tutorial/IC782924.png "Administration des comptes")

8.  Accédez à **Modifier le paramétrage du locataire – Sécurité**.

    ![Modifier la sécurité du client](./media/active-directory-saas-workday-tutorial/IC782925.png "Modifier la sécurité du client")

9.  Dans la section **URL de redirection**, procédez comme suit :

    ![URL de redirection](./media/active-directory-saas-workday-tutorial/IC7829581.png "URL de redirection")

     9\.1. Cliquez sur **Ajouter une ligne**.

     9\.2. Dans les zones de texte **URL de redirection de connexion** et **URL de redirection mobile**, tapez l'**URL client Workday** que vous avez entrée dans la page **Configurer l'URL de l'application** du portail Azure.
    
     9\.3. Dans la page **Configurer l'authentification unique dans Workday** du portail Azure, copiez l'**URL du service de déconnexion unique**, puis collez-la dans la zone de texte **URL de redirection de déconnexion**.

     9\.4. Dans la zone de texte **Environnement**, saisissez le nom de l'environnement.


       >[AZURE.NOTE]La valeur de l'attribut Environment est liée à celle de du client : > >- Si le nom de domaine de l'URL du client Workday commence par impl (par exemple, *https://impl.workday.com/\<client>/login-saml2.htmld*), l'attribut **Environment** doit être défini sur Implementation. >- Si le nom de domaine commence différemment, vous devez contacter Workday pour obtenir la valeur d'**Environment** correspondante.

10. Dans la section **SAML Setup**, procédez comme suit :

    ![Configuration de SAML](./media/active-directory-saas-workday-tutorial/IC782926.png "Configuration de SAML")

     10\.1. Sélectionnez **Activer l'authentification SAML**.

     10\.2. Cliquez sur **Ajouter une ligne**.

11. Dans la section SAML Identity Providers, procédez comme suit :

    ![Fournisseurs d’identité SAML](./media/active-directory-saas-workday-tutorial/IC7829271.png "Fournisseurs d’identité SAML")

     11\.1. Dans la zone de texte Nom du fournisseur d'identité, saisissez le nom d'un fournisseur (par ex., *SPInitiatedSSO*).

     11\.2. Dans le portail Azure, dans la page **Configurer l'authentification unique sur Workday**, copiez la valeur de l'**ID de fournisseur d'identité**, puis collez-la dans la zone de texte **Émetteur**.

     11\.3. Sélectionnez **Activer la déconnexion initiée par Workday**.

     11\.4. Dans la page **Configurer l'authentification unique dans Workday** du portail Azure, copiez la valeur de l'**URL du service de déconnexion unique**, puis collez-la dans la zone de texte **URL de demande de déconnexion**.


     11\.3. Cliquez sur **Certificat de clé publique du fournisseur d'identité**, puis sur **Créer**. <br><br> ![Créer](./media/active-directory-saas-workday-tutorial/IC782928.png "Créer")

     11\.4. Cliquez sur **Créer la clé publique x509**. <br><br> ![Créer](./media/active-directory-saas-workday-tutorial/IC782929.png "Créer")

     11\.5. Dans la section **Clé publique x509**, procédez comme suit : <br><br> ![Afficher la clé publique x509](./media/active-directory-saas-workday-tutorial/IC782930.png "Afficher la clé publique x509") <br>

      1.  Dans la zone de texte **Nom**, tapez le nom du certificat (par ex. : *PPE\_SP*).
      2.  Dans la zone de texte **Valide à partir du**, saisissez la valeur correspondante du certificat.
      3.  Dans la zone de texte **Valide jusqu'au**, saisissez la valeur correspondante du certificat.
		
           >[AZURE.NOTE]Vous pouvez obtenir les dates de début et de fin de validité du certificat téléchargé en double-cliquant dessus. Les dates sont répertoriées dans l'onglet **Détails**.

      4.  Créez un fichier **codé en base 64** à partir du certificat téléchargé.

		>[AZURE.TIP]Pour plus d'informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

      5.  Ouvrez le certificat codé en base 64 dans le Bloc-notes, puis copiez son contenu.
      6.  Dans la zone de texte **Certificat**, collez le contenu du Presse-papiers.
      7.  Cliquez sur **OK**.

12.  Procédez comme suit : <br><br> ![Configuration SSO](./media/active-directory-saas-workday-tutorial/IC7829351111.png "Configuration SSO")

     12\.1. Activez la **paire de clés privées x509**.

     12\.2. Dans la zone de texte **ID du fournisseur de services**, saisissez ****http://www.workday.com**.

     12\.3. Sélectionnez **Activer l'authentification SAML initiée par le fournisseur de services**.

     12\.4. Dans la boîte de dialogue **Configurer l'authentification unique sur Workday** du portail Azure, copiez la valeur de l'**URL du service d'authentification unique**, puis collez-la dans la zone de texte **URL du service IdP SSO**.
     
     12\.5 Sélectionnez **Ne pas compresser la demande d'authentification initiée par le fournisseur de services**.

     12\.6. Comme **Méthode de signature de la demande d'authentification**, sélectionnez **SHA256**. <br><br> ![Méthode de signature de la demande d’authentification](./media/active-directory-saas-workday-tutorial/IC782932.png "Méthode de signature de la demande d’authentification") <br><br>
 
     12\.7 Cliquez sur **OK**. <br><br> ![OK](./media/active-directory-saas-workday-tutorial/IC782933.png "OK")

12. Dans la page **Configurer l'authentification unique sur Workday** du portail Azure AD, cliquez sur **Suivant**. <br><br>

    ![Configurer l’authentification unique](./media/active-directory-saas-workday-tutorial/IC782934.png "Configurer l’authentification unique")

13. À la page **Confirmation de l'authentification unique**, cliquez sur **Terminer**. <br><br>

    ![Configurer l’authentification unique](./media/active-directory-saas-workday-tutorial/IC782935111.png "Configurer l’authentification unique")



##Configuration de l'approvisionnement des utilisateurs
  
Pour approvisionner un utilisateur test dans Workday, vous devez contacter l’équipe du support technique Workday. L’équipe du support technique Workday crée l’utilisateur à votre place.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Workday, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte test.

2.  Dans la page d'intégration d'applications **Workday**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-workday-tutorial/IC782935.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-workday-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez la rubrique [Présentation du panneau d'accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Sept15_HO2-->