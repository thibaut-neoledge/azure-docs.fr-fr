<properties
	pageTitle="Gestion du Contrôle d'accès en fonction du rôle avec l'interface de ligne de commande Azure | Microsoft Azure"
	description="Découvrez comment gérer l'accès en fonction du rôle avec l'interface de ligne de commande Azure en répertoriant les rôles et les actions de rôle, l'affectation de rôles pour l'abonnement et l'application."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="02/29/2016"
	ms.author="kgremban"/>

# Gestion du contrôle d’accès en fonction du rôle avec l’interface de ligne de commande Azure

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Interface de ligne de commande Azure](role-based-access-control-manage-access-azure-cli.md)
- [API REST](role-based-access-control-manage-access-rest.md)

## Répertorier les rôles pour le contrôle d’accès basé sur les rôles (RBAC)

>[AZURE.IMPORTANT] Pour pouvoir utiliser les applets de commande de cet article, vous devez [installer Azure CLI](../xplat-cli-install.md).

###	Répertorier tous les rôles disponibles
Pour répertorier tous les rôles, utilisez :

		azure role list

L'exemple suivant affiche la liste de *tous les rôles disponibles*.

![Ligne de commande Azure RBAC - liste des rôles azure - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

###	Répertorier les actions d'un rôle
Pour répertorier les actions d'un rôle, utilisez :

    azure role show <role in quotes>

L'exemple suivant montre les actions des rôles *Collaborateur* et *Collaborateur de machine virtuelle*.

![Ligne de commande Azure RBAC - affichage des rôles azure - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

##	Répertorier les accès
###	Répertorier les affectations de rôles valables dans un groupe de ressources
Pour répertorier les affectations de rôles valables dans un groupe de ressources, utilisez :

    azure role assignment list --resource-group <resource group name>

L'exemple suivant illustre les affectations de rôle valables pour le groupe *pharma-sales-projecforcast*.

![Ligne de commande Azure RBAC - liste des affectations de rôle azure par groupe - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

###	Répertorier les affectations de rôles d'un utilisateur, notamment les rôles affectés à des groupes d'utilisateurs

L’exemple suivant montre les affectations de rôle effectives sur l’utilisateur **sameert@aaddemo.com*.

![Ligne de commande Azure RBAC - liste des affectations de rôle azure par utilisateur - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

##	Accorder l'accès
Une fois que vous avez identifié le rôle que vous souhaitez affecter, utilisez la commande suivante pour accorder l'accès :

    azure role assignment create

###	Affectation d'un rôle à un groupe pour l'abonnement
Pour affecter un rôle à un groupe pour l'abonnement, utilisez :

	azure role assignment create --objId  <group's object id> --role <name of role> --scope <subscription/subscription id>

L'exemple suivant affecte le rôle *Lecteur* à l'*équipe de Christine Koch* pour l'*abonnement*.

![Ligne de commande Azure RBAC - création des affectations de rôle azure par groupe - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

###	Affectation d'un rôle à une application pour l'abonnement
Pour affecter un rôle à une application pour l'abonnement, utilisez :

    azure role assignment create --objId  <applications's object id> --role <name of role> --scope <subscription/subscription id>

L'exemple suivant affecte le rôle *Collaborateur* à une application *Azure AD* pour l'abonnement sélectionné.

 ![Ligne de commande Azure RBAC - création de liste des affectations de rôle azure par utilisateur](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

###	Affectation d'un rôle à un utilisateur pour le groupe de ressources
Pour affecter un rôle à un utilisateur pour le groupe de ressources, utilisez :

	azure role assignment create --signInName  <user's email address> --roleName <name of role in quotes> --resourceGroup <resource group name>

L'exemple suivant affecte le rôle *Collaborateur de machine virtuelle* à l'utilisateur **samert@aaddemo.com* au groupe de ressources *Pharma-Sales-ProjectForcast*.

![Ligne de commande Azure RBAC - création d’affectation de rôle azure par utilisateur - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

###	Affectation d'un rôle à un groupe au niveau des ressources
Pour affecter un rôle à un groupe au niveau des ressources, utilisez :

    azure role assignment create --objId  <group id> --roleName <name of role in quotes> --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

L'exemple suivant affecte le rôle *Collaborateur de machine virtuelle* à un groupe *Azure AD* dans un *sous-réseau*.

![Ligne de commande Azure RBAC - création des affectations de rôle azure par groupe - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

##	Suppression d'accès
Pour supprimer une affectation de rôle

    azure role assignment delete --objId <object id to from which to remove role> --roleName <role name>

L'exemple suivant supprime l'affectation du rôle *Collaborateur de machine virtuelle* pour le groupe de ressources **sammert@aaddemo.com* *Pharma-Sales-ProjectForcast*. Il supprime ensuite l'affectation de rôle du groupe pour l'abonnement.

![Ligne de commande Azure RBAC - suppression d’affectation de rôle - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## Créer un rôle personnalisé
Pour créer un rôle personnalisé, utilisez la commande `azure role create`.

L’exemple suivant crée un rôle personnalisé appelé *Opérateur de machine virtuelle* qui accorde l’accès à toutes les opérations de lecture des fournisseurs de ressources *Microsoft.Compute*, *Microsoft.Storage* et *Microsoft.Network* ainsi que l’accès pour démarrer, redémarrer et analyser des machines virtuelles. Le rôle personnalisé peut être utilisé dans deux abonnements. Cet exemple utilise un fichier JSON en tant qu’entrée.

![JSON - définition de rôle personnalisé - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-1.png)

![Ligne de commande Azure RBAC - création d’un rôle azure - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## Modifier un rôle personnalisé

Pour modifier un rôle personnalisé, utilisez d’abord la commande `azure role show` pour récupérer la définition de rôle. Apportez ensuite les modifications souhaitées à la définition de rôle. Enfin, utilisez `azure role set` pour enregistrer la définition de rôle modifiée.

L’exemple suivant ajoute l’opération Microsoft.Insights/diagnosticSettings/* à la propriété **Actions** et un abonnement Azure à la propriété **AssignableScopes** du rôle personnalisé Opérateur de machine virtuelle.

![JSON - modifier la définition de rôle personnalisé - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)

![Ligne de commande Azure RBAC - définition d’un rôle azure - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)

## Supprimer un rôle personnalisé

Pour supprimer un rôle personnalisé, utilisez tout d’abord la commande `azure role show` afin de déterminer la propriété **Id** du rôle. Ensuite, utilisez la commande `azure role delete` pour supprimer le rôle en spécifiant la propriété **Id**.

L’exemple suivant supprime le rôle personnalisé *Opérateur de machine virtuelle*.

![Ligne de commande Azure RBAC - suppression d’un rôle azure - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)

## Répertorier les rôles personnalisés

Pour répertorier les rôles pouvant être affectés dans une étendue, utilisez la commande `azure role list`.

L’exemple suivant répertorie tous les rôles pouvant être affectés dans l’abonnement sélectionné.

![Ligne de commande Azure RBAC - liste des rôles azure - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)

Dans l’exemple suivant, le rôle personnalisé *Opérateur de machine virtuelle* n’est pas disponible dans l’abonnement *Production4*, car cet abonnement ne figure pas dans la propriété **AssignableScopes** du rôle.

![Ligne de commande Azure RBAC - liste des rôles azure pour les rôles personnalisés - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)





## Rubriques RBAC
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0302_2016-->