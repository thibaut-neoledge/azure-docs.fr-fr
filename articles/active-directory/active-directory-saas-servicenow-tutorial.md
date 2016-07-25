<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory à ServiceNow | Microsoft Azure" 
    description="Découvrez comment utiliser ServiceNow avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !" 
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
    ms.date="07/07/2016" 
    ms.author="jeedes" />

#Didacticiel : Intégration d’Azure Active Directory à ServiceNow
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et ServiceNow. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire dans ServiceNow, version Calgary ou version ultérieure
-   Le locataire ServiceNow doit avoir le [plug-in d’authentification unique à plusieurs fournisseurs](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) activé. Cette opération est possible en envoyant une demande de service à https://hi.service-now.com/
  
À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à ServiceNow pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise ServiceNow (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour ServiceNow
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-servicenow-tutorial/IC769496.png "Scénario")
##Activation de l’intégration d’applications pour ServiceNow
  
Cette section décrit l’activation de l’intégration d’applications pour ServiceNow.

###Pour activer l’intégration d’applications pour ServiceNow, procédez comme suit :

1.  Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-servicenow-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-servicenow-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-servicenow-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-servicenow-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **ServiceNow**.

    ![Galerie d’applications](./media/active-directory-saas-servicenow-tutorial/IC701016.png "Galerie d'applications")

7.  Dans le volet des résultats, sélectionnez **ServiceNow**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![ServiceNow](./media/active-directory-saas-servicenow-tutorial/IC701017.png "ServiceNow")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur ServiceNow avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

Dans le cadre de cette procédure, vous devez télécharger un certificat codé en base 64 sur votre locataire Dropbox for Business. Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans la page d’intégration d’applications **ServiceNow** du portail Azure AD Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à ServiceNow**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les paramètres de l’application**, procédez comme suit :

    ![Configurer l’URL de l’application](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurer l’URL de l’application")

    a. Dans la zone de texte **URL d’authentification unique ServiceNow**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre application ServiceNow (p. ex., *https://\<NomInstance>.service-now.com*).

    b. Dans la zone de texte **URL de l’émetteur**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre application ServiceNow (p. ex., *https://\<NomInstance>.service-now.com*).

    c. Cliquez sur **Suivant**

4.  Pour permettre à Azure AD de configurer automatiquement ServiceNow pour l’authentification basée SAML, entrez votre nom d’instance ServiceNow, le nom d’utilisateur administrateur et le mot de passe administrateur dans le formulaire **Configurer automatiquement l’authentification unique** puis cliquez sur *Configurer*. Notez que le nom d’utilisateur administrateur fourni doit avoir le rôle **security\_admin** attribué dans ServiceNow pour que cela fonctionne. Sinon, pour configurer manuellement ServiceNow afin d’utiliser Azure AD comme fournisseur d’identité SAML, cliquez sur **Configurer manuellement l’application pour l’authentification unique**, sur **Suivant**, puis effectuez les étapes suivantes.

    ![Configurer l’URL de l’application](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configurer l’URL de l’application")



5.  Dans la page **Configurer l’authentification unique à ServiceNow**, cliquez sur **Télécharger le certificat**, enregistrez le fichier de certificat en local sur votre ordinateur, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configurer l’authentification unique")

1. Connectez-vous à votre application ServiceNow en tant qu’administrateur.

2. À gauche du volet de navigation, cliquez sur **Properties**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-servicenow-tutorial/IC7694980.png "Configurer l’URL de l’application")


3. Dans la boîte de dialogue **Multiple Provider SSO Properties**, effectuez les opérations suivantes :

    ![Configurer l’URL de l’application](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "Configurer l’URL de l’application")

    a. Pour **Enable multiple provider SSO**, sélectionnez **Yes**.

    b. Pour **Enable debug logging got the multiple provider SSO integration**, sélectionnez **Yes**.

    c. Dans la zone de texte **The field on the user table that...**, entrez **user\_name**.

    d. Cliquez sur **Enregistrer**.



1. À gauche du volet de navigation, cliquez sur **x509 Certificates**.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC7694973.png "Configurer l’authentification unique")


1. Dans la boîte de dialogue **X.509 Certificates**, cliquez sur **New**.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Configurer l’authentification unique")


1. Dans la boîte de dialogue **X.509 Certificates**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configurer l’authentification unique")

    a. Cliquez sur **Nouveau**.

    b. Dans la zone de texte **Name**, indiquez le nom de votre configuration (p. ex., **TestSAML2.0**).

    c. Sélectionnez **Active**.

    d. Pour **Format**, sélectionnez **PEM**.

    e. Pour **Type**, sélectionnez **Trust Store Cert**.

    f. Créez un fichier codé en Base 64 à partir du certificat téléchargé.
   
	> [AZURE.NOTE] Pour plus d’informations, consultez la section [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).
    
    g. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **PEM Certificate**.

    h. Cliquez sur **Update**.


1. À gauche du volet de navigation, cliquez sur **Identity Providers**.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC7694976.png "Configurer l’authentification unique")

1. Dans la boîte de dialogue **Identity Providers**, cliquez sur **New** :

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Configurer l’authentification unique")


1. Dans la boîte de dialogue **Identity Providers**, cliquez sur **SAML2 Update1?** :

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Configurer l’authentification unique")


1. Dans la boîte de dialogue SAML2 Update1 Properties, effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Configurer l’authentification unique")


    a. Dans la zone de texte **Name**, indiquez le nom de votre configuration (p. ex., **SAML 2.0**).

    b. Dans la zone de texte **Champ utilisateur**, tapez **email** ou **user\_id**, selon le champ utilisé pour identifier les utilisateurs dans votre déploiement ServiceNow.
    
    **Remarque :** vous pouvez configurer Azure AD afin d’émettre l’ID d’utilisateur Azure AD (nom d’utilisateur principal) ou l’adresse de messagerie comme identificateur unique dans le jeton SAML en accédant à la section **ServiceNow > Attributes > Single Sign-On** du portail Azure Classic et en mappant le champ souhaité à l’attribut **nameidentifier**. La valeur stockée pour l'attribut sélectionné dans Azure AD (par exemple, nom d'utilisateur principal) doit correspondre à la valeur stockée dans ServiceNow pour le champ saisi (par exemple, user\_id)

    c. Dans le portail Azure AD Classic, copiez la valeur de **l’ID de fournisseur d’identité**, puis collez-la dans la zone de texte **URL de fournisseur d’identité**.

    d. Dans le portail Azure AD Classic, copiez la valeur de **l’URL de la demande d’authentification**, puis collez-la dans la zone de texte **Demande d’authentification du fournisseur d’identité**.

    e. Dans le portail Azure AD Classic, copiez la valeur de **l’URL du service de déconnexion unique**, puis collez-la dans la zone de texte **Demande de déconnexion unique du fournisseur d’identité**.

    f. Dans la zone de texte **Page d’accueil ServiceNow**, entrez l’URL de la page d’accueil de votre instance ServiceNow.

    > [AZURE.NOTE] La page d’accueil de l’instance ServiceNow est une concaténation de votre **URL de locataire ServiceNow** et de **/navpage.do** (p. ex., *https://fabrikam.service-now.com/navpage.do*).
 

    g. Dans la zone de texte **ID de l’entité / Émetteur**, entrez l’URL de votre locataire ServiceNow.

    h. Dans la zone de texte **URL de l’audience**, entrez l’URL de votre locataire ServiceNow.

    i. Dans la zone de texte **Liaison du protocole pour la demande de déconnexion unique du fournisseur d’identité**, entrez **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    j. Dans la zone de texte Stratégie d’ID de nom, entrez **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    k. Désélectionnez **Créer une classe de contexte d’authentification**.

    l. Dans la **méthode de référentiel de classe de contexte d’authentification**, entrez **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.

    m. Dans la zone de texte **Variation d’horloge**, entrez **60**.

    n. Pour **Script d’authentification unique**, sélectionnez **MultiSSO\_SAML2\_Update1**.

    o. Pour **Certificat x509**, sélectionnez le certificat que vous avez créé à l’étape précédente.

    p. Cliquez sur **Envoyer**.



6. Dans le portail Azure AD Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configurer l’authentification unique")

7. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configurer l’authentification unique")



##Configuration de l'approvisionnement des utilisateurs
  
Cette section décrit comment activer l’approvisionnement des utilisateurs des comptes d’utilisateurs Active Directory sur ServiceNow.


### Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1. Dans la page d’intégration d’applications **ServiceNow** du portail de gestion Azure Classic, cliquez sur **Configurer l’approvisionnement d’utilisateurs**.

	![Approvisionnement d'utilisateurs](./media/active-directory-saas-servicenow-tutorial/IC769498.png "Approvisionnement d'utilisateurs")


2. Sur la page **Saisissez vos identifiants ServiceNow pour permettre l’approvisionnement automatique des utilisateurs**, indiquez les paramètres de configuration suivants : Configurer l’approvisionnement des utilisateurs.

     a. Dans la zone de texte **Nom de l'Instance ServiceNow**, tapez le nom d'instance ServiceNow.

     b. Dans la zone de texte **Nom d’utilisateur admin ServiceNow**, entrez le nom du compte d’administrateur ServiceNow.

     c. Dans la zone de texte **Mot de passe de l’admin ServiceNow**, entrez le mot de passe de ce compte.

     d. Cliquez sur **Valider** pour vérifier votre configuration.

     e. Cliquez sur le bouton **Suivant** pour ouvrir la page **Étapes suivantes**.

     f. Si vous voulez approvisionner tous les utilisateurs pour cette application, sélectionnez « **Approvisionner automatiquement tous les comptes du répertoire dans cette application** ».

	![Étapes suivantes](./media/active-directory-saas-servicenow-tutorial/IC698804.png "Étapes suivantes")

     g. Sur la page **Étapes suivantes**, cliquez sur **Terminer** pour enregistrer votre configuration.











##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à ServiceNow, procédez comme suit :

1.  Dans le portail Azure AD Classic, créez un compte de test.

2.  Sur la page d’intégration d’application **ServiceNow**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-servicenow-tutorial/IC769499.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-servicenow-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!---HONumber=AcomDC_0713_2016-->