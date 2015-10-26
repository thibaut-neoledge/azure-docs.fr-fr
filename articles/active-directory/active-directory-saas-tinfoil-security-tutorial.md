<properties pageTitle="Didacticiel : Intégration d’Azure AD à Tinfoil Security | Microsoft Azure" description="Découvrez comment utiliser Tinfoil Security avec Azure AD pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure AD à Tinfoil Security
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=613055).
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Tinfoil Security. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Tinfoil Security pour lequel l’authentification unique est activée
  
À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Tinfoil Security pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Tinfoil Security (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Tinfoil Security
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Configurer l’authentification unique](./media/active-directory-saas-tinfoil-security-tutorial/IC798965.png "Configurer l’authentification unique")

##Activation de l’intégration d’applications pour Tinfoil Security
  
Cette section décrit l’activation de l’intégration d’applications pour Tinfoil Security.

###Pour activer l’intégration d’applications pour Tinfoil Security, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-tinfoil-security-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-tinfoil-security-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-tinfoil-security-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-tinfoil-security-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **Tinfoil Security**.

    ![Galerie d’applications](./media/active-directory-saas-tinfoil-security-tutorial/IC798966.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **Tinfoil Security**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Tinfoil Security](./media/active-directory-saas-tinfoil-security-tutorial/IC802771.png "Tinfoil Security")

##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Tinfoil Security avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. La configuration de l’authentification unique pour Tinfoil Security oblige à récupérer une valeur d’empreinte numérique dans un certificat. Si vous n’êtes pas familiarisé avec cette procédure, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/YKQF266SAxI).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans la page d’intégration d’applications **Tinfoil Security** du portail Azure AD, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-tinfoil-security-tutorial/IC798967.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Tinfoil Security**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-tinfoil-security-tutorial/IC798968.png "Configurer l’authentification unique")

3.  Dans la zone de texte **URL de réponse de Tinfoil Security** de la page **Configurer l’URL de l’application**, entrez l’URL de votre service ACS (Assertion Consumer Service) Tinfoil Security (par exemple, « **https://www.tinfoilsecurity.com/saml/consume*", puis cliquez sur **Suivant**.

    >[AZURE.NOTE]Vous devez pouvoir obtenir l’URL d’ACS dans les métadonnées de Tinfoil Security (https://www.tinfoilsecurity.com/saml/metadata).

    ![Configurer l’URL de l’application](./media/active-directory-saas-tinfoil-security-tutorial/IC798969.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Tinfoil Security**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sous le nom **c:\\Tinfoil Security.cer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-tinfoil-security-tutorial/IC798970.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Tinfoil Security en tant qu’administrateur.

6.  Dans la barre d’outils située en haut, cliquez sur **My Account**.

    ![Tableau de bord](./media/active-directory-saas-tinfoil-security-tutorial/IC798971.png "Tableau de bord")

7.  Cliquez sur **Security**.

    ![Sécurité](./media/active-directory-saas-tinfoil-security-tutorial/IC798972.png "Sécurité")

8.  Dans la page de configuration **Single Sign on**, procédez comme suit :

    ![Authentification unique](./media/active-directory-saas-tinfoil-security-tutorial/IC798973.png "Authentification unique")

    1.  Sélectionnez **Enable SAML**.
    2.  Cliquez sur **Manual Configuration**.
    3.  Dans la boîte de dialogue **Configurer l’authentification unique sur Tinfoil Security** du portail Azure, copiez la valeur de **URL SSO SAML** et collez-la dans la zone de texte **URL de publication SAML**.
    4.  Copiez la valeur de **Empreinte** dans le certificat exporté, puis collez-la dans la zone de texte **Empreinte du certificat SAML**.  

        >[AZURE.TIP]Pour plus d’informations, consultez [Récupération de la valeur d’empreinte d’un certificat](http://youtu.be/YKQF266SAxI).

    5.  Copiez **Votre ID de compte**.
    6.  Cliquez sur **Enregistrer**.

9.  Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-tinfoil-security-tutorial/IC798974.png "Configurer l’authentification unique")

10. Dans le menu situé en haut, cliquez sur **Attributs** pour ouvrir la boîte de dialogue **Attributs du jeton SAML**.

    ![Attributs](./media/active-directory-saas-tinfoil-security-tutorial/IC795920.png "Attributs")

11. Pour ajouter les mappages d’attribut requis, procédez comme suit :

    ![Attributs](./media/active-directory-saas-tinfoil-security-tutorial/IC798975.png "Attributs")

    1.  Cliquez sur **Ajouter un attribut utilisateur**.
    2.  Dans la zone de texte **Nom de l’attribut**, entrez **accountid**.
    3.  Dans la zone de texte **Valeur de l’attribut**, collez l’ID de compte que vous avez copié dans la section précédente.
    4.  Cliquez sur **Terminé**.

12. Cliquez sur **Appliquer les modifications**.

##Configuration de l'approvisionnement des utilisateurs
  
Pour se connecter à Tinfoil Security, les utilisateurs d’Azure AD doivent être approvisionnés dans Tinfoil Security. Dans le cas de Tinfoil Security, l’approvisionnement est une tâche manuelle.

###Pour configurer l’approvisionnement des utilisateurs, procédez comme suit :

1.  Si l’utilisateur fait partie d’un compte d’entreprise, vous devez contacter l’équipe de support Tinfoil Security pour obtenir le compte d’utilisateur créé.

2.  Si l’utilisateur est un utilisateur normal de SaaS Tinfoil Security, l’utilisateur peut ajouter un collaborateur à n’importe quel site de l’utilisateur. Cela déclenche l’envoi à l’adresse de messagerie spécifiée, d’une invitation à créer un compte utilisateur Tinfoil Security.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par Tinfoil Security, pour approvisionner des comptes d’utilisateur AAD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Tinfoil Security, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte test.

2.  Dans la page d’intégration d’applications Tinfoil Security, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-tinfoil-security-tutorial/IC798976.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-tinfoil-security-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->