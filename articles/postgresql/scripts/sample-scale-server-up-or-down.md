---
title: "Script CLI Azure - Mettre à l’échelle Azure Database pour PostgreSQL | Microsoft Docs"
description: "Exemple de script Azure CLI - Mettre à l’échelle le serveur Azure Database pour PostgreSQL vers un autre niveau de performances après interrogation des métriques."
services: postgresql
author: salonisonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.custom: mvc
ms.topic: sample
ms.date: 05/31/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 75efaa7dd6165fe0a3d3e35928107cae71e23d5a
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017

---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Surveiller et mettre à l’échelle d’un seul serveur PostgreSQL à l’aide de la CLI Azure
Cet exemple de script CLI met à l’échelle un serveur Azure Database pour PostgreSQL vers un nouveau niveau de performance après l’analyse des métriques. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exemple de script
Dans cet exemple de script, modifiez les lignes en surbrillance pour personnaliser le nom d’utilisateur et le mot de passe d’administrateur. Remplacez l’ID d’abonnement utilisé dans les commandes de surveillance az par votre propre ID d’abonnement.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh?highlight=15-16 "Créez et mettez à l’échelle Base de données Azure pour PostgreSQL.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Supprimez le groupe de ressources.")]

## <a name="script-explanation"></a>Explication du script
Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| **Commande** | **Remarques** |
|---|---|
| [az group create](/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az postgres server create](/cli/azure/postgres/server#create) | Crée un serveur PostgreSQL qui héberge les bases de données. |
| [az monitor metrics list](/cli/azure/monitor/metrics#list) | Affiche la valeur de métrique pour les ressources. |
| [az group delete](/cli/azure/group#delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur Azure CLI : [Documentation d’Azure CLI](/cli/azure/overview)
- Essayer des scripts supplémentaires : [Exemples Azure CLI pour Base de données Azure pour PostgreSQL](../sample-scripts-azure-cli.md)
- En savoir plus sur la mise à l’échelle : [Niveaux de service](../concepts-service-tiers.md) et [Unités de calcul et de stockage](../concepts-compute-unit-and-storage.md)

