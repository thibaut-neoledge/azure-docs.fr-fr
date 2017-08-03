---
title: "Sauvegarde et restauration d’un serveur dans Base de données Azure pour PostgreSQL | Microsoft Docs"
description: "Découvrez comment sauvegarder et restaurer un serveur dans Base de données Azure pour PostgreSQL à l’aide de l’interface Azure CLI."
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
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 4cd77c4ae4d9487aad11ea790c5d88a4eaff6077
ms.contentlocale: fr-fr
ms.lasthandoff: 07/08/2017

---

# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql-by-using-the-azure-cli"></a>Sauvegarde et restauration d’un serveur Base de données Azure pour PostgreSQL à l’aide de l’interface Azure CLI

Utilisez Base de données Azure pour PostgreSQL afin de restaurer une base de données de serveur à une date antérieure couvrant une période de 7 à 35 jours.

## <a name="prerequisites"></a>Composants requis
Pour utiliser ce guide pratique, il vous faut :
- Un [serveur Azure Database pour PostgreSQL et une base de données](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Si vous installez et utilisez l’interface Azure CLI localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour mettre en œuvre la procédure décrite dans ce guide pratique. Pour vérifier la version, à l’invite de commande de l’interface Azure CLI, entrez `az --version`. Pour installer ou mettre à niveau l’interface Azure CLI, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="back-up-happens-automatically"></a>La sauvegarde s’effectue automatiquement
Lorsque vous utilisez Base de données Azure pour PostgreSQL, le service de base de données crée automatiquement une sauvegarde du service toutes les 5 minutes. 

Pour le niveau De base, les sauvegardes sont disponibles pendant 7 jours. Pour le niveau Standard, les sauvegardes sont disponibles pendant 35 jours. Pour plus d’informations, consultez [Niveaux tarifaires dans Base de données Azure pour PostgreSQL](concepts-service-tiers.md).

À l’aide de cette fonctionnalité de sauvegarde automatique, vous pouvez restaurer le serveur et ses bases de données à une date ou un état antérieur.

## <a name="restore-a-database-to-a-previous-point-in-time-by-using-the-azure-cli"></a>Restaurer une base de données à un état antérieur à l’aide de l’interface Azure CLI
Utilisez Base de données Azure pour PostgreSQL pour restaurer le serveur à un état antérieur. Les données restaurées sont copiées dans un nouveau serveur et le serveur existant est conservé tel quel. Par exemple, si une table est accidentellement supprimée à midi aujourd’hui, vous pouvez restaurer le serveur à l’état qu’il présentait juste avant midi. Vous pouvez ensuite récupérer la table et les données manquantes à partir de la copie restaurée du serveur. 

Pour restaurer le serveur, utilisez la commande [az postgres server restore](/cli/azure/postgres/server#restore) de l’interface Azure CLI.

### <a name="run-the-restore-command"></a>Exécuter la commande de restauration

Pour restaurer le serveur, à l’invite de commande de l’interface Azure CLI, entrez la commande suivante :

```azurecli-interactive
az postgres server restore --resource-group myResourceGroup --name mypgserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mypgserver-20170401
```

La commande `az postgres server restore` requiert les paramètres suivants :
| Paramètre | Valeur suggérée | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Groupe de ressources où se trouve le serveur source.  |
| name | mypgserver-restored | Nom du serveur créé par la commande de restauration. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Sélectionnez un état antérieur auquel effectuer la restauration. La date et l’heure doivent être comprises dans la période de rétention de la sauvegarde du serveur source. Utilisez le format de date et d’heure ISO8601. Par exemple, vous pouvez utiliser votre fuseau horaire local, comme `2017-04-13T05:59:00-08:00`. Vous pouvez également utiliser le format UTC Zulu, par exemple, `2017-04-13T13:59:00Z`. |
| source-server | mypgserver-20170401 | Nom ou identifiant du serveur source à partir duquel la restauration s’effectuera. |

Lorsque vous restaurez un serveur à un état antérieur, un nouveau serveur est créé. Le serveur d’origine et ses bases de données à l’état spécifié sont copiés sur le nouveau serveur.

Les valeurs d’emplacement et de niveau tarifaire du serveur restauré restent les mêmes que celles du serveur d’origine. 

La commande `az postgres server restore` est synchrone. Une fois le serveur restauré, vous pouvez l’utiliser à nouveau afin de répéter la procédure pour un autre état. 

Une fois la restauration terminée, recherchez le nouveau serveur et vérifiez que les données ont été restaurées correctement.

## <a name="next-steps"></a>Étapes suivantes
[Bibliothèques de connexions pour Azure Database pour PostgreSQL](concepts-connection-libraries.md)

