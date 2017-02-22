---
title: 'Didacticiel : Configuration de Workday pour la synchronisation entrante | Microsoft Docs'
description: "Découvrez comment utiliser Workday en tant que source de données d&quot;identité pour Azure Active Directory."
services: active-directory
author: MarkusVi
documentationcenter: na
manager: femila
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/24/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 9bf2e87353901a043f01ff7d634e1b174cd6a52a
ms.openlocfilehash: a0d86046b471dec473fd707eedae061f9d1b9902


---
# <a name="tutorial-configuring-workday-for-inbound-synchronization"></a>Didacticiel : Configuration de Workday pour la synchronisation entrante
L’objectif de ce didacticiel consiste à vous présenter les opérations à effectuer dans Workday et Azure AD pour importer des utilisateurs de Workday à Azure AD. 

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure AD Premium valide
* Un locataire dans Workday

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour Workday 
2. Création d’un utilisateur système d’intégration  
3. Création d’un groupe de sécurité 
4. Affectation de l’utilisateur système d’intégration au groupe de sécurité 
5. Configuration des options du groupe de sécurité 
6. Activation des modifications de stratégie de sécurité 
7. Configuration de l’importation d’utilisateurs dans Azure AD 

## <a name="enabling-the-application-integration-for-workday"></a>Activation de l’intégration d’application pour Workday
Cette section décrit l’activation de l’intégration d’applications pour Workday.

### <a name="steps"></a>Étapes :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-workday-inbound-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-workday-inbound-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-workday-inbound-tutorial/IC749321.png "Ajouter une application")
5. Dans la zone de recherche, tapez **Workday**.
   
    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-workday-inbound-tutorial/IC701021.png "Ajouter une application à partir de la galerie")
6. Dans le volet de résultats, sélectionnez Workday, puis cliquez sur Terminer pour ajouter l’application.
   
    ![Galerie d’applications](./media/active-directory-saas-workday-inbound-tutorial/IC701022.png "Galerie d’applications")

## <a name="creating-an-integration-system-user"></a>Création d’un utilisateur système d’intégration 
### <a name="steps"></a>Étapes :
1. Dans **Workday Workbench**, entrez Create user dans la zone de recherche, puis cliquez sur **Create Integration System User**. 
   
    ![Créer un utilisateur](./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "Créer un utilisateur")
2. Exécutez la tâche **Create Integration System User** en fournissant un nom d’utilisateur et un mot de passe pour un nouvel utilisateur du système d’intégration.  Laissez l’option Require New Password at Next Sign In désactivée, étant donné que cet utilisateur se connectera par programmation. Laissez la valeur par défaut de 0 pour l’option Session Timeout Minutes afin d’éviter que les sessions de l’utilisateur n’expirent prématurément. 
   
    ![Créer un utilisateur de système d’intégration](./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "Créer un utilisateur de système d’intégration")

## <a name="creating-a-security-group"></a>Création d’un groupe de sécurité
Pour le scénario de ce didacticiel, vous devez créer un groupe de sécurité système d’intégration sans contrainte et lui affecter l’utilisateur.

### <a name="steps"></a>Étapes :
1. Entrez create security group dans la zone de recherche, puis cliquez sur **Create Security Group**. 
   
    ![Créer un groupe de sécurité](./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "Créer un groupe de sécurité")
2. Exécutez la tâche Create Security Group.  Sélectionnez Integration System Security Group—Unconstrained dans la liste déroulante Type of Tenanted Security Group pour créer un groupe de sécurité auquel des membres seront ajoutés de manière explicite. 
   
    ![Créer un groupe de sécurité](./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "Créer un groupe de sécurité")

## <a name="assigning-the-integration-system-user-to-the-security-group"></a>Affectation de l’utilisateur système d’intégration au groupe de sécurité
### <a name="steps"></a>Étapes :
1. Entrez edit security group dans la zone de recherche, puis cliquez sur **Edit Security Group**. 
   
    ![Modifier un groupe de sécurité](./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "Modifier un groupe de sécurité")
2. Recherchez et sélectionnez le nouveau groupe de sécurité d’intégration par nom. 
   
    ![Modifier un groupe de sécurité](./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "Modifier un groupe de sécurité")
3. Ajoutez le nouvel utilisateur système d’intégration au nouveau groupe de sécurité. 
   
    ![Groupe de sécurité système](./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "Groupe de sécurité système")  

## <a name="configuring-security-group-options"></a>Configuration des options du groupe de sécurité
À cette étape, vous accordez au nouveau groupe de sécurité des autorisations pour les opérations **Get** et **Put** sur les objets sécurisés par les stratégies de sécurité de domaine suivantes :

* External Account Provisioning
* Worker Data: Public Worker Reports
* Worker Data: All Positions
* Worker Data: Current Staffing Information
* Worker Data: Business Title on Worker Profile

### <a name="steps"></a>Étapes :
1. Entrez les stratégies de sécurité de domaine dans la zone de recherche, puis cliquez sur le lien, Domain Security Policies for Functional Area.  
   
    ![Stratégies de sécurité de domaine](./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "Stratégies de sécurité de domaine")  
2. Recherchez system et sélectionnez la zone fonctionnelle **System** .  Cliquez sur **OK**.  
   
    ![Stratégies de sécurité de domaine](./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "Stratégies de sécurité de domaine")  
3. Dans la liste des stratégies de sécurité de la zone fonctionnelle System, développez Security Administration et sélectionnez la stratégie de sécurité de domaine, External Account Provisioning.  
   
    ![Stratégies de sécurité de domaine](./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "Stratégies de sécurité de domaine")  
4. Cliquez sur **Edit Permissions**, puis, sur la page de dialogue **Edit Permissions**, ajoutez le nouveau groupe de sécurité à la liste des groupes de sécurité avec autorisations d’intégration **Get** et **Put**. 
   
    ![Modifier l’autorisation](./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "Modifier l’autorisation")  
5. Répétez l’étape 1 ci-dessus pour revenir à l’écran de sélection des zones fonctionnelles. Cette fois, recherchez staffing, sélectionnez la zone fonctionnelle Staffing, puis cliquez sur **OK**.
   
    ![Stratégies de sécurité de domaine](./media/active-directory-saas-workday-inbound-tutorial/IC750990.png "Stratégies de sécurité de domaine")  
6. Dans la liste des stratégies de sécurité de la zone fonctionnelle Staffing, développez Worker Data: Staffing, et répétez l’étape 4 ci-dessus pour chacune des stratégies de sécurité restantes :
   
   * Worker Data: Public Worker Reports
   * Worker Data: All Positions
   * Worker Data: Current Staffing Information
   * Worker Data: Business Title on Worker Profile

    ![Stratégies de sécurité de domaine](./media/active-directory-saas-workday-inbound-tutorial/IC750991.png "Stratégies de sécurité de domaine")  







## <a name="activating-security-policy-changes"></a>Activation des modifications de stratégie de sécurité
### <a name="steps"></a>Étapes :
1. Entrez activate dans la zone de recherche, puis cliquez sur le lien Activate Pending Security Policy Changes. 
   
    ![Activer](./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "Activer") 
2. Commencez la tâche Activate Pending Security Policy Changes en entrant un commentaire à des fins d’audit, puis cliquez sur **OK**. 
   
    ![Activer la sécurité en attente](./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "Activer la sécurité en attente")   
3. Terminez la tâche sur l’écran suivant en cochant la case Confim, puis cliquez sur **OK**. 
   
    ![Activer la sécurité en attente](./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "Activer la sécurité en attente")  

## <a name="configuring-user-import-in-azure-ad"></a>Configuration de l’importation d’utilisateurs dans Azure AD
Cette section décrit la configuration d’Azure AD pour importer des utilisateurs à partir de Workday.

### <a name="steps"></a>Étapes :
1. Sur la page d’intégration d’application **Workday**, cliquez sur **Configurer l’importation d’utilisateurs** pour ouvrir la boîte de dialogue **Configurer l’approvisionnement**.
2. Sur la page **Paramètres et informations d’identification administrateur**, procédez comme suit, puis cliquez sur **Suivant** : 
   
    ![Paramètres et informations d’identification administrateur](./media/active-directory-saas-workday-inbound-tutorial/IC750995.png "Paramètres et informations d’identification administrateur")  
   
    a. Dans la zone de texte Nom d’utilisateur admin Workday, entrez le nom de l’utilisateur que vous avez créé dans la section Création d’un utilisateur de système d’intégration.
   
    b. Dans la zone de texte Mot de passe de l’admin Workday, entrez le mot de passe de l’utilisateur que vous avez créé dans la section Création d’un utilisateur de système d’intégration.
   
    c. Dans la zone de texte URL de locataire Workday, entrez l’URL de votre locataire Workday.
3. Sur la page **Tester la connexion**, cliquez sur **Démarrer le test** pour vérifier la connectivité, puis cliquez sur **Suivant**. 
   
    ![Tester la connexion](./media/active-directory-saas-workday-inbound-tutorial/IC750996.png "Tester la connexion")  
4. Sur la page **Options d’approvisionnement**, cliquez sur **Suivant**. 
   
    ![Options d’approvisionnement](./media/active-directory-saas-workday-inbound-tutorial/IC750997.png "Options d’approvisionnement")
5. Dans la boîte de dialogue **Démarrer l’approvisionnement**, cliquez sur **Terminer**. 
   
    ![Démarrer l’approvisionnement](./media/active-directory-saas-workday-inbound-tutorial/IC750998.png "Démarrer l’approvisionnement")

Vous pouvez maintenant accéder à la section **Utilisateurs** et vérifier si votre utilisateur Workday a été importé.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)




<!--HONumber=Jan17_HO4-->


