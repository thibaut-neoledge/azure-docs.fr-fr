---
title: 'Didacticiel : Configuration de Workday pour la synchronisation entrante | Microsoft Docs'
description: Apprenez à utiliser la synchronisation entrante avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/06/2016
ms.author: jeedes

---
# <a name="tutorial:-configuring-workday-for-inbound-synchronization"></a>Didacticiel : Configuration de Workday pour la synchronisation entrante
> [!NOTE]
> Les clients vivant en Chine peuvent accéder à Azure Active Directory Premium à l’aide de l’instance mondiale d’Azure AD.    
> Actuellement, Azure AD Premium n’est pas pris en charge dans le service Microsoft Azure fonctionnant avec 21Vianet en Chine.    
> 
> 

L’objectif de ce didacticiel consiste à vous présenter les opérations à effectuer dans Workday et Microsoft Azure AD pour importer des utilisateurs de Workday à Microsoft Azure AD.    
 Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :  

* Un abonnement Azure valide  
* Un locataire dans Workday  

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :  

1. Activation de l’intégration d’application pour Workday  
2. Création d’un utilisateur système d’intégration   
3. Création d’un groupe de sécurité  
4. Affectation de l’utilisateur système d’intégration au groupe de sécurité  
5. Configuration des options du groupe de sécurité  
6. Activation des modifications de stratégie de sécurité  
7. Configuration de l’importation d’utilisateurs dans Microsoft Azure AD  

## <a name="enabling-the-application-integration-for-workday"></a>Activation de l’intégration d’application pour Workday
Cette section décrit l’activation de l’intégration d’application pour Workday.    

### <a name="to-enable-the-application-integration-for-workday,-perform-the-following-steps:"></a>Pour activer l’intégration d’application pour Workday, procédez comme suit :
1. Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.    
   
   ![Active Directory](./media/active-directory-saas-inbound-synchronization-tutorial/IC700993.png "Active Directory")  
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.    
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.    
   
   ![Applications](./media/active-directory-saas-inbound-synchronization-tutorial/IC700994.png "Applications")  
4. Pour ouvrir la **Galerie d’applications**, cliquez sur **Ajouter une application**, puis sur **Ajouter une application utilisable par mon organisation**.    
   
   ![Que voulez-vous faire ?](./media/active-directory-saas-inbound-synchronization-tutorial/IC700995.png "What do you want to do?")  
5. Dans la **zone de recherche**, entrez **Workday**.    
   
   ![Workday](./media/active-directory-saas-inbound-synchronization-tutorial/IC701021.png "Workday")  
6. Dans le volet de résultats, sélectionnez **Workday**, puis cliquez sur **Terminer** pour ajouter l’application.    
   
   ![Workday](./media/active-directory-saas-inbound-synchronization-tutorial/IC701022.png "Workday")  

## <a name="creating-an-integration-system-user"></a>Création d’un utilisateur système d’intégration
1. Dans **Workday Workbench**, entrez **create user** dans la zone de recherche, puis cliquez sur le lien **Create Integration System User**.     
   
   ![créer un utilisateur](./media/active-directory-saas-inbound-synchronization-tutorial/IC750979.png "create user")  
2. Exécutez la tâche Create Integration System User en fournissant un nom d’utilisateur et un mot de passe pour un nouvel utilisateur système d’intégration.  Laissez l’option Require New Password at Next Sign In désactivée, étant donné que cet utilisateur se connectera par programmation.    
   Laissez la valeur par défaut de 0 pour l’option Session Timeout Minutes afin d’éviter que les sessions de l’utilisateur n’expirent prématurément.    
   
   ![Create Integration System User](./media/active-directory-saas-inbound-synchronization-tutorial/IC750980.png "Create Integration System User")  

## <a name="creating-a-security-group"></a>Création d’un groupe de sécurité
Pour le scénario de ce didacticiel, vous devez créer un groupe de sécurité système d’intégration sans contrainte et lui affecter l’utilisateur.    

1. Entrez create security group dans la zone de recherche, puis cliquez sur le lien, Create Security Group.     
   
   ![Créer un groupe de sécurité](./media/active-directory-saas-inbound-synchronization-tutorial/IC750981.png "CreateSecurity Group")  
2. Exécutez la tâche Create Security Group.  Sélectionnez Integration System Security Group—Unconstrained dans la liste déroulante Type of Tenanted Security Group pour créer un groupe de sécurité auquel des membres seront ajoutés de manière explicite.     
   
   ![Créer un groupe de sécurité](./media/active-directory-saas-inbound-synchronization-tutorial/IC750982.png "CreateSecurity Group")  

## <a name="assigning-the-integration-system-user-to-the-security-group"></a>Affectation de l’utilisateur système d’intégration au groupe de sécurité
1. Entrez edit security group dans la zone de recherche, puis cliquez sur le lien **Edit Security Group**.     
   
   ![Edit Security Group](./media/active-directory-saas-inbound-synchronization-tutorial/IC750983.png "Edit Security Group")  
2. Recherchez et sélectionnez le nouveau groupe de sécurité d’intégration par nom.    
   
   ![Edit Security Group](./media/active-directory-saas-inbound-synchronization-tutorial/IC750984.png "Edit Security Group")  
3. Ajoutez le nouvel utilisateur système d’intégration au nouveau groupe de sécurité.       
   
   ![Groupe de sécurité système](./media/active-directory-saas-inbound-synchronization-tutorial/IC750985.png "System Security Group")  

## <a name="configuring-security-group-options"></a>Configuration des options du groupe de sécurité
À cette étape, vous accordez au nouveau groupe de sécurité des autorisations pour les opérations Get et Put sur les objets sécurisés par les stratégies de sécurité de domaine suivantes :  

* External Account Provisioning  
* Worker Data: Public Worker Reports  
* Worker Data: All Positions  
* Worker Data: Current Staffing Information  
* Worker Data: Business Title on Worker Profile  

&nbsp;  

1. Entrez les stratégies de sécurité de domaine dans la zone de recherche, puis cliquez sur le lien, Domain Security Policies for Functional Area.     
   
   ![Stratégies de sécurité de domaine](./media/active-directory-saas-inbound-synchronization-tutorial/IC750986.png "Domain Security Policies")  
2. Recherchez system et sélectionnez la zone fonctionnelle System.  Cliquez sur OK.     
   
   ![Stratégies de sécurité de domaine](./media/active-directory-saas-inbound-synchronization-tutorial/IC750987.png "Domain Security Policies")  
3. Dans la liste des stratégies de sécurité de la zone fonctionnelle System, développez Security Administration et sélectionnez la stratégie de sécurité de domaine, External Account Provisioning.     
   
   ![Stratégies de sécurité de domaine](./media/active-directory-saas-inbound-synchronization-tutorial/IC750988.png "Domain Security Policies")  
4. Cliquez sur le bouton Edit Permissions, puis, dans l’écran Edit Permissions, ajoutez le nouveau groupe de sécurité à la liste des groupes de sécurité avec autorisations d’intégration Get et Put.     
   
   ![Modifier une autorisation](./media/active-directory-saas-inbound-synchronization-tutorial/IC750989.png "Edit Permission")  
5. Répétez l’étape 1 ci-dessus pour revenir à l’écran de sélection des zones fonctionnelles. Cette fois, recherchez staffing, sélectionnez la zone fonctionnelle Staffing, puis cliquez sur le OK.    
   
   ![Stratégies de sécurité de domaine](./media/active-directory-saas-inbound-synchronization-tutorial/IC750990.png "Domain Security Policies")  
6. Dans la liste des stratégies de sécurité de la zone fonctionnelle Staffing, développez Worker Data: Staffing, et répétez l’étape 4 ci-dessus pour chacune des stratégies de sécurité restantes :    
   
   * Worker Data: Public Worker Reports  
   * Worker Data: All Positions  
   * Worker Data: Current Staffing Information  
   * Worker Data: Business Title on Worker Profile    
   
   ![Stratégies de sécurité de domaine](./media/active-directory-saas-inbound-synchronization-tutorial/IC750991.png "Domain Security Policies")  

## <a name="activating-security-policy-changes"></a>Activation des modifications de stratégie de sécurité
1. Entrez activate dans la zone de recherche, puis cliquez sur le lien Activate Pending Security Policy Changes.    
   
   ![Activer](./media/active-directory-saas-inbound-synchronization-tutorial/IC750992.png "Activate")  
2. Commencez la tâche Activate Pending Security Policy Changes en entrant un commentaire à des fins d’audit, puis cliquez sur OK.      
   
   ![Activer la sécurité en attente](./media/active-directory-saas-inbound-synchronization-tutorial/IC750993.png "Activate Pending Security")  
3. Terminez la tâche sur l’écran suivant en cochant la case Confirm et en cliquant sur OK.     
   
   ![Activer la sécurité en attente](./media/active-directory-saas-inbound-synchronization-tutorial/IC750994.png "Activate Pending Security")  

## <a name="configuring-user-import-in-microsoft-azure-ad"></a>Configuration de l’importation d’utilisateurs dans Microsoft Azure AD
Cette section décrit la configuration de Microsoft Azure AD pour importer des utilisateurs à partir de Workday.    

### <a name="to-configure-user-import-in-microsoft-azure-ad,-perform-the-following-steps:"></a>Pour configurer l’importation d’utilisateurs dans Microsoft Azure AD, procédez comme suit :
1. Sur la page d’intégration d’application **Workday**, cliquez sur **Configurer l’importation d’utilisateurs** pour ouvrir la boîte de dialogue **Configurer l’approvisionnement**.    
2. Sur la page **Paramètres et informations d’identification administrateur** , procédez comme suit, puis cliquez sur Suivant :    
   
   ![Paramètres et informations d’identification administrateur](./media/active-directory-saas-inbound-synchronization-tutorial/IC750995.png "Settings and admin credentials")    
   
   1. Dans la zone de texte **Nom d’utilisateur admin Workday** , entrez le nom de l’utilisateur que vous avez créé dans la section [Création d’un utilisateur de système d’intégration](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser) .    
   2. Dans la zone de texte **Mot de passe de l’admin Workday** , entrez le mot de passe de l’utilisateur que vous avez créé dans la section [Création d’un utilisateur de système d’intégration](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser) .    
   3. Dans la zone de texte **URL de locataire Workday** , entrez l’URL de votre locataire Workday.    
3. Sur la page **Tester la connexion**, cliquez sur **Démarrer le test** pour vérifier la connectivité, puis cliquez sur **Suivant**.    
   
   ![Tester la connexion](./media/active-directory-saas-inbound-synchronization-tutorial/IC750996.png "Test connection")  
4. Sur la page **Options d’approvisionnement**, cliquez sur **Suivant**.    
   
   ![Options d’approvisionnement](./media/active-directory-saas-inbound-synchronization-tutorial/IC750997.png "Provisioning options")  
5. Dans la boîte de dialogue **Démarrer l’approvisionnement**, cliquez sur **Terminer**.    
   
   ![Démarrer l’approvisionnement](./media/active-directory-saas-inbound-synchronization-tutorial/IC750998.png "Start provisioning")  

Vous pouvez maintenant accéder à la section **Utilisateurs** et vérifier si votre utilisateur Workday a été importé.    

<!--HONumber=Oct16_HO2-->


