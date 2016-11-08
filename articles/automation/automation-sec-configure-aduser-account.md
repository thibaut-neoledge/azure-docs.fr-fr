---
title: Configurer un compte d’utilisateur Azure AD | Microsoft Docs
description: Cet article décrit comment configurer les informations d’identification du compte d’utilisateur Azure AD pour les runbooks dans Azure Automation pour l’authentification sur ARM et ASM.
services: automation
documentationcenter: ''
author: MGoedtel
manager: jwhit
editor: tysonn
keywords: utilisateur azure active directory, azure service management, compte d’utilisateur azure ad

ms.service: automation
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/12/2016
ms.author: magoedte

---
# Authentification des Runbooks avec Azure Service Management et Resource Manager
Cet article décrit les étapes à effectuer pour configurer un compte d’utilisateur d’Azure AD pour les runbooks Azure Automation en cours d’exécution sur des ressources Azure Service Management (ASM) ou Azure Resource Manager (ARM). Bien que cette procédure est toujours prise en charge comme identité d’authentification pour vos runbooks ARM, il est recommandé d’utiliser le nouveau compte d’authentification Azure.

## Création d’un nouvel utilisateur Azure Active Directory
1. Connectez-vous au portail Azure Classic en tant qu’administrateur de services fédérés pour l’abonnement Azure que vous souhaitez gérer.
2. Sélectionnez **Active Directory**, puis sélectionnez le nom du répertoire de votre organisation.
3. Sélectionnez l’onglet **Utilisateurs**, puis, dans la zone de commande, sélectionnez **Ajouter un utilisateur**.
4. Sur la page **Dites-nous en plus sur cet utilisateur**, sous **Type d’utilisateur**, sélectionnez **Nouvel utilisateur dans votre organisation**.
5. Entrez un nom d’utilisateur.
6. Sélectionnez le nom du répertoire associé à votre abonnement Azure sur la page Active Directory.
7. Sur la page **Profil de l’utilisateur**, entrez les nom et prénom de l’utilisateur, un nom convivial et sélectionnez Utilisateur dans la liste **Rôles**. Ne sélectionnez pas la case à cocher **Activer l’authentification multifacteur**.
8. Notez le nom complet de l’utilisateur et le mot de passe temporaire.
9. Sélectionnez **Paramètres > Administrateurs > Ajouter**.
10. Tapez le nom d’utilisateur complet de l’utilisateur que vous avez créé.
11. Sélectionnez l’abonnement que vous souhaitez gérer.
12. Déconnectez-vous d’Azure et puis reconnectez-vous avec le compte que vous venez de créer. Vous serez invité à modifier le mot de passe de l’utilisateur.

## Création d’un compte Automation dans le portail Azure Classic
Dans cette section, vous allez effectuer les étapes suivantes pour créer un nouveau compte Azure Automation dans le portail Azure qui sera utilisé avec vos runbooks pour la gestion des ressources en mode ASM et ARM.

> [!NOTE]
> Les comptes Automation créés avec le portail Azure Classic peuvent être gérés par le portail Azure Classic et le portail Azure, et par le jeu d’applets de commande correspondant. Une fois que le compte est créé, la façon de créer et de gérer des ressources au sein du compte n'importe pas. Si vous envisagez de continuer à utiliser le portail Azure Classic, vous devez alors l’utiliser au lieu d’utiliser le portail Azure pour créer des comptes Automation.
> 
> 

1. Connectez-vous au portail Azure Classic en tant qu’administrateur de services fédérés pour l’abonnement Azure que vous souhaitez gérer.
2. Sélectionnez **Automation**.
3. Sur la page **Automation**, sélectionnez **Créer un compte Automation**.
4. Dans la boîte de dialogue **Créer un compte Automation**, entrez un nom pour votre nouveau compte Automation et sélectionnez une **région** dans la liste déroulante.
5. Cliquez sur **OK** pour accepter les paramètres et créer le compte.
6. Une fois le compte créé, il est répertorié sur la page **Automation**.
7. Cliquez sur le compte pour accéder à la page Tableau de bord.
8. Sur la page Tableau de bord Automation, sélectionnez **Actifs**.
9. Sur la page **Actifs**, sélectionnez l’option **Ajouter des paramètres** située en bas de la page.
10. Sur la page **Ajouter des paramètres**, sélectionnez **Ajouter des informations d’identification**.
11. Sur la page **Définir les informations d’identification**, sélectionnez **Informations d’identification de Windows PowerShell** dans la liste déroulante **Type d’informations d’identification** et entrez un nom pour les informations d’identification.
12. Sur la page **Définir les informations d’identification** suivante, entrez le nom d’utilisateur du compte utilisateur Active Directory créé précédemment dans le champ **Nom d’utilisateur** et le mot de passe dans les champs **Mot de passe** et **Confirmer le mot de passe**. Cliquez sur **OK** pour enregistrer vos modifications.

## Création d’un compte Automation dans le portail Azure
Dans cette section, vous allez effectuer les étapes suivantes pour créer un nouveau compte Azure Automation dans le portail Azure qui sera utilisé avec vos runbooks pour la gestion des ressources en mode ARM.

1. Connectez-vous au portail Azure en tant qu’administrateur de service pour l’abonnement Azure que vous souhaitez gérer.
2. Sélectionnez **Comptes Automation**.
3. Dans le panneau Comptes Automation, cliquez sur **Ajouter**.<br>![Ajouter un compte Automation](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties.png)
4. Dans le panneau **Ajouter un compte Automation**, entrez le nom de votre nouveau compte Automation dans la zone **Nom**.
5. Si vous disposez de plusieurs abonnements, spécifiez celui du nouveau compte, ainsi qu’un **groupe de ressources** nouveau ou existant et un **emplacement** de centre de données Azure.
6. Vérifiez que l’option **Créer un compte d’authentification Azure** est bien définie sur la valeur **Non**, puis cliquez sur le bouton **Créer**.
   
   > [!NOTE]
   > Si vous choisissez de ne pas créer de compte d’identification en sélectionnant l’option **Non**, un message d’avertissement s’affiche dans le panneau **Ajouter un compte Automation**. Bien que le compte soit créé avec le rôle de **contributeur** dans l’abonnement, il n’aura pas d’identité d’authentification correspondante au sein de votre service de répertoire d’abonnements et, par conséquent, il n’aura pas accès aux ressources de votre abonnement. Cela empêchera tous les Runbooks faisant référence à ce compte de pouvoir authentifier et effectuer des tâches sur les ressources ARM.
   > 
   > 
   
    ![Avertissement Ajouter un compte Automation](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties-error.png)
7. Pour suivre la progression de la création du compte Automation, accédez à l’onglet **Notifications** du menu.

Lorsque la création des information d’identification est terminée, vous devrez créer un actif d’informations d’identification pour associer le compte Automation au compte d’utilisateur Active Directory créé précédemment. N’oubliez pas que nous avons uniquement créé le compte Automation et qu’il n’est pas associé à une identité d’authentification. Suivez les étapes présentées dans l’article [Ressources d’informations d’identification dans Azure Automation](automation-credentials.md#creating-a-new-credential) et entrez la valeur du **nom d’utilisateur** au format **domaine\\utilisateur**.

## Utilisation des informations d’identification dans un Runbook
Vous pouvez récupérer les informations d’identification dans un Runbook à l’aide de l’activité [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) et les utiliser avec [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) pour vous connecter à votre abonnement Azure. Si les informations d’identification correspondent à un administrateur de plusieurs abonnements Azure, vous devez également utiliser [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) pour spécifier celle qui convient. Cela est illustré dans l’exemple Windows PowerShell ci-dessous qui apparaît en haut de la plupart des Runbooks Azure Automation.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

Vous devez répéter ces lignes après tout [point de contrôle](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) dans votre Runbook. Si le Runbook est interrompu, puis reprend avec un autre collaborateur, ce dernier doit s’authentifier à nouveau.

## Étapes suivantes
* Passez en revue les différents types de Runbooks et les étapes pour créer vos propres Runbooks dans l’article [Types de Runbooks Azure Automation](automation-runbook-types.md)

<!---HONumber=AcomDC_0914_2016-->