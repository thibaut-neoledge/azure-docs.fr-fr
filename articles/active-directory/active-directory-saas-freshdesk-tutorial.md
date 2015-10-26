<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Freshdesk | Microsoft Azure" description="Apprenez à utiliser Freshdesk avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure Active Directory à Freshdesk
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=524323).
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Freshdesk. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Freshdesk
  
À l’issue de ce didacticiel, les utilisateurs d'Azure AD que vous avez affectés à Freshdesk pourront s’authentifier de manière unique dans l'application sur votre site d'entreprise Freshdesk (connexion initiée par le fournisseur du service) ou à l'aide de la [Présentation du panneau d'accès](https://msdn.microsoft.com/library/dn308586).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour Freshdesk
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-freshdesk-tutorial/IC776761.png "Scénario")
##Activation de l’intégration d’application pour Freshdesk
  
Cette section décrit l’activation de l’intégration d’application pour Freshdesk.

###Pour activer l'intégration d’applications pour Freshdesk, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-freshdesk-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-freshdesk-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-freshdesk-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-freshdesk-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Freshdesk**.

    ![Galerie d’applications](./media/active-directory-saas-freshdesk-tutorial/IC776762.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **Freshdesk**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Freshdesk](./media/active-directory-saas-freshdesk-tutorial/IC776763.png "Freshdesk")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Freshdesk avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. La configuration de l'authentification unique pour Freshdesk oblige à récupérer une valeur d’empreinte numérique dans un certificat. Si cette procédure ne vous est pas familière, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD puis dans la page d’intégration d’applications **Freshdesk**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-freshdesk-tutorial/IC776764.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Freshdesk**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-freshdesk-tutorial/IC776765.png "Configurer l’authentification unique")

3.  Dans la page **Configuration l’URL de l'application**, dans la zone de texte **URL de connexion à Freshdesk**, tapez votre URL selon le modèle suivant « *https://\<nom\_locataire>.Freshdesk.com* », puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-freshdesk-tutorial/IC776766.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Freshdesk**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sous **c:\\Freshdesk.cer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-freshdesk-tutorial/IC776767.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Freshdesk en tant qu’administrateur.

6.  Dans le menu situé en haut, cliquez sur **Admin**.

    ![Administrateur](./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Administrateur")

7.  Dans l’onglet **General Settings**, cliquez sur **Security**.

    ![Sécurité](./media/active-directory-saas-freshdesk-tutorial/IC776769.png "Sécurité")

8.  Dans la section **Security**, procédez comme suit :

    ![Authentification unique](./media/active-directory-saas-freshdesk-tutorial/IC776770.png "Authentification unique")

    1.  Pour **Single Sign On (SSO)**, sélectionnez **On**.
    2.  Sélectionnez **SAML SSO**.
    3.  Dans la page **Configurer l’authentification unique sur Freshdesk** du portail Azure, copiez la valeur **URL de connexion distante** et collez-la dans la zone de texte **SAML Login URL**.
    4.  Dans la page **Configurer l’authentification unique sur Freshdesk** du portail Azure, copiez la valeur **URL de déconnexion distante** et collez-la dans la zone de texte **Logout URL**.
    5.  Copiez la valeur de **Empreinte** dans le certificat exporté, puis collez-la dans la zone de texte **Security Certificate Fingerprint**.  

        >[AZURE.TIP]Pour plus d’informations, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

    6.  Cliquez sur **Enregistrer**.

9.  Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-freshdesk-tutorial/IC776771.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Pour permettre aux utilisateurs Azure AD de se connecter à Freshdesk, vous devez les approvisionner dans Freshdesk. Dans le cas de Freshdesk, l’approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre locataire **Freshdesk**.

2.  Dans le menu situé en haut, cliquez sur **Admin**.

    ![Administrateur](./media/active-directory-saas-freshdesk-tutorial/IC776772.png "Administrateur")

3.  Dans l’onglet **General Settings**, cliquez sur **Agents**.

    ![Agents](./media/active-directory-saas-freshdesk-tutorial/IC776773.png "Agents")

4.  Cliquez sur **New Agent**.

    ![Nouvel agent](./media/active-directory-saas-freshdesk-tutorial/IC776774.png "Nouvel agent")

5.  Dans la boîte de dialogue Agent Information, procédez comme suit :

    ![Informations sur l’agent](./media/active-directory-saas-freshdesk-tutorial/IC776775.png "Informations sur l’agent")

    1.  Dans la zone de texte **Full Name**, tapez le nom du compte Azure AD que vous souhaitez approvisionner.
    2.  Dans la zone de texte **Email**, tapez l’adresse de messagerie Azure AD du compte Azure AD que vous souhaitez approvisionner.
    3.  Dans la zone de texte **Title**, tapez le titre du compte Azure AD que vous souhaitez approvisionner.
    4.  Sélectonnez **Agents role**, puis cliquez sur **Assign**.
    5.  Cliquez sur **Enregistrer**.
    
        >[AZURE.NOTE]Le titulaire du compte Azure AD reçoit alors un message électronique qui contient un lien pour confirmer le compte avant qu’il soit activé.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par Freshdesk, pour approvisionner des comptes utilisateur AAD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Freshdesk, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’applications **Freshdesk**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-freshdesk-tutorial/IC776776.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter** puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-freshdesk-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->