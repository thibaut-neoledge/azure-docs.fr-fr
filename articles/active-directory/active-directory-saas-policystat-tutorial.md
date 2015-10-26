<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory avec PolicyStat | Microsoft Azure" description="Apprenez à utiliser PolicyStat avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure Active Directory à PolicyStat
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=616313).
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et PolicyStat. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire PolicyStat
  
À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à PolicyStat pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise PolicyStat (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour PolicyStat
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-policystat-tutorial/IC808662.png "Scénario")
##Activation de l’intégration d’application pour PolicyStat
  
Cette section décrit l’activation de l’intégration d’application pour PolicyStat.

###Pour activer l’intégration d’application pour PolicyStat, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-policystat-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-policystat-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-policystat-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-policystat-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **PolicyStat**.

    ![Galerie d’applications](./media/active-directory-saas-policystat-tutorial/IC808627.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **PolicyStat**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![PolicyStat](./media/active-directory-saas-policystat-tutorial/IC810430.png "PolicyStat")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur PolicyStat avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Votre application PolicyStat s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration **Attributs du jeton SAML**. La capture d’écran suivante montre un exemple :

![Attributs](./media/active-directory-saas-policystat-tutorial/IC808628.png "Attributs")

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, puis dans la page d’intégration d’application **PolicyStat**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-policystat-tutorial/IC808629.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à PolicyStat**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-policystat-tutorial/IC808630.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à PolicyStat**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application PolicyStat (par exemple : *« https://demo-azure.policystat.com »*), puis cliquez sur **Suivant**.

    ![Configurer les paramètres d’application](./media/active-directory-saas-policystat-tutorial/IC808631.png "Configurer les paramètres d’application")

4.  Dans la page **Configurer l’authentification unique sur PolicyStat**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-policystat-tutorial/IC808632.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise PolicyStat en tant qu’administrateur.

6.  Cliquez sur l’onglet **Admin**, puis sur **Single Sign-On Configuration** dans le volet de navigation de gauche.

    ![Menu Administrateur](./media/active-directory-saas-policystat-tutorial/IC808633.png "Menu Administrateur")

7.  Dans la section **Setup**, sélectionnez **Enable Single Sign-on Integration**.

    ![Configuration de l’authentification unique](./media/active-directory-saas-policystat-tutorial/IC808634.png "Configuration de l’authentification unique")

8.  Cliquez sur **Configure Attributes**, puis, dans la section **Configure Attributes**, procédez comme suit :

    ![Configuration de l’authentification unique](./media/active-directory-saas-policystat-tutorial/IC808635.png "Configuration de l’authentification unique")

    1.  Dans la zone de texte **Username Attribute**, entrez **uid**.
    2.  Dans la zone de texte **First Name Attribute**, entrez **firstname**.
    3.  Dans la zone de texte **Last Name Attribute**, entrez **lastname**.
    4.  Dans la zone de texte **Email Attribute**, entrez **emailaddress**.
    5.  Cliquez sur **Save Changes**.

9.  Cliquez sur **Your IDP Metadata**, puis, dans la section **Your IDP Metadata**, procédez comme suit :

    ![Configuration de l’authentification unique](./media/active-directory-saas-policystat-tutorial/IC808635.png "Configuration de l’authentification unique")

    1.  Ouvrez votre fichier de métadonnées téléchargé, copiez son contenu, puis collez-le dans la zone de texte **Your Identity Provider Metadata**.
    2.  Cliquez sur **Save Changes**.

10. Dans le portail Azure Active Directory, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-policystat-tutorial/IC771723.png "Configurer l’authentification unique")

11. 12. Dans le menu situé en haut, cliquez sur **Attributs** pour ouvrir la boîte de dialogue **Attributs du jeton SAML**.

    ![Attributs](./media/active-directory-saas-policystat-tutorial/IC795920.png "Attributs")

13. Pour ajouter les mappages d’attribut requis, procédez comme suit :

    ![Attributs](./media/active-directory-saas-policystat-tutorial/IC804823.png "Attributs")

    1.  Cliquez sur **Ajouter un attribut utilisateur**.
    2.  Dans la zone de texte **Nom de l’attribut**, entrez **uid**.
    3.  Dans la zone de texte **Valeur de l’attribut**, sélectionnez **ExtractMailPrefix()**.
    4.  Dans la liste **Mail**, sélectionnez **User.mail**.
    5.  Cliquez sur **Terminé**.
##Configuration de l'approvisionnement des utilisateurs
  
Pour permettre aux utilisateurs Azure AD de se connecter à PolicyStat, vous devez les approvisionner dans PolicyStat. PolicyStat prend en charge l’approvisionnement juste-à-temps des utilisateurs. Il est donc inutile d’ajouter les utilisateurs manuellement à PolicyStat. Les utilisateurs seront ajoutés automatiquement à leur première connexion à l’aide de l’authentification unique.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par PolicyStat, pour approvisionner des comptes utilisateur AAD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à PolicyStat, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’application **PolicyStat**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-policystat-tutorial/IC808636.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-policystat-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->