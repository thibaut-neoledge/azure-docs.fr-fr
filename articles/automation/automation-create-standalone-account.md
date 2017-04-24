---
title: "Création d’un compte Azure Automation autonome | Microsoft Docs"
description: "Ce didacticiel vous guide tout au long des procédures de création et de test d’une authentification de principal de sécurité dans Azure Automation. Il est complété par un exemple d’utilisation."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/14/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 97b863748dd726e19217e360645b8e6189c010b3
ms.lasthandoff: 04/15/2017

---

# <a name="create-a-standalone-azure-automation-account"></a>Création d’un compte Azure Automation autonome
Cette rubrique montre comment créer un compte Automation à partir du portail Azure si vous souhaitez évaluer et apprendre Azure Automation sans inclure les solutions de gestion supplémentaires ou une intégration avec OMS Log Analytics pour fournir une surveillance avancée des travaux Runbook.  Vous pourrez ajouter ces solutions de gestion ou effectuer une intégration à Log Analytics ultérieurement.  Avec le compte Automation, vous avez la possibilité d’identifier les ressources de gestion des Runbooks dans Azure Resource Manager ou le modèle de déploiement Azure Classic.

Lorsque vous créez un compte Automation dans le portail Azure, il crée automatiquement :

* Un compte d’identification, qui crée un principal du service dans Azure Active Directory, un certificat, et attribue le contrôle d’accès en fonction du rôle (RBAC) Collaborateur, qui est utilisé pour gérer les ressources Resource Manager à l’aide de Runbooks.   
* Un compte d’identification Classic en chargeant un certificat de gestion, qui est utilisé pour gérer les ressources classiques à l’aide de Runbooks.  

Le processus s’en trouve ainsi simplifié, et vous êtes en mesure de commencer rapidement à générer et déployer des Runbooks pour répondre à vos besoins d’automatisation.  

## <a name="create-a-new-automation-account-from-the-azure-portal"></a>Créer un compte Automation à partir du portail Azure
Dans cette section, suivez la procédure qui vous permet de créer un compte Azure Automation dans le portail Azure.    

>[!NOTE]
>Pour créer un compte Automation, vous devez posséder le rôle Administrateurs de services ou être coadministrateur de l’abonnement, ce qui permet d’accéder à l’abonnement. Vous devez également être ajouté en tant qu’utilisateur à l’instance Active Directory par défaut de cet abonnement. Il n’est pas nécessaire d’attribuer au compte un rôle doté de privilèges.
>
>Si vous n’êtes pas membre de l’instance d’Active Directory de l’abonnement avant d’être ajouté au rôle Coadministrateur de l’abonnement, vous êtes ajouté à Active Directory en tant qu’invité. Dans ce cas, vous recevez le message d’avertissement « Vous n’avez pas les autorisations pour créer... » dans le panneau **Ajouter un compte Automation**.
>
>Les utilisateurs qui ont d’abord reçu le rôle Coadministrateur peuvent être supprimés de l’instance Active Directory de l’abonnement, puis rajoutés pour en faire des utilisateurs complets dans Active Directory. Pour vérifier si tel est le cas, dans le volet **Azure Active Directory** du portail Azure, sélectionnez **Utilisateurs et groupes** et **Tous les utilisateurs**, choisissez l’utilisateur concerné, puis sélectionnez **Profil**. La valeur de l’attribut **Type d’utilisateur** sous le profil de l’utilisateur ne doit pas être **Invité**.

1. Connectez-vous au portail Azure avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.
2. Cliquez sur **Nouveau**.<br><br> ![Sélection de l’option Nouveau dans le portail Azure](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. Recherchez **Automation**, puis dans les résultats de recherche, sélectionnez **Automation & Control***.<br><br> ![Recherche et sélection d’Automation à partir de la Place de marché](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)<br> 
3. Dans le panneau Comptes Automation, cliquez sur **Ajouter**.<br><br>![Ajouter un compte Automation](media/automation-create-standalone-account/automation-create-automationacct-properties.png)
   
   > [!NOTE]
   > Si l’avertissement suivant s’affiche dans le panneau **Ajouter un compte Automation**, cela signifie que votre compte n’est ni membre du rôle Administrateurs des abonnements ni coadministrateur de l’abonnement.<br><br>![Avertissement Ajouter un compte Automation](media/automation-create-standalone-account/create-account-without-perms.png)
   > 
   > 
4. Dans le panneau **Ajouter un compte Automation**, entrez le nom de votre nouveau compte Automation dans la zone **Nom**.
5. Si vous disposez de plusieurs abonnements, spécifiez-en un pour le nouveau compte, puis indiquez un **groupe de ressources** nouveau ou existant et un **emplacement** de centre de données Azure.
6. Vérifiez que l’option **Créer un compte d’identification Azure** est bien définie sur la valeur **Oui**, puis cliquez sur le bouton **Créer**.  
   
   > [!NOTE]
   > Si vous choisissez de ne pas créer de compte d’identification en sélectionnant l’option **Non**, un message d’avertissement s’affiche dans le panneau **Ajouter un compte Automation**.  Même si le compte est créé dans le portail Azure, il n’aura pas d’identité d’authentification correspondante au sein de votre service d’annuaire des abonnements classique ou Resource Manager et n’aura donc pas accès aux ressources de votre abonnement.  Cela empêche tous les Runbooks faisant référence à ce compte de pouvoir s’authentifier et d’effectuer des tâches sur les ressources de ces modèles de déploiement.
   > 
   > ![Avertissement Ajouter un compte Automation](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)<br>
   > Si le principal du service n’est pas créé, le rôle Collaborateur n’est pas attribué.
   > 

7. Pour suivre la progression de la création du compte Automation, accédez à l’onglet **Notifications** du menu.

### <a name="resources-included"></a>Ressources incluses
Une fois le compte Automation créé, plusieurs ressources vous sont automatiquement créées.  Le tableau ci-dessous récapitule les ressources du compte d’identification.<br>

| Ressource | Description |
| --- | --- |
| Runbook AzureAutomationTutorial |Exemple de Runbook Graphical qui illustre l’authentification à l’aide du compte d’identification et l’accès à l’ensemble des ressources Resource Manager. |
| Runbook AzureAutomationTutorialScript |Exemple de Runbook PowerShell qui illustre l’authentification à l’aide du compte d’identification et l’accès à l’ensemble des ressources Resource Manager. |
| AzureRunAsCertificate |Ressource de certificat créée automatiquement lors de la création du compte Automation ou à l’aide du script PowerShell ci-dessous pour un compte existant.  Elle vous permet de vous authentifier auprès d’Azure afin de pouvoir gérer les ressources Azure Resource Manager des Runbooks.  Ce certificat a une durée de vie d’un an. |
| AzureRunAsConnection |Ressource de connexion créée automatiquement lors de la création du compte Automation ou à l’aide du script PowerShell ci-dessous pour un compte existant. |

Le tableau ci-dessous récapitule les ressources du compte d’identification Classic.<br>

| Ressource | Description |
| --- | --- |
| Runbook AzureClassicAutomationTutorial |Exemple de Runbook Graphical qui accède à toutes les machines virtuelles Classic d’un abonnement à l’aide du compte d’identification Classic (certificat), puis indique leur nom et leur état. |
| Runbook AzureClassicAutomationTutorialScript |Exemple de Runbook PowerShell qui accède à toutes les machines virtuelles Classic d’un abonnement à l’aide du compte d’identification Classic (certificat), puis indique leur nom et leur état. |
| AzureClassicRunAsCertificate |Ressource de certificat créée automatiquement, qui est utilisée pour l’authentification auprès d’Azure afin que vous puissiez gérer les ressources Azure Classic des Runbooks.  Ce certificat a une durée de vie d’un an. |
| AzureClassicRunAsConnection |Ressource de connexion créée automatiquement, qui est utilisée pour l’authentification auprès d’Azure afin que vous puissiez gérer les ressources Azure Classic des Runbooks. |


## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la création graphique, consultez [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md).
* Pour une prise en main des Runbooks PowerShell, consultez [Mon premier Runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Pour une prise en main des Runbooks de workflow PowerShell, consultez [Mon premier Runbook PowerShell Workflow (automation-first-runbook-textual.md).
