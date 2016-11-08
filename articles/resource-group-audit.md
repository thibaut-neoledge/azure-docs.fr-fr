---
title: Opérations d’audit avec Resource Manager | Microsoft Docs
description: Utilisez le journal d’activité dans Resource Manager pour passer en revue les actions et les erreurs des utilisateurs. Affiche le portail Azure, PowerShell, l’interface CLI Azure et REST.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2016
ms.author: tomfitz

---
# Opérations d’audit avec Resource Manager
Les journaux d’activité vous permettent de déterminer :

* Les opérations qui ont été effectuées sur les ressources de votre abonnement
* Les utilisateurs qui ont lancé l’opération (même si les opérations lancées par un service principal ne retournent pas d’utilisateur en tant qu’appelant)
* Le moment où a eu lieu l’opération
* L’état de l’opération
* Les valeurs d’autres propriétés qui peuvent vous aider à effectuer des recherches sur l’opération

[!INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Cette rubrique traite de l’audit des opérations. Si vous voulez en savoir plus sur les journaux d’activité pour résoudre les problèmes liés à un déploiement, consultez [Résolution des problèmes liés aux déploiements de groupes de ressources dans Azure](resource-manager-troubleshoot-deployments-portal.md).

Vous pouvez récupérer des informations dans les journaux d’activité par le biais du portail, de PowerShell, de l’interface de ligne de commande Azure, de l’API REST Insights ou de [Insights .NET Library](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## Afficher les journaux d’activité à l’aide du portail
1. Pour afficher les journaux d’activité via le portail, sélectionnez **More Services** (Autres services), puis **Journaux d’activité**.
   
    ![sélectionner les journaux d’activité](./media/resource-group-audit/select-audit-logs.png)
2. Le panneau **Journaux d’activité** affiche un résumé des opérations récentes pour tous les groupes de ressources de votre abonnement. Il contient une liste des opérations récentes.
   
    ![afficher des actions](./media/resource-group-audit/audit-summary.png)
3. Pour limiter le nombre d’opérations affichées, sélectionnez d’autres conditions. Par exemple, l’illustration suivante indique les champs **Intervalle de temps** et **Événement lancé par** modifiés pour afficher les actions effectuées par un utilisateur ou une application au cours du mois passé.
   
    ![définir des options de filtre](./media/resource-group-audit/set-filter.png)
4. Sélectionnez **Appliquer** pour afficher les résultats de votre requête.
5. Si vous avez besoin d’exécuter la requête ultérieurement, sélectionnez **Enregistrer** et attribuez un nom à votre requête.
   
    ![enregistrer la requête](./media/resource-group-audit/save-query.png)
6. Pour afficher automatiquement les journaux d’audit d’une ressource ou d’un groupe de ressources en particulier, sélectionnez **Journal d’activité** à partir du panneau correspondant. Notez que le journal d’activité est automatiquement filtré sur la dernière ressource sélectionnée.
   
    ![filtrer par ressource](./media/resource-group-audit/filtered-by-resource.png)

## Afficher les journaux d’activité à l’aide de PowerShell
1. Pour récupérer les entrées de journal, exécutez la commande **Get-AzureRmLog**. Vous spécifiez des paramètres supplémentaires pour filtrer la liste des entrées. Si vous ne spécifiez pas une heure de début et de fin, les entrées de la dernière heure sont retournées. Par exemple, pour récupérer les opérations d’un groupe de ressources pendant la dernière heure d’exécution :
   
        Get-AzureRmLog -ResourceGroup ExampleGroup
   
    L’exemple suivant montre comment utiliser le journal d’audit pour rechercher les opérations effectuées à un moment spécifié. Les dates de début et de fin sont indiquées dans un format de date.
   
        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00
   
    Vous pouvez également utiliser les fonctions de date pour spécifier la plage de dates, par exemple, les 14 derniers jours.
   
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

## Afficher les journaux d’activité à l’aide de la CLI Azure
1. Pour récupérer les entrées de journal, exécutez la commande **azure group log show**.
   
        azure group log show ExampleGroup
2. Vous pouvez filtrer les résultats avec un utilitaire JSON comme [jq](http://stedolan.github.io/jq/download/). L’exemple suivant montre comment rechercher des opérations qui ont mis à jour un fichier de configuration web.
   
        azure group log show ExampleGroup --json | jq ".[] | select(.operationName.localizedValue == "Update web sites config")"
3. Vous pouvez rechercher les actions d’un utilisateur particulier.
   
        azure group log show ExampleGroup --json | jq ".[] | select(.caller=="someone@contoso.com")"

## API REST pour afficher les journaux d’audit
Les opérations REST à utiliser avec le journal d’activité font partie de l’[API REST Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx). Pour récupérer les événements du journal d’activité, consultez [Liste des événements de gestion dans un abonnement](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## Étapes suivantes
* Les journaux d’activité Azure sont utilisables avec Power BI pour obtenir des informations plus détaillées sur les actions de votre abonnement. Consultez le billet de blog sur [l’affichage et l’analyse des journaux d’activité Azure dans Power BI](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Pour en savoir plus sur la définition de stratégies de sécurité, consultez [Contrôle d’accès en fonction du rôle Azure](active-directory/role-based-access-control-configure.md).
* Pour en savoir plus sur les commandes qui permettent de résoudre les problèmes liés aux déploiements, consultez [Résolution des problèmes liés aux déploiements de groupes de ressources dans Azure](resource-manager-troubleshoot-deployments-portal.md).
* Pour savoir comment empêcher des suppressions sur une ressource pour tous les utilisateurs, consultez [Verrouiller des ressources avec Azure Resource Manager](resource-group-lock-resources.md).

<!---HONumber=AcomDC_0824_2016-->