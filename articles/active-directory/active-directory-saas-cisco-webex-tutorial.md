<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory à Cisco Webex | Microsoft Azure" description="Apprenez à utiliser Cisco Webex avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure Active Directory à Cisco Webex
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=524907).

L’objectif de ce didacticiel est de montrer comment intégrer Azure et Cisco Webex. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Cisco Webex

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Cisco Webex pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Cisco Webex (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du volet d’accès](https://msdn.microsoft.com/library/dn308586).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Cisco Webex
2.  Configuration de l’authentification unique
3.  Configuration de l’approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-cisco-webex-tutorial/IC777614.png "Scénario")
##Activation de l’intégration d’applications pour Cisco Webex

Cette section décrit l’activation de l’intégration d’applications pour Cisco Webex.

###Pour activer l’intégration d’applications pour Cisco Webex, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-cisco-webex-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l’annuaire pour lequel vous voulez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d’annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-cisco-webex-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-cisco-webex-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-cisco-webex-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Cisco Webex**.

    ![Galerie d’applications](./media/active-directory-saas-cisco-webex-tutorial/IC777615.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Cisco Webex**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Cisco Webex](./media/active-directory-saas-cisco-webex-tutorial/IC777616.png "Cisco Webex")
##Configuration de l’authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Cisco Webex avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez créer un certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, puis dans la page d’intégration d’applications **Cisco Webex**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue \*\*Configurer l’authentification unique\*\*.

    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-webex-tutorial/IC777617.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Cisco Webex**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-webex-tutorial/IC777618.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, procédez comme suit, puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-cisco-webex-tutorial/IC777619.png "Configurer l’URL de l’application")

    1.  Dans la zone de texte **URL de connexion**, tapez l’URL de votre locataire Cisco Webex (par exemple, \**http://contoso.webex.com*).
2.  Dans la zone de texte **URL de réponse Cisco Webex**, tapez votre **URL AssertionConsumerService Cisco Webex ** (par exemple, \**https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company*).

4.  Dans la page **Configurer l’authentification unique sur Cisco Webex**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-webex-tutorial/IC777620.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Cisco Webex en tant qu’administrateur.

6.  Dans le menu situé en haut, cliquez sur **Administration du site**.

    ![Administration du site](./media/active-directory-saas-cisco-webex-tutorial/IC777621.png "Administration du site")

7.  Dans la section **Gérer le site**, cliquez sur **Configuration de l’authentification unique**.

    ![Configuration de l’authentification unique](./media/active-directory-saas-cisco-webex-tutorial/IC777622.png "Configuration de l’authentification unique")

8.  Dans la section Configuration de l’authentification unique web fédérée, procédez comme suit :

    ![Configuration de l’authentification unique web fédérée](./media/active-directory-saas-cisco-webex-tutorial/IC777623.png "Configuration de l’authentification unique web fédérée")

    1.  Dans la liste **Protocole de fédération**, sélectionnez **SAML 2.0**.
    2.  Créez un fichier **codé en base 64** à partir du certificat téléchargé.  

        >[AZURE.TIP]Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    3.  Ouvrez le certificat codé en base 64 dans le Bloc-notes, puis copiez son contenu.
    4.  Cliquez sur **Importer les métadonnées SAML**, puis collez votre certificat codé en base 64.
    5.  Dans le portail Azure, dans la page de boîte de dialogue **Configurer l’authentification unique sur Cisco Webex**, copiez la valeur **URL de l’émetteur**, puis collez-la dans la zone de texte **Émetteur pour SAML (ID d’IdP)**.
    6.  Dans le portail Azure, dans la page de boîte de dialogue **Configurer l’authentification unique sur Cisco Webex**, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **URL de connexion au service d’authentification unique du client**.
    7.  Dans la liste **Format NameID**, sélectionnez **Adresse de messagerie**.
    8.  Dans la zone de texte **AuthnContextClassRef**, tapez **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**.
    9.  Dans le portail Azure, dans la page de boîte de dialogue **Configurer l’authentification unique sur Cisco Webex**, copiez la valeur **URL de déconnexion distante**, puis collez-la dans la zone de texte **URL de déconnexion du service d’authentification unique du client**.
    10. Cliquez sur **Mettre à jour**.

9.  Dans le portail Azure, dans la page de boîte de dialogue **Configurer l’authentification unique sur Cisco Webex**, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-webex-tutorial/IC777624.png "Configurer l’authentification unique")
##Configuration de l’approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à Cisco Webex, vous devez les approvisionner dans Cisco Webex. En l’occurrence, cet approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre locataire **Cisco Webex**.

2.  Accédez à **Gérer les utilisateurs > Ajouter un utilisateur**.

    ![Ajouter des utilisateurs](./media/active-directory-saas-cisco-webex-tutorial/IC777625.png "Ajouter des utilisateurs")

3.  Dans la section Ajouter un utilisateur, procédez comme suit :

    ![Ajouter un utilisateur](./media/active-directory-saas-cisco-webex-tutorial/IC777626.png "Ajouter un utilisateur")

    1.  Dans la zone **Type de compte**, sélectionnez **Hôte**.
    2.  Tapez les informations d’un utilisateur Azure AD existant dans les zones de texte suivantes : **Prénom, Nom**, **Nom d’utilisateur**, **Adresse de messagerie**, **Mot de passe**, **Confirmer le mot de passe**.
    3.  Cliquez sur **Add**.

>[AZURE.NOTE]Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Cisco Webex pour approvisionner des comptes d’utilisateur Azure Active Directory.

##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Cisco Webex, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’applications \*\*Cisco Webex\*\*, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-cisco-webex-tutorial/IC777627.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-cisco-webex-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->