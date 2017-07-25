---
title: "Script CLI Azure - Créer une instance d’Azure Database pour PostgreSQL | Microsoft Docs"
description: "Exemple de script CLI - crée un serveur Azure Database pour PostgreSQL et configure une règle de pare-feu au niveau du serveur."
services: postgresql
author: salonisonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: azure-cli
ms.topic: sample
ms.date: 05/31/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 18700246b496d037464d86945b24ac59c8d88756
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017

---

# <a name="create-an-azure-database-for-postgresql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Créer un serveur Azure Database pour PostgreSQL et configurer une règle de pare-feu à l’aide de l’interface de ligne de commande Azure
Cet exemple de script CLI crée un serveur Azure Database pour PostgreSQL et configure une règle de pare-feu au niveau du serveur. Une fois que le script a été exécuté avec succès, le serveur PostgreSQL est accessible à partir de tous les services Azure et l’adresse IP configurée.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exemple de script
Dans cet exemple de script, modifiez les lignes en surbrillance pour personnaliser le nom d’utilisateur et le mot de passe d’administrateur.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/create-postgresql-server-and-firewall-rule.sh?highlight=15-16 "Créez une base de données Azure pour PostgreSQL, et des règles de pare-feu au niveau serveur.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/delete-postgresql.sh "Supprimez le groupe de ressources.")]

## <a name="script-explanation"></a>Explication du script
Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| **Commande** | **Remarques** |
|---|---|
| [az group create](/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az postgres server create](/cli/azure/postgres/server#create) | Crée un serveur PostgreSQL qui héberge les bases de données. |
| [az postgres server firewall create](/cli/azure/postgres/server/firewall-rule#create) | Crée une règle de pare-feu pour autoriser l’accès au serveur et aux bases de données qui s’y trouvent à partir de la plage d’adresses IP entrée. |
| [az group delete](/cli/azure/group#delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur Azure CLI : [Documentation d’Azure CLI](/cli/azure/overview)
- Essayer des scripts supplémentaires : [Exemples Azure CLI pour Base de données Azure pour PostgreSQL](../sample-scripts-azure-cli.md)

