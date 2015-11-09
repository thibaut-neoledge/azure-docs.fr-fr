<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Jive | Microsoft Azure" 
    description="Apprenez à utiliser Jive avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Didacticiel : Intégration d’Azure Active Directory avec Jive

  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Jive. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire dans Jive
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour Jive
2.  Configuration de l'approvisionnement des utilisateurs

##Activation de l’intégration d’application pour Jive
  
Cette section décrit l’activation de l’intégration d’application pour Jive.

###Pour activer l’intégration d’application pour Jive, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-jive-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-jive-tutorial/IC700994.png "Applications")

4.  Pour ouvrir la **Galerie d’applications**, cliquez sur **Ajouter une application**, puis sur **Ajouter une application utilisable par mon organisation**.

    ![Que voulez-vous faire ?](./media/active-directory-saas-jive-tutorial/IC700995.png "Que voulez-vous faire ?")

5.  Dans la **zone de recherche**, entrez **Jive**.

    ![Jive](./media/active-directory-saas-jive-tutorial/IC701001.png "Jive")

6.  Dans le volet des résultats, sélectionnez **Jive**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Jive](./media/active-directory-saas-jive-tutorial/IC701005.png "Jive")
##Configuration de l'approvisionnement des utilisateurs
  
Cette section décrit comment activer l’approvisionnement des utilisateurs des comptes d’utilisateurs Active Directory sur Jive. Dans le cadre de cette procédure, vous devez fournir un jeton de sécurité à demander sur Jive.com.
  
La capture d’écran suivante présente un exemple de la boîte de dialogue associée dans Azure AD :

![Configurer l’approvisionnement des utilisateurs](./media/active-directory-saas-jive-tutorial/IC698794.png "Configurer l’approvisionnement des utilisateurs")

###Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1.  Dans le portail de gestion Azure, dans la page d’intégration d’application **Jive**, cliquez sur **Configurer l’approvisionnement des utilisateurs** pour ouvrir la boîte de dialogue **Configurer l’approvisionnement des utilisateurs**.

2.  Dans la page **Entrez vos informations d’identification Jive pour activer la configuration automatique d’un utilisateur**, indiquez les paramètres de configuration suivants :

    1.  Dans la zone de texte **Nom d’utilisateur admin Jive**, tapez le nom d’un compte Jive auquel le profil **System Administrator** est attribué dans Jive.com.

    2.  Dans la zone de texte **Mot de passe de l’admin Jive**, tapez le mot de passe de ce compte.

    3.  Dans la zone de texte **URL de locataire Jive**, tapez l’URL de locataire Jive.

        >[AZURE.NOTE]L’URL de locataire Jive est celle utilisée par votre organisation pour se connecter à Jive. En règle générale, l’URL a le format suivant :**www.<organisation>.jive.com**.

    4.  Cliquez sur **Valider** pour vérifier votre configuration.

    5.  Cliquez sur le bouton **Suivant** pour ouvrir la page **Confirmation**.

3.  Dans la page **Confirmation**, cliquez sur la coche pour enregistrer votre configuration.
  
Vous pouvez maintenant créer un compte de test, attendre 10 minutes, puis vérifier la synchronisation du compte à Jive.com.

<!---HONumber=Nov15_HO1-->