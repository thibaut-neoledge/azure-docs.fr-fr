<properties
   pageTitle="Configuration d’Azure Automation"
   description="Décrit les étapes que vous devez effectuer pour configurer Azure Automation pour une première utilisation."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/10/2015"
   ms.author="bwren;sngun" />

# Configuration d’Azure Automation

Cet article décrit les actions que vous devez effectuer pour commencer à utiliser Azure Automation.

## Comptes Automation

Lorsque vous démarrez Azure Automation pour la première fois, vous devez créer au moins un compte Automation. Les comptes Automation vous permettent d’isoler vos ressources Automation (Runbooks, ressources, configurations) des ressources Automation contenues dans d’autres comptes Automation. Vous pouvez utiliser des comptes Automation pour séparer les ressources Automation dans des environnements logiques distincts. Par exemple, vous pouvez utiliser un compte pour le développement et un autre pour la production.

Les ressources Automation de chaque compte Automation sont associées à une seule région Azure, mais les comptes Automation peuvent gérer les services Azure dans n’importe quelle région. L’existence de stratégies qui requièrent l’isolation des données et des ressources dans une région spécifique constitue la raison principale de création de comptes Automation dans différentes régions.

>[AZURE.NOTE]Les comptes Automation et les ressources qu'ils contiennent, créés avec la version d'évaluation du portail Azure, ne sont pas accessibles dans le portail Azure. Si vous souhaitez gérer ces comptes ou leurs ressources avec Windows PowerShell, vous devez utiliser les modules Azure Resource Manager.
>
>Les comptes Automation créés avec le portail Azure peuvent être gérés soit par le portail, soit par le jeu d'applets de commande. Une fois que le compte est créé, la façon de créer et de gérer des ressources au sein du compte n'importe pas. Si vous envisagez de continuer à utiliser le portail Azure, vous devez alors l'utiliser au lieu d'utiliser le portail d'évaluation Azure pour créer des comptes Automation.


Un compte Automation peut être suspendu s’il existe un problème avec votre compte Azure, comme un retard de paiement. Dans ce cas, vous ne pouvez pas accéder au compte, tous les travaux en cours d’exécution sont interrompus et toutes les planifications sont désactivées. Vous pouvez afficher le compte, mais vous ne pouvez pas voir les ressources qu’il contient. Lorsque vous avez corrigé le problème et que le compte Automation est activé, vous devez activer vos planifications et redémarrer les Runbooks qui ont été interrompus.


## Configuration de l’authentification aux ressources Azure

Lorsque vous accédez aux ressources Azure à l’aide des [applets de commande Azure](http://msdn.microsoft.com/library/azure/jj554330.aspx), vous devez fournir l’authentification à votre abonnement Azure. Dans Azure Automation, cela se fait par le biais d’un compte professionnel dans Azure Active Directory, que vous configurez en tant qu’administrateur pour votre abonnement. Vous pouvez ensuite créer des [informations d’identification](http://msdn.microsoft.com/library/dn940015.aspx) pour ce compte utilisateur et l’utiliser avec [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) dans votre Runbook.

>[AZURE.NOTE]Les comptes Microsoft, anciennement appelés LiveID, ne peuvent pas être utilisés avec Azure Automation.

## Création d’un nouvel utilisateur Azure Active Directory pour gérer un abonnement Azure

1. Connectez-vous au portail Azure en tant qu’administrateur de service pour l’abonnement Azure que vous souhaitez gérer.
2. Sélectionnez **Active Directory**
3. Sélectionnez le nom du répertoire associé à votre abonnement Azure. Si nécessaire, vous pouvez modifier cette association à partir de **Paramètres > Abonnements > Modifier le répertoire**.
4. [Création d’un nouvel utilisateur Active Directory](http://msdn.microsoft.com/library/azure/hh967632.aspx). Sélectionnez **Nouvel utilisateur dans votre organisation** pour le **Type d’utilisateur** et n’utilisez pas **Activer l’authentification multifacteur**.
5. Notez le nom complet de l’utilisateur et le mot de passe temporaire.
7. Sélectionnez **Paramètres > Administrateurs > Ajouter**.
8. Tapez le nom d’utilisateur complet de l’utilisateur que vous avez créé.
9. Sélectionnez l’abonnement que vous souhaitez gérer.
10. Déconnectez-vous d’Azure et puis reconnectez-vous avec le compte que vous venez de créer. Vous serez invité à modifier le mot de passe de l’utilisateur.
11. Créez une nouvelle [Ressource d’informations d’identification Azure Automation](http://msdn.microsoft.com/library/dn940015.aspx) pour le compte d’utilisateur que vous avez créé. Le **Type d’informations d’identification** doit être **Informations d’identification Windows PowerShell**.

## Créer un compte Automation

Un compte Automation est un conteneur pour vos ressources Azure Automation. Il fournit un moyen de séparer vos environnements ou de mieux organiser vos flux de travail. Si vous avez déjà créé un compte Automation, vous pouvez ignorer cette étape.

1. Connectez-vous au [Portail Azure en version préliminaire](https://portal.azure.com/).

2. Dans le portail Azure en version préliminaire, cliquez sur **Nouveau** > **Gestion** > **Compte Automation**

3. Dans le panneau **Ajouter un compte Automation**, configurez les détails de votre compte Automation.

>[AZURE.NOTE]Lorsqu’un compte Automation est créé avec le portail Azure en version préliminaire, le compte et toutes ses ressources associées ne sont pas ramenés au portail de gestion classique.

Vous trouverez ci-dessous la liste des paramètres à configurer :

|Paramètre |Description |
|:---|:---|
| Nom | Nom de votre compte Automation ; il doit s’agir d’une valeur unique. |
| Groupe de ressources | Les groupes de ressources facilitent l’affichage et la gestion des ressources Azure connexes. Dans le portail Azure en version préliminaire, vous pouvez choisir un groupe de ressources existant ou en créer un nouveau pour votre compte Automation, tandis que dans le portail de gestion Azure, tous les comptes Automation sont placés dans un groupe de ressources par défaut. |
| Abonnement | Choisissez un abonnement dans la liste des abonnements disponibles. |
| Région | La région spécifie où sont stockées les ressources Automation du compte. Vous pouvez choisir n’importe quelle région dans la liste : les fonctionnalités de votre compte n’en sont pas affectées, mais vos Runbooks peuvent s’exécuter plus rapidement si la région de votre compte est proche de l’emplacement où sont stockées vos autres ressources Azure. |
| Options de compte | Cette option vous permet de choisir quelles ressources seront créées dans votre nouveau compte Automation ; la sélection de l’option **Oui** crée un Runbook didacticiel. |

![Créer un compte](media/automation-configuration/automation-01-create-automation-account.png)

>[AZURE.NOTE]Lorsqu’un compte Automation créé à l’aide du portail de gestion classique est [déplacé vers un groupe de ressources différent](../resource-group-move-resources.md) à l’aide du portail Azure en version préliminaire, le compte Automation ne sera plus disponible dans le portail Azure classique, car les comptes Azure Resource Manager ne sont pas pris en charge dans le portail de gestion classique.



## Utilisation des informations d’identification dans un Runbook

Vous pouvez récupérer les informations d’identification dans un Runbook à l’aide de l’activité [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) et les utiliser avec [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) pour vous connecter à votre abonnement Azure. Si les informations d’identification correspondent à un administrateur de plusieurs abonnements Azure, vous devez également utiliser [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) pour spécifier celle qui convient. Cela est illustré dans l’exemple Windows PowerShell ci-dessous qui apparaît en haut de la plupart des Runbooks Azure Automation.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
	Add-AzureAccount –Credential $cred
	Select-AzureSubscription –SubscriptionName "My Subscription"

Vous devez répéter ces lignes après tout [point de contrôle](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) dans votre Runbook. Si le Runbook est interrompu, puis reprend avec un autre collaborateur, ce dernier doit s’authentifier à nouveau.

## Articles connexes
- [Azure Automation : authentification auprès d’Azure à l’aide d’Azure Active Directory](http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/)
 

<!---HONumber=AcomDC_1125_2015-->