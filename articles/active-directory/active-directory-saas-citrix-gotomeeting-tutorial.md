<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory à Citrix GoToMeeting | Microsoft Azure" description="Apprenez à utiliser Citrix GoToMeeting avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure Active Directory à Citrix GoToMeeting  
S’applique à : Azure

>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=522412).

L’objectif de ce didacticiel est de montrer comment intégrer Azure et Citrix GoToMeeting. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire dans Citrix GoToMeeting

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Citrix GoToMeeting
2.  Configuration de l’authentification unique
3.  Configuration de l’approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Configuration](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "Configuration")
##Activation de l’intégration d’applications pour Citrix GoToMeeting

Cette section décrit l’activation de l’intégration d’applications pour Citrix GoToMeeting.

###Pour activer l’intégration d’applications pour Citrix GoToMeeting, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l’annuaire pour lequel vous voulez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d’annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Citrix GoToMeeting**.

    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701006.png "Citrix GoToMeeting")

7.  Dans le volet de résultats, sélectionnez **Citrix GoToMeeting**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701012.png "Citrix GoToMeeting")
##Configuration de l’authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Citrix GoToMeeting avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez télécharger un certificat codé en base 64 sur votre locataire Citrix GoToMeeting. Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans la page d’intégration d’applications **Citrix GoToMeeting**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **CONFIGURER L’AUTHENTIFICATION UNIQUE**.

    ![Activer l’authentification unique](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "Activer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Citrix GoToMeeting**, sélectionnez **Authentification unique Microsoft Azure AD**.

    ![Configurer l’authentification unique](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’authentification unique sur Citrix GoToMeeting**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "Configurer l’authentification unique")

4.  Dans une autre fenêtre de navigateur, connectez-vous à votre locataire Citrix GoToMeeting en tant qu’administrateur.

5.  Ouvrez la page [Configurer l’authentification unique (SSO) SAML 2.0](https://login.citrixonline.com/saml/settings.html), puis procédez comme suit :

    ![Configuration de SAML](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC689232.png "Configuration de SAML")

    1.  Sélectionnez **Configurer manuellement**.
    2.  Dans le portail Azure, dans la page de boîte de dialogue **Configurer l’authentification unique sur Citrix GoToMeeting**, copiez la valeur **URL de la page de déconnexion**, puis collez-la dans la zone de texte **URL de la page de déconnexion**.
    3.  Dans le portail Azure, dans la page de boîte de dialogue **Configurer l’authentification unique sur Citrix GoToMeeting**, copiez la valeur **URL de la page de connexion**, puis collez-la dans la zone de texte **URL de la page de connexion**.
    4.  Créez un fichier **codé en base 64** à partir du certificat téléchargé.  

        >[AZURE.TIP]Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    5.  Cliquez sur **Remplacer le certificat**, puis téléchargez votre **fichier de certificat codé en base 64**.
    6.  Cliquez sur **Enregistrer**.

6.  Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "Configurer l’authentification unique")
##Configuration de l’approvisionnement des utilisateurs

Cette section décrit l’activation de l’approvisionnement des comptes d’utilisateur Active Directory sur Citrix GoToMeeting.

###Pour configurer l’approvisionnement des utilisateurs, procédez comme suit :

1.  Dans le portail de gestion Azure, dans la page d’intégration d’applications **Citrix GoToMeeting**, cliquez sur **Effectuer la configuration de l’utilisateur** pour ouvrir la boîte de dialogue **Effectuer la configuration de l’utilisateur**.

    ![Configurer l’approvisionnement des utilisateurs](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "Configurer l’approvisionnement des utilisateurs")

2.  Dans la page **Nom d’utilisateur administrateur de Citrix GotoMeeting**, tapez le nom d’un compte Citrix GoToMeeting disposant de droits d’administration sur votre locataire Citrix GoToMeeting, puis cliquez sur **Suivant**.

    ![Configurer l’approvisionnement des utilisateurs](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "Configurer l’approvisionnement des utilisateurs")

3.  Dans la page **Confirmation**, cliquez sur la coche pour enregistrer votre configuration.

4.  Cliquez sur le bouton **valider** pour vérifier votre configuration.
##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Citrix GoToMeeting, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’applications **Citrix GoToMeeting**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "Oui")

Vous devez maintenant attendre 10 minutes et vérifier que le compte a été synchronisé avec Dropbox for Business.

Une première étape de vérification consiste à contrôler l’état d’approvisionnement en cliquant sur le tableau de bord dans la page d’intégration d’applications **Citrix GoToMeeting** dans le portail de gestion Azure.

![Tableau de bord](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "Tableau de bord")

Si le cycle d’approvisionnement d’utilisateur a abouti, l’état associé suivant est indiqué :

![Statut d’intégration](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "Statut d’intégration")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès.

Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->