---
title: "Démarrage rapide : création d’un serveur Azure Database pour MySQL - CLI Azure | Microsoft Docs"
description: "Ce guide de démarrage rapide explique comment utiliser l’interface CLI Azure pour créer un serveur Azure Database pour MySQL dans un groupe de ressources Azure."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: portal
ms.workload: 
ms.date: 05/24/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 9f78163e4ff1166a2abd94150d686256ee338286
ms.contentlocale: fr-fr
ms.lasthandoff: 05/25/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-cli"></a>Création d’un serveur Azure Database pour MySQL à l’aide de la CLI Azure
Ce guide de démarrage rapide explique comment utiliser l’interface CLI Azure pour créer un serveur Azure Database pour MySQL dans un groupe de ressources Azure en environ cinq minutes. L’interface de ligne de commande (CLI) Azure permet de créer et gérer des ressources Azure à partir de la ligne de commande ou dans les scripts.

Pour suivre ce guide de démarrage rapide, assurez-vous que vous avez installé la dernière version [d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure
Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran.

```azurecli
az login
```
Suivez les instructions de l’invite de commande pour ouvrir https://aka.ms/devicelog dans votre navigateur, et entrez le code généré dans **l’invite de commande**.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Créez un [groupe de ressources Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) avec la commande [az group create](https://docs.microsoft.com/cli/azure/group#create). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées en tant que groupe.

L’exemple suivant crée un groupe de ressources nommé `mycliresource` à l’emplacement `westus`.

```azurecli
az group create --name mycliresource --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Créer un serveur de base de données Azure pour MySQL
Créez un serveur Azure Database pour MySQL avec la commande **az sql server create**. Un serveur peut gérer plusieurs bases de données. En règle générale, une base de données distincte est utilisée pour chaque projet ou pour chaque utilisateur.

L’exemple suivant crée un serveur Azure Database pour MySQL situé dans `westus` dans le groupe de ressources `mycliresource` avec le nom `mycliserver`. Le serveur dispose d’une connexion administrateur avec le nom `myadmin` et le mot de passe `Password01!`. Le serveur est créé avec le niveau de performance **De base** et **50** unités de calcul partagées entre toutes les bases de données dans le serveur. Vous pouvez faire augmenter ou diminuer le calcul et le stockage selon les besoins de l’application.

```azurecli
az mysql server create --resource-group mycliresource --name mycliserver --location westus --admin-user myadmin --admin-password Password01! --performance-tier Basic --compute-units 50
```

![Création d’un serveur Azure Database pour MySQL à l’aide de la CLI Azure](./media/quickstart-create-mysql-server-database-using-azure-cli/3_az-mysq-server-create.png)

## <a name="configure-firewall-rule"></a>Configurer une règle de pare-feu
Créez une règle de pare-feu au niveau du serveur Azure Database pour MySQL avec la commande **az mysql server firewall-rule create**. Une règle de pare-feu au niveau du serveur permet à une application externe, comme l’outil de ligne de commande **mysql.exe** ou MySQL Workbench de se connecter à votre serveur via le pare-feu du service Azure MySQL. 

L’exemple suivant illustre la création d’une règle de pare-feu pour une plage d’adresses prédéfinie qui, ici, est l’intégralité de la plage d’adresses IP possible.

```azurecli
az mysql server firewall-rule create --resource-group mycliresource --server mycliserver --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
## <a name="configure-ssl-settings"></a>Configurer les paramètres SSL
Par défaut, des connexions SSL entre votre serveur et les applications clientes sont appliquées.  Cela garantit la sécurité des données « en mouvement » en chiffrant le flux de données sur Internet.  Pour faciliter ce démarrage rapide, nous allons désactiver les connexions SSL pour votre serveur.  Cela n’est pas recommandé pour les serveurs de production.  Pour plus de détails, consultez [Configuration de la connectivité SSL dans votre application pour se connecter en toute sécurité à la base de données Azure pour MySQL](./howto-configure-ssl.md).

L’exemple suivant désactive l’application de SSL sur votre serveur MySQL.
 
 ```azurecli
 az mysql server update --resource-group mycliresource --name mycliserver -g -n --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>Obtenir les informations de connexion

Pour vous connecter à votre serveur, vous devez fournir des informations sur l’hôte et des informations d’identification pour l’accès.

```azurecli
az mysql server show --resource-group mycliresource --name mycliserver
```

Le résultat est au format JSON. Prenez note du **fullyQualifiedDomainName** et du **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mycliserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mycliresource/providers/Microsoft.DBforMySQL/servers/mycliserver",
  "location": "westus",
  "name": "mycliserver",
  "resourceGroup": "mycliresource",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "MYSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>Connectez-vous au serveur avec l’outil de ligne de commande mysql.exe
Pour vous connecter à votre serveur à l’aide de l’outil de ligne de commande **mysql.exe**, assurez-vous que vous disposez de l’installation de MySQL sur votre ordinateur.  Vous pouvez télécharger MySQL [ici](https://dev.mysql.com/downloads/).

Ouvrez l’invite de commande et entrez ce qui suit : 

1. Connectez-vous au serveur avec l’outil de ligne de commande **mysql** :
```dos
 mysql -h mycliserver.mysql.database.azure.com -u myadmin@mycliserver -p
```

2. Afficher l’état du serveur :
```dos
 mysql> status
```
Si tout se passe bien, l’outil de ligne de commande doit générer ce qui suit :

```dos
C:\Users\v-chenyh>mysql -h mycliserver.mysql.database.azure.com -u myadmin@mycliserver -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.26.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.6.35, for Win64 (x86_64)

Connection id:          65512
Current database:
Current user:           myadmin@116.230.243.143
SSL:                    Not in use
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mycliserver.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    gbk
Conn.  characterset:    gbk
TCP port:               3306
Uptime:                 2 days 9 hours 47 min 20 sec

Threads: 4  Questions: 34833  Slow queries: 2  Opens: 84  Flush tables: 4  Open tables: 1  Queries per second avg: 0.167
--------------

mysql>
```

> [!TIP]
> Pour les autres commandes, consultez le [Manuel de référence de MySQL 5.6 - Chapitre 4.5.1](https://dev.mysql.com/doc/refman/5.6/en/mysql.html).

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Connectez-vous au serveur à l’aide de l’outil MySQL Workbench GUI
1.    Lancez l’application MySQL Workbench sur votre ordinateur client. Vous pouvez télécharger et installer MySQL Workbench à partir [d’ici](https://dev.mysql.com/downloads/workbench/).

2.    Dans la boîte de dialogue **Configurer une nouvelle connexion**, entrez les informations suivantes dans l’onglet **Paramètres** :

| **Paramètres** | **Description** |
|----------------|-----------------|
|    *Nom de connexion* | spécifier un nom pour cette connexion (il peut s’agir de n’importe quoi) |
| *Méthode de connexion* | choisissez Standard (TCP/IP) |
| *Nom d’hôte* | mycliserver.mysql.database.azure.com (nom du serveur noté précédemment) |
| *Port* | 3306 |
| *Nom d’utilisateur* | myadmin@mycliserver (connexion d’administrateur serveur noté précédemment) |
| *Mot de passe* | Veuillez stocker le mot de passe du compte administrateur |

   ![configurer une nouvelle connexion](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

Cliquez sur **Tester la connexion** pour tester si tous les paramètres sont correctement configurés.
À présent, vous pouvez cliquer sur la connexion qui vient d’être créée pour vous connecter au serveur.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez pas besoin de ces ressources pour un autre didacticiel de démarrage rapide, vous pouvez les supprimer en procédant comme suit : 

```azurecli
az group delete --name mycliresource
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Conception d’une base de données MySQL avec Azure CLI](./tutorial-design-database-using-cli.md)

