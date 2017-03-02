---
title: "Didacticiel : Intégration d’Azure Active Directory à Druva | Microsoft Docs"
description: "Apprenez à utiliser Druva avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: 0c24b01924d5fcec0d4600e044e679cfc295815f
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-integration-with-druva"></a>Didacticiel : Intégration d’Azure Active Directory à Druva
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Druva.  

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un abonnement Druva pour lequel l’authentification unique (SSO) est activée

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Druva pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Druva (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

* Activation de l’intégration d’applications pour Druva
* Configuration de l'authentification unique
* Configuration de l'approvisionnement des utilisateurs
* Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-druva-tutorial/IC795084.png "Scénario")

## <a name="enable-the-application-integration-for-druva"></a>Activer l’intégration d’applications pour Druva
Cette section décrit l’activation de l’intégration d’applications pour Druva.

**Pour activer l’intégration d’applications pour Druva, suivez les étapes ci-dessous :**

1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-druva-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-druva-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-druva-tutorial/IC749321.png "Ajouter une application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-druva-tutorial/IC749322.png "Ajouter une application à partir de la galerie")
6. Dans la **zone de recherche**, tapez **Druva**.
   
   ![Galerie d’applications](./media/active-directory-saas-druva-tutorial/IC795085.png "Galerie d’applications")
7. Dans le volet des résultats, sélectionnez **Druva**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![Druva](./media/active-directory-saas-druva-tutorial/IC795086.png "Druva")
   
## <a name="configure-single-sign-on"></a>Configurer l’authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Druva avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  

Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

Votre application Druva attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration **Attributs du jeton SAML** . 

La capture d’écran suivante montre un exemple :

![SAML Token Attributes](./media/active-directory-saas-druva-tutorial/IC795087.png "SAML Token Attributes")

**Pour configurer l’authentification unique, procédez comme suit :**

1. Sur la page d’intégration d’applications **Druva** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-druva-tutorial/IC795027.png "Configurer l’authentification unique")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Druva**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-druva-tutorial/IC795088.png "Configurer l’authentification unique")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Druva**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Druva (par exemple, *https://cloud.druva.com/home/*), puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-druva-tutorial/IC795089.png "Configurer l’URL de l’application")
4. Dans la page **Configurer l’authentification unique sur Druva**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-druva-tutorial/IC795090.png "Configurer l’authentification unique")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Druva en tant qu’administrateur.
6. Accédez à **Manage \> Settings**.
   
   ![Paramètres](./media/active-directory-saas-druva-tutorial/IC795091.png "Paramètres")
7. Dans la boîte de dialogue Single Sign-On Settings, procédez comme suit :
   
   ![Single Sign-On Settings](./media/active-directory-saas-druva-tutorial/IC795092.png "Single Sign-On Settings")   
   1. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Druva** de la boîte de dialogue, copiez la valeur **URL de connexion distante** et collez-la dans la zone de texte **ID Provider Login URL**.
   2. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Druva**, copiez la valeur **URL de déconnexion distante** et collez-la dans la zone de texte **ID Provider Logout URL**.
   3. Créez un fichier **codé en base 64** à partir du certificat téléchargé.  
     >[!TIP]
     >Pour plus d’informations, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o)
     > 
   4. Ouvrez votre certificat codé en base 64 dans le bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **ID Provider Certificate** .
   5. Pour ouvrir la page **Settings**, cliquez sur**Save**.
8. Dans la page **Settings**, cliquez sur **Generate SSO Token**.
   
   ![Paramètres](./media/active-directory-saas-druva-tutorial/IC795093.png "Paramètres")
9. Dans la boîte de dialogue **Single Sign-on Authentication Token** , procédez comme suit :
   
   ![SSO Token](./media/active-directory-saas-druva-tutorial/IC795094.png "SSO Token")
   1. Cliquez sur **Copy**.
   2. Cliquez sur **Fermer**.
10. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
    
   ![Configurer l’authentification unique](./media/active-directory-saas-druva-tutorial/IC795095.png "Configurer l’authentification unique")
11. Dans le menu situé en haut, cliquez sur **Attributs** to open the **SAML Token Attributs** .
    
    ![Attributs](./media/active-directory-saas-druva-tutorial/IC795096.png "Attributs")
12. Pour ajouter les mappages d’attribut requis, procédez comme suit :
    
   | Nom de l'attribut | Valeur de l’attribut |
   | --- | --- |
   | insync\_auth\_token |<*valeur du Presse-papiers*> |
    
   1. Pour chaque ligne de données dans le tableau ci-dessus, cliquez sur **Ajouter un attribut utilisateur**.
   2. Dans la zone de texte **Nom de l’attribut** , indiquez le nom d’attribut pour cette ligne.
   3. Dans la zone de texte **Valeur de l’attribut** , tapez la valeur d’attribut pour cette ligne.
   4. Cliquez sur **Terminé**.
13. Cliquez sur **Appliquer les modifications**.
    
## <a name="configure-user-provisioning"></a>Configurer l'approvisionnement de l'utilisateur

Pour se connecter à Druva, les utilisateurs d’Azure AD doivent être approvisionnés dans Druva. 

* Dans le cas de Druva, l’approvisionnement est une tâche manuelle.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **Druva** en tant qu’administrateur.
2. Accédez à **Gérer \> Utilisateurs**.
   
   ![Gestion des utilisateurs](./media/active-directory-saas-druva-tutorial/IC795097.png "Gestion des utilisateurs")
3. Cliquez sur **Create New**.
   
   ![Gestion des utilisateurs](./media/active-directory-saas-druva-tutorial/IC795098.png "Gestion des utilisateurs")
4. Dans la boîte de dialogue Create New User, procédez comme suit :
   
   ![Create NewUser](./media/active-directory-saas-druva-tutorial/IC795099.png "Create NewUser")
   
   1. Indiquez l’adresse e-mail et le nom d’un compte d’utilisateur Azure Active Directory valide que vous souhaitez approvisionner dans les zones de texte correspondantes.
   2. Cliquez sur **Create User**.

>[!NOTE]
>Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur fourni par Druva pour approvisionner des comptes d’utilisateurs AAD.
> 
> 

## <a name="assign-users"></a>Affecter des utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

**Pour affecter des utilisateurs à Druva, suivez les étapes ci-dessous :**

1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’applications **Druva**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-druva-tutorial/IC795100.png "Affecter des utilisateurs")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-druva-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


