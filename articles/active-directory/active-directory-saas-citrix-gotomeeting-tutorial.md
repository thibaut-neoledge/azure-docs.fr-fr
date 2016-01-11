<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory à Citrix GoToMeeting | Microsoft Azure" 
    description="Apprenez à utiliser Citrix GoToMeeting avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>

<tags 
    ms.service="active-directory" 
    ms.devlang="na" ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="12/18/2015" 
    ms.author="jeedes" />

#Didacticiel : Intégration d’Azure Active Directory à Citrix GoToMeeting  
S’applique à : Azure

>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=522412).

L’objectif de ce didacticiel est de montrer comment intégrer Azure et Citrix GoToMeeting. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire dans Citrix GoToMeeting

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Citrix GoToMeeting
2.  Configuration de l’authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Configuration](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "Configuration")
##Activation de l’intégration d’applications pour Citrix GoToMeeting

Cette section décrit l’activation de l’intégration d’applications pour Citrix GoToMeeting.

###Pour activer l’intégration d’applications pour Citrix GoToMeeting, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

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

    ![Activer l’authentification unique](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "Activer l'authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Citrix GoToMeeting**, sélectionnez **Authentification unique Microsoft Azure AD**.

    ![Configurer l’authentification unique](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "Configurer l’authentification unique")


3. Dans la page **Configurer les paramètres de l’application**, cliquez sur **Suivant**. <br><br>![Activer l’authentification unique](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689981.png "Activer l'authentification unique")

4.  Dans la page **Configurer l’authentification unique sur Citrix GoToMeeting**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur, ouvrez une session dans votre centre d’organisation Citrix ([https://account.citrixonline.com/organization/administration/](https://account.citrixonline.com/organization/administration/)).

6. Cliquez sur l’onglet **Identity Provider**, puis effectuez les opérations suivantes : <br><br> ![Configuration de SAML](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC6892321.png "Configuration de SAML")

     6\.1. Sélectionnez **Manual**.

     6\.2. Dans le portail Azure, dans la page de boîte de dialogue **Configurer l’authentification unique sur Citrix GoToMeeting**, copiez la valeur **URL de la page de connexion**, puis collez-la dans la zone de texte **Sign-in page URL**.

     6\.3. Dans le portail Azure, dans la page de boîte de dialogue **Configurer l’authentification unique sur Citrix GoToMeeting**, copiez la valeur **URL de la page de déconnexion**, puis collez-la dans la zone de texte **Sign-out page URL**.

     6\.4. Dans le portail Azure, dans la page **Configurer l’authentification unique sur Citrix GoToMeeting**, copiez la valeur **ID d’identité**, puis collez-la dans la zone de texte **Identity Provider Entity ID**.

     6\.5. Pour charger votre certificat téléchargé, cliquez sur **Upload Certificate**.

     6\.6. Cliquez sur **Enregistrer**.

6.  Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "Configurer l’authentification unique")


7. Dans la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**. <br><br>![Configuration de SAML](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689982.png "Configuration de SAML")





##Configuration de l'approvisionnement des utilisateurs

Cette section décrit l’activation de l’approvisionnement des comptes d’utilisateur Active Directory sur Citrix GoToMeeting.

###Pour configurer l’approvisionnement des utilisateurs, procédez comme suit :

1.  Dans le Portail de gestion Azure, dans la page d’intégration d’applications **Citrix GoToMeeting**, cliquez sur **Effectuer la configuration de l’utilisateur** pour ouvrir la boîte de dialogue **Effectuer la configuration de l’utilisateur**.

    ![Configurer l’approvisionnement des utilisateurs](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "Configurer l’approvisionnement des utilisateurs")

2.  Dans la page **Paramètres et informations d’identification administrateur**, procédez comme suit :

    ![Configurer l’approvisionnement des utilisateurs](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "Configurer l’approvisionnement des utilisateurs")

     2\.1. Dans la zone de texte **Nom d’utilisateur admin Citrix GoToMeeting**, tapez le nom d’utilisateur d’un administrateur.

     2\.2. Dans la zone de texte **Mot de passe de l’admin Citrix GoToMeeting**, tapez le mot de passe de l’administrateur.

     2\.3. Cliquez sur **Next**.

3.  Dans la page **Confirmation**, cliquez sur la coche pour enregistrer votre configuration.

4.  Cliquez sur le bouton **Valider** pour vérifier votre configuration.
##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Citrix GoToMeeting, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’applications **Citrix GoToMeeting**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter** puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "Oui")

Vous devez maintenant attendre 10 minutes et vérifier que le compte a été synchronisé avec Dropbox for Business.

Une première étape de vérification consiste à contrôler l’état d’approvisionnement en cliquant sur le tableau de bord dans la page d’intégration d’applications **Citrix GoToMeeting** dans le Portail de gestion Azure.

![Tableau de bord](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "Tableau de bord")

Si le cycle d’approvisionnement d’utilisateur a abouti, l’état associé suivant est indiqué :

![Statut d’intégration](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "Statut d’intégration")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès.

Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=AcomDC_1223_2015-->