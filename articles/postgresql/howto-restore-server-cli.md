---
title: Comment restaurer un serveur dans Azure Database pour PostgreSQL | Microsoft Docs
description: "Cet article décrit comment sauvegarder et restaurer un serveur dans Azure Database pour PostgreSQL à l’aide de l’interface de ligne de commande (CLI) Azure."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: fa63ccedb455e4972bfdd0e15069ad5e932b0fa3
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017

---

# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-cli"></a>Comment sauvegarder et restaurer un serveur Azure Database pour PostgreSQL à l’aide de l’interface Azure CLI

## <a name="prerequisites"></a>Composants requis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- Un [serveur Azure Database pour PostgreSQL et une base de données](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="backup-happens-automatically"></a>La sauvegarde s’effectue automatiquement
Lorsque vous utilisez Azure Database pour PostgreSQL, le service de base de données crée automatiquement une sauvegarde du service toutes les 5 minutes. 

Les sauvegardes sont disponibles pendant 7 jours avec le niveau De base et 35 jours lors de l’utilisation de niveau Standard. Pour plus d’informations, consultez [Niveaux tarifaires d’Azure Database pour PostgreSQL](concepts-service-tiers.md).

À l’aide de cette fonctionnalité de sauvegarde automatique, vous pouvez restaurer le serveur et toutes ses bases de données dans un nouveau serveur à un état antérieur.

## <a name="restore-a-database-to-a-previous-point-in-time-using-the-azure-cli"></a>Restaurer une base de données à un état antérieur à l’aide de l’interface Azure CLI
Azure Database pour PostgreSQL vous permet de restaurer le serveur à un état antérieur. Les données restaurées sont copiées dans un nouveau serveur et le serveur existant est conservé tel quel. Par exemple, si une table a été accidentellement supprimée à midi aujourd’hui, vous pouvez restaurer le serveur à l’état qu’il présentait juste avant midi. Il ne vous reste alors qu’à récupérer la table et les données manquantes à partir de la copie restaurée du serveur. 

Pour effectuer la restauration, utilisez la commande d’interface Azure CLI [az postgres server restore](/cli/azure/postgres/server#restore).

### <a name="run-the-restore-command"></a>Exécuter la commande de restauration
```azurecli-interactive
az postgres server restore --resource-group myResourceGroup --name mypgserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mypgserver-20170401
```

La commande `az postgres server restore` requiert les paramètres suivants :
| Paramètre | Valeur suggérée | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Groupe de ressources dans lequel le serveur source se trouve.  |
| name | mypgserver-restored | Nom du nouveau serveur créé par la commande de restauration. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Choisissez le point dans le temps à utiliser pour la restauration. Cette date et cette heure doivent être comprises dans la période de rétention de sauvegarde du serveur source. Utilisez le format de date et d’heure ISO8601. Par exemple, vous pouvez utiliser votre propre fuseau horaire local, comme `2017-04-13T05:59:00-08:00`, ou le format UTC `2017-04-13T13:59:00Z`. |
| source-server | mypgserver-20170401 | Nom ou ID du serveur source à partir duquel effectuer la restauration. |

La restauration d’un serveur à un état antérieur entraîne la création d’un nouveau serveur, copiant le serveur d’origine tel qu’il était au point dans le temps que vous spécifiez. Les valeurs d’emplacement et de niveau tarifaire du serveur restauré sont les mêmes que celles du serveur source. La commande est synchrone et est renvoyée après que le serveur a été restauré. 

Une fois la restauration terminée, recherchez le serveur qui a été créé. Vérifiez que les données ont été restaurées comme prévu.

## <a name="next-steps"></a>Étapes suivantes
[Bibliothèques de connexions pour Azure Database pour PostgreSQL](concepts-connection-libraries.md)

