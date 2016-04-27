<properties
	pageTitle="Opérations d’audit avec Resource Manager | Microsoft Azure"
	description="Utilisez le journal d’audit dans Resource Manager pour passer en revue les actions et les erreurs des utilisateurs. Affiche le portail Azure, PowerShell, l’interface CLI Azure et REST."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/21/2016"
	ms.author="tomfitz"/>

# Opérations d’audit avec Resource Manager

Les journaux d’audit vous permettent de déterminer :

- Les opérations qui ont été effectuées sur les ressources de votre abonnement
- Les utilisateurs qui ont lancé l’opération (même si les opérations lancées par un service principal ne retournent pas d’utilisateur en tant qu’appelant)
- Le moment où a eu lieu l’opération
- L’état de l’opération
- Les valeurs d’autres propriétés qui peuvent vous aider à effectuer des recherches sur l’opération

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Cette rubrique traite de l’audit des opérations. Si vous voulez en savoir plus sur les journaux d’audit pour résoudre les problèmes liés à un déploiement, consultez [Résolution des problèmes liés aux déploiements de groupes de ressources dans Azure](resource-manager-troubleshoot-deployments-portal.md).

Vous pouvez récupérer des informations dans les journaux d’audit par le biais du portail Azure, d’Azure PowerShell, de l’interface de ligne de commande Azure, de l’API REST Insights ou de [Insights .NET Library](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## Portail pour afficher les journaux d’audit

1. Pour afficher les journaux d’audit dans le portail, sélectionnez **Parcourir** et **Journaux d’audit**.

    ![sélectionner des journaux d’audit](./media/resource-group-audit/select-audit-logs.png)

2. Le panneau **Journaux d’audit** affiche un résumé des opérations récentes de tous les groupes de ressources de votre abonnement. Il inclut une représentation graphique du moment où ont eu lieu les opérations et de leur état, ainsi qu’une liste des opérations.

    ![afficher des actions](./media/resource-group-audit/audit-summary.png)

3. Pour rechercher un type particulier d’action, vous pouvez filtrer les opérations affichées dans le panneau des journaux d’audit. Sélectionnez **Filtrer** en haut du panneau.

    ![filtrer les journaux](./media/resource-group-audit/filter-logs.png)

4. Dans le panneau **Filtre**, vous pouvez sélectionner plusieurs conditions pour limiter le nombre d’opérations affichées. Par exemple, vous pouvez afficher toutes les actions effectuées par un utilisateur particulier au cours de la semaine dernière.

    ![définir des options de filtre](./media/resource-group-audit/set-filter.png)

Une fois la vue des journaux d’audit mise à jour, seules les opérations qui répondent à la condition spécifiée sont affichées. Ces paramètres sont conservés lors de la prochaine consultation des journaux d’audit, vous devrez donc peut-être modifier ces valeurs pour étendre l’affichage des opérations.

Vous pouvez également afficher automatiquement les journaux d’audit d’une ressource particulière en les sélectionnant dans le panneau de cette ressource. Dans le portail, sélectionnez la ressource à auditer, puis **Journaux d’audit**.

![auditer une ressource](./media/resource-group-audit/audit-by-resource.png)

Notez que le journal d’audit est automatiquement filtré sur la dernière ressource sélectionnée pour la semaine précédente.

![filtrer par ressource](./media/resource-group-audit/filtered-by-resource.png)

## PowerShell pour afficher les journaux d’audit

1. Pour récupérer les entrées de journal, exécutez la commande **Get-AzureRmLog**. Vous spécifiez des paramètres supplémentaires pour filtrer la liste des entrées. Si vous ne spécifiez pas une heure de début et de fin, les entrées de la dernière heure sont retournées. Par exemple, pour récupérer les opérations d’un groupe de ressources pendant la dernière heure d’exécution :

        Get-AzureRmLog -ResourceGroup ExampleGroup

    L’exemple suivant montre comment utiliser le journal d’audit pour rechercher les opérations effectuées à un moment spécifié. Les dates de début et de fin sont indiquées dans un format de date.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00

    Vous pouvez également utiliser les fonctions de date pour spécifier la plage de dates, par exemple, les 14 derniers jours.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)

2. En fonction de l’heure de début que vous spécifiez, les commandes précédentes peuvent retourner une longue liste d’opérations pour le groupe de ressources. Vous pouvez filtrer les résultats de votre recherche en fournissant des critères de recherche. Par exemple, si vous recherchez la manière dont une application web a été arrêtée, vous pouvez exécuter la commande suivante.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
        
    Dans cet exemple, elle montre qu’une action d’arrêt a été effectuée par someone@contoso.com.
        
        Authorization     :
        Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
        Action    : Microsoft.Web/sites/stop/action
        Role      : Subscription Admin
        Condition :
        Caller            : someone@contoso.com
        CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
        EventSource       : Administrative
        EventTimestamp    : 8/28/2015 4:08:18 PM
        OperationName     : Microsoft.Web/sites/stop/action
        ResourceGroupName : ExampleGroup
        ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
        Status            : Succeeded
        SubscriptionId    : xxxxx
        SubStatus         : OK

3. Vous pouvez rechercher les actions effectuées par un utilisateur particulier, même pour un groupe de ressources qui n’existe plus.

        Get-AzureRmLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com

## Interface CLI Azure pour afficher les journaux d’audit

1. Pour récupérer les entrées de journal, exécutez la commande **azure group log show**.

        azure group log show ExampleGroup

2. Vous pouvez filtrer les résultats avec un utilitaire JSON comme [jq](http://stedolan.github.io/jq/download/). L’exemple suivant montre comment rechercher des opérations qui ont mis à jour un fichier de configuration web.

        azure group log show ExampleGroup --json | jq ".[] | select(.operationName.localizedValue == "Update web sites config")"

3. Vous pouvez rechercher les actions d’un utilisateur particulier.

        azure group log show ExampleGroup --json | jq ".[] | select(.caller=="someone@contoso.com")"

## API REST pour afficher les journaux d’audit

Les opérations REST à utiliser avec le journal d’audit font partie de l’[API REST Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx). Pour récupérer les événements du journal d’audit, consultez [Liste des événements de gestion dans un abonnement](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## Étapes suivantes

- Les journaux d’audit Azure sont utilisables avec Power BI pour obtenir des informations plus détaillées sur les actions de votre abonnement. Consultez le billet de blog sur [l’affichage et l’analyse des journaux d’audit Azure dans Power BI](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
- Pour en savoir plus sur la définition de stratégies de sécurité, consultez [Contrôle d’accès en fonction du rôle Azure](./active-directory/role-based-access-control-configure.md).
- Pour en savoir plus sur les commandes qui permettent de résoudre les problèmes liés aux déploiements, consultez [Résolution des problèmes liés aux déploiements de groupes de ressources dans Azure](resource-manager-troubleshoot-deployments-portal.md).
- Pour savoir comment empêcher des suppressions sur une ressource pour tous les utilisateurs, consultez [Verrouiller des ressources avec Azure Resource Manager](resource-group-lock-resources.md).

<!---HONumber=AcomDC_0413_2016-->