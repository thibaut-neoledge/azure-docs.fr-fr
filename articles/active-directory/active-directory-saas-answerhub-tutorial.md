<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory à AnswerHub | Microsoft Azure" description="Apprenez à utiliser AnswerHub avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore !" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure Active Directory à AnswerHub
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=528077).

L’objectif de ce didacticiel est de montrer comment intégrer Azure et AnswerHub. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement AnswerHub pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à AnswerHub pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise AnswerHub (connexion initiée par le fournisseur du service) ou en s’appuyant sur la [Présentation du volet d’accès](https://msdn.microsoft.com/library/dn308586).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour AnswerHub
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-answerhub-tutorial/IC785165.png "Scénario")
##Activation de l’intégration d’application pour AnswerHub

Cette section décrit l’activation de l’intégration d’application pour AnswerHub.

###Pour activer l’intégration d’application pour AnswerHub, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-answerhub-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-answerhub-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-answerhub-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-answerhub-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **AnswerHub**.

    ![Galerie d’applications](./media/active-directory-saas-answerhub-tutorial/IC785166.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **AnswerHub**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![AnswerHub](./media/active-directory-saas-answerhub-tutorial/IC785167.png "AnswerHub")
##Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur AnswerHub avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, dans la page d’intégration d’application **AnswerHub**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue \*\*Configurer l’authentification unique\*\*.

    ![Configurer l’authentification unique](./media/active-directory-saas-answerhub-tutorial/IC785168.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à AnswerHub**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-answerhub-tutorial/IC785169.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l'application**, dans la zone de texte **URL de connexion de AnswerHub**, tapez votre URL selon le modèle "\**https://company.answerhub.com*", puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-answerhub-tutorial/IC785170.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur AnswerHub**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-answerhub-tutorial/IC785171.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise AnswerHub en tant qu’administrateur.

6.  Accédez à **Administration**.

7.  Cliquez sur l’onglet **Users and Groups**.

8.  Dans le volet de navigation situé sur le côté gauche, dans la section **Social Settings**, cliquez sur**SAML Setup**.

9.  Cliquez sur l’onglet **IDP Config**.

10. Sous l’onglet **IDP Config**, procédez comme suit :

    ![SAML Setup](./media/active-directory-saas-answerhub-tutorial/IC785172.png "SAML Setup")

    1.  Dans le portail Azure, dans la page **Configurer l’authentification unique sur AnswerHub**, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **IDP Login URL**.
    2.  Dans le portail Azure, dans la page **Configurer l’authentification unique sur AnswerHub**, copiez la valeur **URL de déconnexion distante**, puis collez-la dans la zone de texte **IDP Logout URL**.
    3.  Dans le portail Azure, dans la page **Configurer l’authentification unique sur AnswerHub**, copiez la valeur **Format de l’identification du nom**, puis collez-la dans la zone de texte **IDP Name Identifier Format**.
    4.  Cliquez sur **Keys and Certificates**.

11. Sous l’onglet Keys and Certificates, procédez comme suit :

    ![Keys and Certificates](./media/active-directory-saas-answerhub-tutorial/IC785173.png "Keys and Certificates")

    1.  Créez un fichier **codé en base 64** à partir du certificat téléchargé.  

		>[AZURE.TIP]Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    2.  Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **IDP Public Key (x509 Format)**.
    3.  Cliquez sur **Save**.

12. Sous l’onglet **IDP Config**, cliquez sur **Save**.

13. Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-answerhub-tutorial/IC785174.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à AnswerHub, vous devez les approvisionner dans AnswerHub. Dans le cas d’AnswerHub, cet approvisionnement est une tâche manuelle.

###Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **AnswerHub** en tant qu’administrateur.

2.  Accédez à **Administration**.

3.  Cliquez sur l’onglet **Users & Groups**.

4.  Dans le volet de navigation situé sur le côté gauche, dans la section **Manage Users**, cliquez sur **Create or import users**.

    ![Users & Groups](./media/active-directory-saas-answerhub-tutorial/IC785175.png "Users & Groups")

5.  Tapez l’adresse électronique, le nom d’utilisateur et le mot de passe du compte d’utilisateur Azure Active Directory valide que vous souhaitez approvisionner dans les zones de texte correspondantes, à savoir, **Email address**, **Username** et **Password**, puis cliquez sur **Save**.

>[AZURE.NOTE]Vous pouvez utiliser n'importe quel autre outil ou API de création de compte d’utilisateur AnswerHub fourni par ce service pour approvisionner des comptes utilisateurs AAD.

##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à AnswerHub, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’application \*\*AnswerHub\*\*, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-answerhub-tutorial/IC785176.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter** puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-answerhub-tutorial/IC767830.png "Oui")

Si vous voulez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez la page [Présentation du volet d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->