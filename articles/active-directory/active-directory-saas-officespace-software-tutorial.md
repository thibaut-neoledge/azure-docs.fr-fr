---
title: 'Didacticiel : Intégration d’Azure Active Directory avec OfficeSpace Software | Microsoft Docs'
description: Apprenez à utiliser OfficeSpace Software avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/08/2016
ms.author: jeedes

---
# Didacticiel : Intégration d’Azure Active Directory avec OfficeSpace Software
L’objectif de ce didacticiel est de montrer comment intégrer Azure et OfficeSpace Software. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement OfficeSpace Software pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à OfficeSpace Software pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise OfficeSpace Software (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour OfficeSpace Software
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-officespace-software-tutorial/IC777764.png "Scénario")

## Activation de l’intégration d’application pour OfficeSpace Software
Cette section décrit l’activation de l’intégration d’application pour OfficeSpace Software.

### Pour activer l’intégration d’application pour OfficeSpace Software, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-officespace-software-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-officespace-software-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter l’application](./media/active-directory-saas-officespace-software-tutorial/IC749321.png "Ajouter l’application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-officespace-software-tutorial/IC749322.png "Ajouter une application à partir de la galerie")
6. Dans la **zone de recherche**, entrez **OfficeSpace Software**.
   
   ![Galerie d’applications](./media/active-directory-saas-officespace-software-tutorial/IC777765.png "Galerie d’applications")
7. Dans le volet des résultats, sélectionnez **OfficeSpace Software**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![OfficeSpace Software](./media/active-directory-saas-officespace-software-tutorial/IC781007.png "OfficeSpace Software")
   
   ## Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur OfficeSpace Software avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. La configuration de l’authentification unique pour OfficeSpace Software nécessite de récupérer une valeur d’empreinte numérique dans un certificat. Si cette procédure ne vous est pas familière, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

### Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, dans la page d’intégration d’application **OfficeSpace Software**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-officespace-software-tutorial/IC777766.png "Configurer l’authentification unique")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à OfficeSpace Software**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-officespace-software-tutorial/IC777767.png "Configurer l’authentification unique")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à OfficeSpace Software**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application OfficeSpace Software (par exemple, « *https://company.officespacesoftware.com*"), puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-officespace-software-tutorial/IC775556.png "Configurer l’URL de l’application")
4. Dans la page **Configurer l’authentification unique sur OfficeSpace Software**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-officespace-software-tutorial/IC793769.png "Configurer l’authentification unique")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise OfficeSpace Software en tant qu’administrateur.
6. Accédez à **Admin > Connectors**.
   
   ![Administrateur](./media/active-directory-saas-officespace-software-tutorial/IC777769.png "Administrateur")
7. Cliquez sur **SAML Authorization**.
   
   ![Connecteurs](./media/active-directory-saas-officespace-software-tutorial/IC777770.png "Connecteurs")
8. Dans la section **SAML Authentication**, procédez comme suit :
   
   ![Configuration de SAML](./media/active-directory-saas-officespace-software-tutorial/IC777771.png "Configuration de SAML")
   
   1. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur OfficeSpace Software**, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **Logout provider url**.
   2. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur OfficeSpace Software**, copiez la valeur **URL de déconnexion distante**, puis collez-la dans la zone de texte **Client idp target url**.
   3. Copiez la valeur **Empreinte numérique** du certificat exporté, puis collez-la dans la zone de texte **Client idp cert fingerprint**.
      
      > [!TIP]
      > Pour plus d’informations, consultez [Récupération de la valeur de l’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).
      > 
      > 
   4. Cliquez sur **Save Settings**.
9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-officespace-software-tutorial/IC777772.png "Configurer l’authentification unique")
   
   ## Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à OfficeSpace Software, vous devez les approvisionner dans OfficeSpace Software. Dans le cas de OfficeSpace Software, l’approvisionnement est une tâche automatique. Vous n’avez rien à faire. Au besoin, les utilisateurs sont automatiquement créés lors de la première tentative d’authentification unique.

> [!NOTE]
> Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par OfficeSpace Software pour approvisionner des comptes d’utilisateur Azure Active Directory.
> 
> 

## Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### Pour affecter des utilisateurs à OfficeSpace Software, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **OfficeSpace Software**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-officespace-software-tutorial/IC777773.png "Affecter des utilisateurs")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-officespace-software-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->