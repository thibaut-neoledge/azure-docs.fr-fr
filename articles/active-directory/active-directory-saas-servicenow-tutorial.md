<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory à ServiceNow | Microsoft Azure" description="Découvrez comment utiliser ServiceNow avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi"/>
#Didacticiel : Intégration d’Azure Active Directory à ServiceNow
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=521880).
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et ServiceNow.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire dans ServiceNow
  
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

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-servicenow-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-servicenow-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-servicenow-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-servicenow-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la zone **rechercher**, entrez **ServiceNow**.

    ![Galerie d'applications](./media/active-directory-saas-servicenow-tutorial/IC701016.png "Galerie d'applications")

7.  Dans le volet des résultats, sélectionnez **ServiceNow**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![ServiceNow](./media/active-directory-saas-servicenow-tutorial/IC701017.png "ServiceNow")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur ServiceNow avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

Dans le cadre de cette procédure, vous devez télécharger un certificat codé en base 64 sur votre locataire Dropbox for Business. Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Sur la page d’intégration d’applications **ServiceNow** du portail Azure AD, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configurer l’authentification unique")

2.  Sur la page **Comment voulez-vous que les utilisateurs se connectent à ServiceNow**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configurer l’authentification unique")

3.  Sur la page **Configurer l’URL de l'application**, dans la zone de texte **URL de connexion à ServiceNow**, tapez votre URL selon le modèle suivant « *https://<InstanceName>.servicenow.com* », puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurer l’URL de l’application")

4.  Sur la page **Configurer l’authentification unique à ServiceNow**, cliquez sur **Télécharger le certificat**, enregistrez le fichier de certificat en local sur votre ordinateur, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configurer l’authentification unique")

5.  Dans votre locataire ServiceNow, dans la barre de navigation à gauche, cliquez sur **Propriétés** pour ouvrir la page des **Propriétés d’authentification unique SAML 2.0**.


6. Sur la page **Propriétés d’authentification unique SAML 2.0**, procédez comme suit :

     6.1. En regard de **Activer l'authentification externe**, sélectionnez **Oui**.


     6.2. Dans la zone de texte **L'URL du fournisseur d'identité qui émettra le jeton de sécurité SAML2 avec les informations sur l’utilisateur**, entrez **https://sts.windows.net/<le GUID de votre locataire>/**.


     6.3. Dans la zone de texte **L'URL de base pointant vers le service AuthnRequest du fournisseur d'identité**, entrez **https://login.windows.net/<le GUID de votre locataire >/saml2**.


     6.4. Dans la zone de texte **L'URL de base pointant vers le service SingleLogoutRequest du fournisseur d'identité**, entrez **https://login.windows.net/<le GUID de votre locataire >/saml2**.


     6.5. Dans la zone de texte **La liaison du protocole pour le service SingleLogoutRequest du fournisseur d'identité**, entrez **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

     6.6. En regard de **SingleLogoutRequest**, sélectionnez **Oui**.

     6.7. Dans la zone de texte **Quand l’authentification unique SAML 2.0 échoue parce que la session n'est pas authentifiée ou qu’il s’agit de la première connexion, rediriger vers cette URL**, entrez **https://login.windows.net/<le GUID de votre locataire>/saml2**.

  

7. Dans la section **Propriétés du fournisseur de services (Service-Now)**, procédez comme suit :

     7.1. Dans la zone de texte **L'URL pointant vers la page d'accueil de l'instance Service-now**, tapez l'URL de la page d'accueil de votre instance ServiceNow. L'URL de la page d'accueil de l'instance ServiceNow est une concaténation de votre **URL de locataire ServiceNow** et de **/navpage.do** : **https://<InstanceName>.service-now.com/navpage.do** <br><br> ![Page d'accueil de l'instance de Service-now](./media/active-directory-saas-servicenow-tutorial/IC700342.png "Page d'accueil de l'instance de Service-now")


     7.2. Dans la zone de texte **L'identification de l'entité ou l'émetteur**, entrez l'URL de votre locataire.

     7.3. Dans la zone de texte **L'URI de l'audience qui accepte un jeton SAML2**, entrez l'URL de votre locataire.

     7.4. Dans la zone de texte **Le champ de tableau de l'utilisateur à faire correspondre avec l’élément NameID de l’objet dans la SAMLResponse**, entrez **e-mail**.

     7.5. Dans la zone de texte **La stratégie NameID à utiliser pour renvoyer le NameID de l’objet dans la SAMLResponse**, entrez **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

     7.6 Laissez la case **Créer une demande AuthnContextClass dans l'instruction AuthnRequest** décochée.

     7.7 Dans la zone de texte **La méthode AuthnContextClassRef qui sera incluse dans notre AuthnRequest SAML 2.0 au fournisseur d'identité**, entrez **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.



8. Dans la section **Paramètres avancés**, procédez comme suit :

     8.1. Dans la zone de texte **Le nombre de secondes avant la contrainte « notBefore » ou après la contrainte « notOnOrAfter » à considérer comme toujours valide**, entrez **60**.


9. Pour enregistrer la configuration, cliquez sur **Enregistrer**.

10. Dans la barre de navigation à gauche, cliquez sur **Certificat** pour ouvrir la page de **Certificat**.



11. Pour télécharger votre certificat, sur la page Certificat, procédez comme suit :

     11.1. Cliquez sur **Nouveau**.

     11.2. Dans la zone de texte **Nom**, tapez **SAML 2.0**.

     11.3. Sélectionnez **Actif**.

     11.4. En regard de **Format**, sélectionnez **PEM**.

     11.5. Créez un fichier codé en base 64 à partir du certificat téléchargé. >[AZURE.NOTE]Pour plus d'informations, consultez la section [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

     11.6. Dans le **Bloc-notes**, ouvrez votre fichier codé en base 64, puis copiez le contenu de ce fichier dans le presse-papiers.

     11.7. Dans la zone de texte **Certificat PEM**, collez le contenu du presse-papiers.

     11.8. Cliquez sur **Envoyer**.



12. Dans le portail Azure Active Directory, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur Terminer pour fermer la boîte de dialogue Configurer l’authentification unique.<br><br>![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/IC749326.png "Configurer l’authentification unique")




##Configuration de l'approvisionnement des utilisateurs
  
Cette section décrit comment activer l’approvisionnement des utilisateurs des comptes d’utilisateurs Active Directory sur ServiceNow.


### Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1. Dans le portail de gestion Azure, sur la page d’intégration d’application **ServiceNow**, cliquez sur **Configurer l’approvisionnement des utilisateurs**.<br><br>![Approvisionnement d'utilisateurs](./media/active-directory-saas-servicenow-tutorial/IC769498.png "Approvisionnement d'utilisateurs")


2. Sur la page **Saisissez vos identifiants ServiceNow pour permettre l’approvisionnement automatique des utilisateurs**, indiquez les paramètres de configuration suivants : Configurer l’approvisionnement des utilisateurs

     2.1. Dans la zone de texte **Nom de l'Instance ServiceNow**, tapez le nom d'instance ServiceNow.

     2.2. Dans la zone de texte **Nom d'utilisateur Admin ServiceNow**, tapez le nom du compte d'administrateur ServiceNow.

     2.3. Dans la zone de texte **Mot de passe Admin ServiceNow**, tapez le mot de passe de ce compte.

     2.4. Cliquez sur **Valider** pour vérifier votre configuration.

     2.5. Cliquez sur le bouton **Suivant** pour ouvrir la page **Étapes suivantes**.

     2.6. Si vous voulez approvisionner tous les utilisateurs pour cette application, sélectionnez « **Approvisionner automatiquement tous les comptes d'utilisateurs dans le répertoire de cette application** ». <br><br> ![Étapes suivantes](./media/active-directory-saas-servicenow-tutorial/IC698804.png "Étapes suivantes")

     2.7. Sur la page **Étapes suivantes**, cliquez sur **Terminer** pour enregistrer votre configuration.











##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à ServiceNow, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte test.

2.  Dans la page d’intégration d’application **ServiceNow**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-servicenow-tutorial/IC769499.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter** puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-servicenow-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!----HONumber=August15_HO8-->