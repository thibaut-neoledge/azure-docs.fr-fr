<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory à Salesforce Sandbox | azure.microsoft.com/ Azure"
    description="Apprenez à utiliser Salesforce Sandbox avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !" 
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
    ms.date="08/12/2016" 
    ms.author="jeedes" />


#Didacticiel : Intégration d’Azure Active Directory à Salesforce Sandbox
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.azure.microsoft.com/.com/fwlink/?LinkId=521878).
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Salesforce Sandbox. Les bacs à sable (sandbox) vous permettent de créer plusieurs copies de votre organisation dans des environnements distincts à des fins diverses, notamment le développement, le test et la formation, sans compromettre les données ou les applications de votre organisation de production Salesforce. Pour plus d’informations, consultez la page [Présentation de Sandbox](https://help.salesforce.com/HTViewHelpDoc?id=create_test_instance.htm&language=en_US)
  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un bac à sable (sandbox) dans Salesforce.com
  
Si vous ne disposez pas encore d’un bac à sable (sandbox) valide dans Salesforce.com, vous devez contacter Salesforce.
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour Salesforce Sandbox
2.  Configuration de l’authentification unique
3.  Activation de votre domaine
4.  Configuration de l'approvisionnement des utilisateurs
5.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769571.png "Scénario")
##Activation de l’intégration d’application pour Salesforce Sandbox
  
Cette section décrit l’activation de l’intégration d’application pour Salesforce Sandbox.

###Pour activer l’intégration d’application pour Salesforce Sandbox, procédez comme suit :

1.  Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700994.png "Applications")

4.  Pour ouvrir la **Galerie d’applications**, cliquez sur **Ajouter une application**, puis sur **Ajouter une application utilisable par mon organisation**.

    ![Que voulez-vous faire ?](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700995.png "Que voulez-vous faire ?")

5.  Dans la **zone de recherche**, tapez **Salesforce Sandbox**.

    ![Galerie d’applications](./media/active-directory-saas-salesforce-sandbox-tutorial/IC710978.png "Galerie d’applications")

6.  Dans le volet des résultats, sélectionnez **Salesforce Sandbox**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746474.png "Salesforce Sandbox")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Salesforce Sandbox avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure Classic, dans la page d’intégration d’application **Salesforce Sandbox**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-sandbox-tutorial/IC749323.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Salesforce Sandbox**, sélectionnez **Authentification unique azure.microsoft.com/ Azure AD**, puis cliquez sur **Suivant**.

    ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746479.png "Salesforce Sandbox")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Mindflash**, tapez votre URL selon le modèle suivant `http://company.my.salesforce.com`, puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781022.png "Configurer l’URL de l’application")

4. Si vous avez déjà configuré l’authentification unique pour une autre instance Salesforce Sandbox dans votre répertoire, vous devez également configurer l’**identificateur** en utilisant la même valeur que pour l’**URL d’authentification**. Pour afficher le champ **Identificateur** cochez la case **Afficher les paramètres avancés** sur la page **Configurer l’URL de l’application** de la boîte de dialogue.

4.  Dans la page **Configurer l’authentification unique à Salesforce Sandbox**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781023.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre sandbox Salesforce en tant qu’administrateur.

6.  Dans le menu situé en haut, cliquez sur **Setup**.

    ![Paramétrage](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781024.png "Paramétrage")

7.  Dans le volet de navigation de gauche, cliquez sur **Security Controls**, puis sur **Single Sign-On Settings**.

    ![Paramètres d’authentification unique](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781025.png "Paramètres d’authentification unique")

8.  Dans la section Single Sign-On Settings, procédez comme suit :

    ![Paramètres d’authentification unique](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781026.png "Paramètres d’authentification unique")

    a. Sélectionnez **SAML Enabled**.
    
    b. Cliquez sur **Nouveau**.

9.  Dans la section SAML Single Sign-On Settings, procédez comme suit :

    ![Paramètres d’authentification unique SAML](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781027.png "Paramètres d’authentification unique SAML")

    a. Dans la zone de texte Name, indiquez le nom de votre configuration (par exemple, *SPSSOWAAD\_Test*).
    
    b. Dans la page **Configurer l’authentification unique sur Salesforce Sandbox** du portail Azure Classic, copiez la valeur **URL de l’émetteur** et collez-la dans la zone de texte **Issuer (Émetteur)**.
    
    c. Dans la zone de texte **ID d’entité**, entrez **https://test.salesforce.com** s’il s’agit de la première instance Salesforce Sandbox que vous ajoutez à votre répertoire. Si vous avez déjà ajouté une instance Salesforce Sandbox, pour **l’ID d’entité**, entrez **l’URL d’authentification**, qui doit être au format : `http://company.my.salesforce.com`
    
    d. Cliquez sur **Parcourir** pour charger le certificat téléchargé.
    
    e. Pour **SAML Identity Type**, sélectionnez **Assertion contains the Federation ID from the User object**.
    
    f. Pour **SAML Identity Location**, sélectionnez **Identity is in the NameIdentifier element of the Subject statement**.
    
    g. Dans la page **Configurer l’authentification unique sur Salesforce Sandbox** du portail Azure Classic, copiez la valeur **URL de connexion distante** et collez-la dans la zone de texte **Identity Provider Login URL (URL de connexion du fournisseur d’identité)**.
    
    h. SFDC ne prend pas en charge la déconnexion SAML. Pour contourner ce problème, collez « https://login.windows.net/common/wsfederation?wa=wsignout1.0 » dans la zone de texte **d’URL de déconnexion du fournisseur d’identité**.
    
    i. Comme **Liaison de demande initiée par le fournisseur de services**, sélectionnez **HTTP POST**.
    
    j. Cliquez sur **Enregistrer**.

10. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781028.png "Configurer l’authentification unique")

##Activation de votre domaine
  
Cette section suppose que vous avez déjà créé un domaine. Pour plus d’informations, consultez [Définition de votre nom de domaine](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

###Pour activer votre domaine, procédez comme suit :

1.  Dans le volet de navigation gauche, cliquez sur **Domain Management**, puis sur **My Domain**.

    ![Mon domaine](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781029.png "Mon domaine")

    >[AZURE.NOTE]Vérifiez que votre domaine a été correctement configuré.

2.  Dans la section **Login Page Settings**, cliquez sur **Edit**, puis, pour **Authentication Service**, sélectionnez le nom du paramètre d’authentification unique SAML de la section précédente, avant de cliquer sur **Save**.

    ![Mon domaine](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781030.png "Mon domaine")
  
Dès qu’un domaine est configuré, vos utilisateurs doivent utiliser l’URL du domaine pour se connecter au sandbox Salesforce. Pour obtenir la valeur de l’URL, cliquez sur le profil d’authentification unique créé à la section précédente.
##Configuration de l'approvisionnement des utilisateurs
  
Cette section décrit comment activer l’approvisionnement des utilisateurs des comptes d’utilisateurs Active Directory sur Salesforce Sandbox.

###Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1.  Dans le portail Salesforce, dans la barre de navigation supérieure, sélectionnez votre nom pour développer votre menu utilisateur :

    ![Mes paramètres](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698773.png "Mes paramètres")

2.  Dans votre menu utilisateur, sélectionnez **My Settings** pour ouvrir votre page **My Settings**.

3.  Dans le volet de gauche, cliquez sur **Personal** pour développer la section Personal, puis sur **Reset My Security Token**.

    ![Mes paramètres](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698774.png "Mes paramètres")

4.  Dans la page **Reset My Security Token**, cliquez sur **Reset Security Token** pour demander un courrier électronique contenant votre jeton de sécurité Salesforce.com.

    ![Nouveau jeton](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698776.png "Nouveau jeton")

5.  Recherchez dans votre boîte de réception un courrier électronique provenant de Salesforce.com ayant pour objet « **salesforce.com.com security confirmation** » (confirmation de sécurité salesforce.com.com).

6.  Lisez ce courrier électronique et copiez la valeur du jeton de sécurité.

7.  Dans le portail Azure Classic, sur la page d’intégration d’application **Salesforce Sandbox**, cliquez sur **Configurer l’approvisionnement d’utilisateurs** pour ouvrir la boîte de dialogue **Configurer l’approvisionnement d’utilisateurs**.

    ![Configurer l’approvisionnement des utilisateurs](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769573.png "Configurer l’approvisionnement des utilisateurs")

8.  Dans la page **Enter your Salesforce Sandbox credentials to enable automatic user provisioning**, indiquez les paramètres de configuration suivants :

    ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746476.png "Salesforce Sandbox")

    a. Dans la zone de texte **Salesforce Sandbox Admin User Name**, tapez le nom d’un compte Salesforce Sandbox auquel le profil **System Administrator** est attribué dans Salesforce.com.

    b. Dans la zone de texte **Salesforce Sandbox Admin Password**, tapez le mot de passe de ce compte.

    c. Dans la zone de texte **User Security Token**, collez la valeur du jeton de sécurité.

    d. Cliquez sur **Valider** pour vérifier votre configuration.

    e. Cliquez sur le bouton **Suivant** pour ouvrir la page **Confirmation**.

9.  Dans la page **Confirmation**, cliquez sur **Terminer** pour enregistrer votre configuration.
##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Salesforce Sandbox, procédez comme suit :

1.  Dans le portail Azure Classic, créez un compte de test.

2.  Sur la page d’intégration d’application **Salesforce Sandbox**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769574.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-salesforce-sandbox-tutorial/IC767830.png "Oui")
  
À présent, patientez 10 minutes et vérifiez que le compte est bien synchronisé avec Salesforce Sandbox.
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](https://msdn.azure.microsoft.com/.com/library/dn308586).

<!---HONumber=AcomDC_0817_2016-->