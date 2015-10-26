<properties 
  pageTitle="Didacticiel : Configuration de Workday pour la synchronisation entrante | Microsoft Azure" 
  description="Découvrez comment utiliser Workday en tant que source de données d'identité pour Azure Active Directory." 
  services="active-directory" 
  authors="MarkusVi"  
  documentationCenter="na" 
  manager="msStevenPo"/>
<tags 
  ms.service="active-directory" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.tgt_pltfrm="na" 
  ms.workload="identity" 
  ms.date="08/01/2015" 
  ms.author="markvi" />


#Didacticiel : Configuration de Workday pour la synchronisation entrante


>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=521880).
  
L’objectif de ce didacticiel consiste à vous présenter les opérations à effectuer dans Workday et Azure AD pour importer des utilisateurs de Workday à Azure AD.

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure AD Premium valide
-   Un locataire dans Workday
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour Workday 


2. Création d’un utilisateur système d’intégration


3. Création d’un groupe de sécurité


4. Affectation de l’utilisateur système d’intégration au groupe de sécurité


5. Configuration des options du groupe de sécurité


6. Activation des modifications de stratégie de sécurité


7. Configuration de l’importation d’utilisateurs dans Azure AD



##Activation de l’intégration d’application pour Workday
  
Cette section décrit l’activation de l’intégration d’applications pour Workday.

###Pour activer l’intégration d’application pour Workday, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-workday-inbound-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-workday-inbound-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-workday-inbound-tutorial/IC749321.png "Ajouter l’application")

  
5. Dans la zone de recherche, tapez **Workday**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-workday-inbound-tutorial/IC701021.png "Ajouter une application à partir de la galerie")

6. Dans le volet de résultats, sélectionnez Workday, puis cliquez sur Terminer pour ajouter l’application.

    ![Galerie d’applications](./media/active-directory-saas-workday-inbound-tutorial/IC701022.png "Galerie d'applications")





## Création d’un utilisateur système d’intégration

1. Dans **Workday Workbench**, entrez Create user dans la zone de recherche, puis cliquez sur **Create Integration System User**.<br><br>![Créer un utilisateur](./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "Créer un utilisateur")



2. Exécutez la tâche **Create Integration System User** en fournissant un nom d’utilisateur et un mot de passe pour un nouvel utilisateur système d’intégration. Laissez l’option Require New Password at Next Sign In désactivée, étant donné que cet utilisateur se connectera par programmation. <br>Laissez la valeur par défaut de 0 pour l’option Session Timeout Minutes afin d’éviter que les sessions de l’utilisateur n’expirent prématurément.<br><br>![Créer un utilisateur système d’intégration](./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "Créer un utilisateur système d’intégration")
 




## Création d’un groupe de sécurité

Pour le scénario de ce didacticiel, vous devez créer un groupe de sécurité système d’intégration sans contrainte et lui affecter l’utilisateur.


1. Entrez create security group dans la zone de recherche, puis cliquez sur **Create Security Group**.<br><br> ![Créer un groupe de sécurité](./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "Créer un groupe de sécurité")
 

2. Exécutez la tâche Create Security Group. Sélectionnez Integration System Security Group—Unconstrained dans la liste déroulante Type of Tenanted Security Group pour créer un groupe de sécurité auquel des membres seront ajoutés de manière explicite.<br><br> ![Créer un groupe de sécurité](./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "Créer un groupe de sécurité")
 



## Affectation de l’utilisateur système d’intégration au groupe de sécurité

1. Entrez edit security group dans la zone de recherche, puis cliquez sur **Edit Security Group**.<br><br> ![Modifier un groupe de sécurité](./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "Modifier un groupe de sécurité")
 
 

2. Recherchez et sélectionnez le nouveau groupe de sécurité d’intégration par nom. <br><br> ![Modifier un groupe de sécurité](./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "Modifier un groupe de sécurité") Modifier un groupe de sécurité

3. Ajoutez le nouvel utilisateur système d’intégration au nouveau groupe de sécurité.<br><br> ![Groupe de sécurité système](./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "Groupe de sécurité système")




## Configuration des options du groupe de sécurité

À cette étape, vous accordez au nouveau groupe de sécurité des autorisations pour les opérations **Get** et **Put** sur les objets sécurisés par les stratégies de sécurité de domaine suivantes :

- External Account Provisioning

- Worker Data: Public Worker Reports

- Worker Data: All Positions

- Worker Data: Current Staffing Information

- Worker Data: Business Title on Worker Profile
 
   

1. Entrez domain security policies dans la zone de recherche, puis cliquez sur le lien, Domain Security Policies for Functional Area.<br><br> ![Stratégies de sécurité de domaine](./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "Stratégies de sécurité de domaine")  
 

2. Recherchez system et sélectionnez la zone fonctionnelle **System**. Cliquez sur **OK**. <br><br> ![Stratégies de sécurité de domaine](./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "Stratégies de sécurité de domaine")


3. Dans la liste des stratégies de sécurité de la zone fonctionnelle System, développez Security Administration et sélectionnez la stratégie de sécurité de domaine, External Account Provisioning.<br><br> ![Stratégies de sécurité de domaine](./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "Stratégies de sécurité de domaine")


4. Cliquez sur **Edit Permissions**, puis, sur la page de dialogue **Edit Permissions**, ajoutez le nouveau groupe de sécurité à la liste des groupes de sécurité avec autorisations d’intégration **Get** et **Put**.<br><br> ![Modifier une autorisation](./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "Modifier une autorisation")

 

5. Répétez l’étape 1 ci-dessus pour revenir à l’écran de sélection des zones fonctionnelles. Cette fois, recherchez staffing, sélectionnez la zone fonctionnelle Staffing, puis cliquez sur **OK**.<br><br> ![Stratégies de sécurité de domaine](./media/active-directory-saas-workday-inbound-tutorial/IC750990.png "Stratégies de sécurité de domaine")
 

6. Dans la liste des stratégies de sécurité de la zone fonctionnelle Staffing, développez Worker Data: Staffing, et répétez l’étape 4 ci-dessus pour chacune des stratégies de sécurité restantes :

     - Worker Data: Public Worker Reports

     - Worker Data: All Positions

     - Worker Data: Current Staffing Information

     - Worker Data: Business Title on Worker Profile


<br><br> ![Stratégies de sécurité de domaine](./media/active-directory-saas-workday-inbound-tutorial/IC750991.png "Stratégies de sécurité de domaine")







## Activation des modifications de stratégie de sécurité


1. Entrez activate dans la zone de recherche, puis cliquez sur le lien Activate Pending Security Policy Changes. <br><br> ![Activer](./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "Activer") 
 

2. Commencez la tâche Activate Pending Security Policy Changes en entrant un commentaire à des fins d’audit, puis cliquez sur **OK**.<br><br> ![Activer la sécurité en attente](./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "Activer la sécurité en attente")
 

3. Terminez la tâche sur l’écran suivant en cochant la case Confim, puis cliquez sur **OK**. <br><br> ![Activer la sécurité en attente](./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "Activer la sécurité en attente")





## Configuration de l’importation d’utilisateurs dans Azure AD

Cette section décrit la configuration d’Azure AD pour importer des utilisateurs à partir de Workday.


### Pour configurer l’importation d’utilisateurs dans Azure AD, procédez comme suit :


1. Sur la page d’intégration d’application **Workday**, cliquez sur **Configurer l’importation d’utilisateurs** pour ouvrir la boîte de dialogue **Configurer l’approvisionnement**.


2\.Sur la page **Paramètres et informations d’identification administrateur**, procédez comme suit, puis cliquez sur **Suivant** : <br><br> ![Paramètres et informations d’identification d’administrateur](./media/active-directory-saas-workday-inbound-tutorial/IC750995.png "Paramètres et informations d’identification d’administrateur")
 
     2.1. In the Workday admin user name textbox, type the name of the user you have created in the Creating an integration system user section.

     2.2. In the Workday admin password textbox, type the password of the user you have created in the Creating an integration system user section.

     2.3. In the Workday tenant URL textbox, type the URL or your Workday tenant.


3. Sur la page **Tester la connexion**, cliquez sur **Démarrer le test** pour vérifier la connectivité, puis cliquez sur **Suivant**. <br><br> ![Tester la connexion](./media/active-directory-saas-workday-inbound-tutorial/IC750996.png "Tester la connexion")  
 

4. Sur la page **Options d’approvisionnement**, cliquez sur **Suivant**. <br><br> ![Options d’approvisionnement](./media/active-directory-saas-workday-inbound-tutorial/IC750997.png "Options d’approvisionnement")


5. Dans la boîte de dialogue **Démarrer l’approvisionnement**, cliquez sur **Terminer**. <br><br> ![Démarrer l’approvisionnement](./media/active-directory-saas-workday-inbound-tutorial/IC750998.png "Démarrer l’approvisionnement")
 

Vous pouvez maintenant accéder à la section **Utilisateurs**et vérifier si votre utilisateur Workday a été importé.



## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!---HONumber=Oct15_HO3-->