<properties
	pageTitle="Créer un rapport d’historique des modifications d’accès | Microsoft Azure"
	description="Générez un rapport qui répertorie toutes les modifications d’accès à vos abonnements Azure avec contrôle d’accès basé sur les rôles au cours des 90 derniers jours."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="03/23/2016"
	ms.author="kgremban"/>

# Créer un rapport d’historique des modifications d’accès

Lorsque vous n’êtes pas le seul propriétaire de vos abonnements Azure, ou des ressources et groupes de ressources qu’ils contiennent, vous devez être en mesure de suivre toutes les modifications d’accès. Chaque fois qu’un utilisateur autorise ou interdit l’accès dans vos abonnements, les modifications sont consignées dans les événements Azure. Vous pouvez créer des rapports d’historique de modification d’accès pour voir toutes les modifications apportées au cours des 90 derniers jours.

## Créer un rapport avec Azure PowerShell
Pour créer un rapport d’historique des modifications d’accès dans PowerShell, utilisez la commande suivante :

```
Get-AzureRMAuthorizationChangeLog
```

Vous pouvez spécifier quelle propriété des affectations répertorier, y compris les suivantes :

| Propriété | Description |
| -------- | ----------- |
| **Action** | Si l’accès a été autorisé ou interdit |
| **Appelant** | Le propriétaire responsable de la modification d’accès |
| **Date** | La date et l’heure de la modification d’accès |
| **DirectoryName** | Le répertoire Azure Active Directory |
| **PrincipalName** | Le nom de l’utilisateur, du groupe ou de l’application |
| **PrincipalType** | Si l’affectation était pour un utilisateur, un groupe ou une application |
| **RoleId** | Le GUID du rôle qui a été accordé ou refusé |
| **RoleName** | Le rôle qui a été accordé ou refusé |
| **ScopeName** | Le nom de l’abonnement, du groupe de ressources ou de la ressource |
| **ScopeType** | Si l’étendue de l’affectation était au niveau de l’abonnement, du groupe de ressources ou de la ressource |
| **SubscriptionId** | Le GUID de l’abonnement Azure |
| **SubscriptionName** | Le nom de l’abonnement Azure |

Cet exemple de commande répertorie toutes les modifications d’accès de l’abonnement au cours des 7 derniers jours :

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![PowerShell Get-AzureRMAuthorizationChangeLog - capture d’écran](./media/role-based-access-control-configure/access-change-history.png)

## Créer un rapport avec l’interface de ligne de commande Azure
Pour créer un rapport d’historique des modifications d’accès dans l’interface de ligne de commande Azure, utilisez la commande suivante :
```
azure authorization changelog
```

## Exporter vers une feuille de calcul
Pour enregistrer le rapport ou manipuler les données, exportez les modifications d’accès vers un fichier .csv. Vous pouvez ensuite afficher le rapport dans une feuille de calcul pour révision.

![ChangeLog affiché en tant que feuille de calcul - capture d’écran](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## Voir aussi
- Prise en main du [contrôle d’accès basé sur les rôles Azure](role-based-access-control-configure.md)
- Utilisation des [rôles personnalisés dans le contrôle d’accès en fonction du rôle (RBAC) Azure](role-based-access-control-custom-roles.md)

<!---HONumber=AcomDC_0330_2016-->