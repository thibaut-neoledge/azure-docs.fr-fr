---
title: "Création et gestion des règles de pare-feu Azure Database pour PostgreSQL à l’aide de l’interface de ligne de commande Azure | Microsoft Docs"
description: "Décrit comment créer et gérer des règles de pare-feu Azure Database pour PostgreSQL à l’aide de l’interface de ligne de commande Azure."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5cb9a3e1fe6e5477e399fd7148fc5366f76cea38
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Création et gestion des règles de pare-feu Azure Database pour PostgreSQL à l’aide de l’interface de ligne de commande Azure
Les règles de pare-feu au niveau du serveur permettent aux administrateurs de gérer l’accès à un serveur Azure Database pour PostgreSQL à partir d’une adresse IP spécifique ou d’une plage d’adresses IP. À l’aide de commandes d’interface de ligne de commande Azure pratiques, vous pouvez créer, mettre à jour, supprimer, répertorier et afficher les règles de pare-feu pour gérer votre serveur. Pour une vue d’ensemble des pare-feu Azure Database pour PostgreSQL, consultez la rubrique [Règles de pare-feu d’un serveur Azure Database pour PostgreSQL](concepts-firewall-rules.md)

## <a name="prerequisites"></a>Composants requis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- Un [serveur Azure Database pour PostgreSQL et une base de données](quickstart-create-server-database-azure-cli.md)
- Un utilitaire en ligne de commande [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) installé

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Configuration des règles de pare-feu pour Azure Database pour PostgreSQL
La commande **az postgres server firewall-rule** est utilisée depuis l’interface de ligne de commande Azure pour créer, supprimer, répertorier, afficher et mettre à jour des règles de pare-feu. Cette 

## <a name="login-to-azure-and-list-servers"></a>Connexion à Azure et affichage des serveurs
Connectez-vous en toute sécurité à votre compte Azure à l’aide de l’interface de ligne de commande Azure. Pour cela, utilisez la commande **az login**.
1. Exécutez la commande suivante à partir de la ligne de commande.
```azurecli
az login
```
Cette commande génère un code à utiliser lors de l’étape suivante.

2. Ouvrez la page [https://aka.ms/devicelogin](https://aka.ms/devicelogin) dans un navigateur web et entrez le code.

3. À l’invite, connectez-vous à l’aide de vos informations d’identification Azure.

4. Une fois votre connexion autorisée, une liste des abonnements s’affiche dans la console.
Copiez l’identifiant de l’abonnement souhaité pour définir l’abonnement actif à utiliser dorénavant.
```azurecli
az account set --subscription {your subscription id}
```
5. Si vous ne connaissez pas le nom de votre abonnement et du groupe de ressources, affichez la liste des serveurs Azure Database for PostgreSQL.

```azurecli
az postgres server list --resource-group myresourcegroup
```
Notez l’attribut de nom dans la liste ; il servira à spécifier le serveur PostgreSQL sur lequel vous allez travailler. Si nécessaire, vérifiez les informations permettant d’utiliser l’attribut de nom sur ce serveur pour vérifier que le nom est correct :

```azurecli
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

## <a name="list-firewall-rules"></a>Répertorier les règles de pare-feu 
À l’aide du nom du serveur et du nom de groupe de ressources, répertoriez les règles de pare-feu existantes sur le serveur. Notez que l’attribut de nom de serveur est spécifié dans le commutateur **-server** et non pas dans le commutateur **--name**.
```azurecli
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401
```
Le cas échéant, une liste des règles s’affiche, au format JSON par défaut. Vous pouvez utiliser le commutateur **--output table** pour obtenir un format de tableau plus lisible.
```azurecli
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401 --output table
```
## <a name="create-firewall-rule"></a>Création d’une règle de pare-feu
À l’aide du nom du serveur Azure Database pour PostgreSQL et du nom du groupe de ressources, créez une nouvelle règle de pare-feu sur le serveur. Indiquez le nom de la règle ainsi que les adresses IP de début et de fin de la règle pour couvrir une plage d’adresses IP autorisant l’accès.
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup  --server mypgserver-20170401 --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```
Afin d’autoriser l’accès pour une adresse IP unique, fournissez les mêmes adresses IP de début et de fin, comme dans cet exemple.
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup  
--server mypgserver-20170401 --name "Firewall Rule with a Single Address" --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```
En cas de réussite, la commande affiche les détails de la règle de pare-feu que vous avez créée, au format JSON par défaut. En cas d’échec, un message d’erreur apparaît.

## <a name="update-firewall-rule"></a>Mise à jour d’une règle de pare-feu 
À l’aide du nom du serveur Azure Database pour PostgreSQL et du nom du groupe de ressources, mettez à jour une règle de pare-feu existante sur le serveur. Indiquez le nom de la règle de pare-feu existante comme entrée puis les adresses IP de début et de fin à mettre à jour.
```azurecli
az postgres server firewall-rule update --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
En cas de réussite, la commande affiche les détails de la règle de pare-feu que vous avez mise à jour, au format JSON par défaut. En cas d’échec, un message d’erreur apparaît.
> [!NOTE]
> Si la règle de pare-feu n’existe pas, elle sera créée par la commande de mise à jour.

## <a name="show-firewall-rule-details"></a>Affichage des détails de la règle de pare-feu
À l’aide du nom du serveur Azure Database pour PostgreSQL et du nom du groupe de ressources, affichez les détails d’une règle de pare-feu existante sur le serveur. Indiquez le nom de la règle de pare-feu existante comme entrée.
```azurecli
az postgres server firewall-rule show --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1"
```
En cas de réussite, la commande affiche les détails de la règle de pare-feu que vous avez spécifiée, au format JSON par défaut. En cas d’échec, un message d’erreur apparaît.

## <a name="delete-firewall-rule"></a>Suppression d’une règle de pare-feu
À l’aide du nom du serveur Azure Database pour PostgreSQL et du nom du groupe de ressources, supprimez une règle de pare-feu existante du serveur. Indiquez le nom de la règle de pare-feu existante.
```azurecli
az postgres server firewall-rule delete --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1"
```
En cas de réussite, aucun résultat ne s’affiche. En cas d’échec, un message d’erreur apparaît.

## <a name="next-steps"></a>Étapes suivantes
- De même, vous pouvez utiliser un navigateur web pour [créer et gérer les règles de pare-feu Azure Database pour PostgreSQL feu à l’aide du portail Azure](howto-manage-firewall-using-portal.md)
- En savoir plus sur les [règles de pare-feu du serveur Azure Database pour PostgreSQL](concepts-firewall-rules.md)
- Pour vous aider à vous connecter à un serveur Azure Database pour PostgreSQL, consultez la rubrique [Bibliothèques de connexions pour Azure Database pour PostgreSQL](concepts-connection-libraries.md)

