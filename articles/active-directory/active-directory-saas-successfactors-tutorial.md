<properties pageTitle="Didacticiel : Intégration d’Azure AD à SuccessFactors | Microsoft Azure" description="Découvrez comment utiliser SuccessFactors avec Azure AD pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure AD à SuccessFactors
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=529792).
  
L’objectif de ce didacticiel est d’illustrer l’intégration d’Azure et de SuccessFactors en **mode d’authentification unique initiée par le fournisseur du service**. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement SuccessFactors avec authentification unique activée en mode initié par le fournisseur du service
  
À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à SuccessFactors pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise SuccessFactors (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour SuccessFactors
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-successfactors-tutorial/IC791135.png "Scénario")

##Activation de l’intégration d’applications pour SuccessFactors
  
Cette section décrit l’activation de l’intégration d’applications pour SuccessFactors.

###Pour activer l’intégration d’applications pour SuccessFactors, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-successfactors-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-successfactors-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-successfactors-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-successfactors-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **SuccessFactors**.

    ![Galerie d’applications](./media/active-directory-saas-successfactors-tutorial/IC791136.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **SuccessFactors**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![SuccessFactors](./media/active-directory-saas-successfactors-tutorial/IC791137.png "SuccessFactors")

##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur SuccessFactors avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.
  
Pour configurer l’authentification unique, vous devez contacter votre équipe de support SuccessFactors.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans la page d’intégration d’applications **SuccessFactors** du portail Azure AD, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue \*\*Configurer l’authentification unique\*\*.

    ![Configurer l’authentification unique](./media/active-directory-saas-successfactors-tutorial/IC791138.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à SuccessFactors**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-successfactors-tutorial/IC791139.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, procédez comme suit, puis cliquez sur **Suivant** :

    ![Configurer l’URL de l’application](./media/active-directory-saas-successfactors-tutorial/IC791140.png "Configurer l’URL de l’application")

    1.  Dans la zone de texte **URL de connexion à SuccessFactors**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application SuccessFactors (ex. : \**https://performancemanager4.successfactors.com/sf/home?company=CompanyName&loginMethod=SSO*").
2.  Dans la zone de texte **URL de réponse SuccessFactors**, entrez \*\***https://performancemanager4.successfactors.com/saml2/SAMLAssertionConsumer?company=CompanyName**.

        >[AZURE.NOTE]Cette valeur n’est qu’un espace réservé temporaire. La valeur réelle vous est fournie par votre équipe de support SuccessFactors. Plus loin dans ce didacticiel, vous trouverez des instructions pour contacter votre équipe de support SuccessFactors. Dans le cadre de cet échange, vous recevez votre URL de réponse SuccessFactors.

4.  Dans la page **Configurer l’authentification unique sur SuccessFactors**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-successfactors-tutorial/IC791141.png "Configurer l’authentification unique")

5.  Pour configurer l’authentification unique SQML, contactez l’équipe de support SuccessFactors et fournissez-lui les éléments suivants :

    1.  Le certificat téléchargé
    2.  L’URL de connexion distante
    3.  L’URL de déconnexion distante

    >[AZURE.IMPORTANT]Demandez à votre équipe de support SuccessFactors de régler le paramètre NameId Format sur *Unspecified*.

    Votre équipe de support Successfactors vous envoie l’**URL de réponse Successfactors** dont vous avez besoin pour la boîte de dialogue **Configurer l’URL de l’application**.

6.  Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-successfactors-tutorial/IC791142.png "Configurer l’authentification unique")

##Configuration de l'approvisionnement des utilisateurs
  
Pour se connecter à SuccessFactors, les utilisateurs d’Azure AD doivent être approvisionnés dans SuccessFactors. Dans le cas de SuccessFactors, l’approvisionnement est une tâche manuelle.
  
Pour créer les utilisateurs dans SuccessFactors, vous devez contacter l’équipe de support SuccessFactors.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à SuccessFactors, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte test.

2.  Dans la page d’intégration d’applications \*\*SuccessFactors\*\*, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-successfactors-tutorial/IC791143.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-successfactors-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->