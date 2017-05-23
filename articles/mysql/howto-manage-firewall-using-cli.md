---
title: "Création et gestion des règles de pare-feu Azure Database pour MySQL à l’aide de l’interface de ligne de commande Azure | Microsoft Docs"
description: "Décrit comment créer et gérer des règles de pare-feu Azure Database pour MySQL à l’aide de l’interface de ligne de commande Azure."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: d7ff4ae8dbf9610b843c8b48d83b0f3c23ac72d5
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---

# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-using-azure-cli"></a>Création et gestion des règles de pare-feu Azure Database pour MySQL à l’aide de l’interface de ligne de commande Azure
Les règles de pare-feu au niveau du serveur permettent aux administrateurs de gérer l’accès à un serveur Azure Database pour MySQL à partir d’une adresse IP spécifique ou d’une plage d’adresses IP. À l’aide de commandes d’interface de ligne de commande Azure pratiques, vous pouvez créer, mettre à jour, supprimer, répertorier et afficher les règles de pare-feu pour gérer votre serveur. Pour une vue d’ensemble des pare-feu Azure Database pour MySQL, consultez la rubrique [Règles de pare-feu d’Azure Database pour MySQL](./concepts-firewall-rules.md)

## <a name="prerequisites"></a>Composants requis
* [Installation d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)
* Installation du Kit de développement logiciel (SDK) Python pour les services PostgreSQL et MySQL
* Installation du composant Azure CLI pour les services PostgreSQL et MySQL
* Création d’un serveur Azure Database pour MySQL

## <a name="firewall-rule-commands"></a>Commandes des règles de pare-feu :
La commande **az mysql server firewall-rule** est utilisée depuis l’interface de ligne de commande Azure pour créer, supprimer, répertorier, afficher et mettre à jour des règles de pare-feu.

Commandes :
- **create** : créez une règle de pare-feu du serveur Azure MySQL.
- **delete** : supprimez une règle de pare-feu du serveur Azure MySQL.
- **list** : répertoriez les règles de pare-feu du serveur Azure MySQL.
- **show** : affichez les détails d’une règle de pare-feu du serveur Azure MySQL.
- **update**: mettez à jour une règle de pare-feu du serveur Azure MySQL.

## <a name="login-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Se connecter à Azure et répertorier vos serveurs Azure Database pour MySQL
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
5. Si vous ne connaissez pas le nom de votre abonnement et du groupe de ressources, affichez la liste des serveurs Azure Database for MySQL.
```azurecli
az mysql server list --resource-group myResourceGroup
```
Notez l’attribut de nom dans la liste ; il servira à spécifier le serveur MySQL sur lequel vous allez travailler. Si nécessaire, vérifiez les informations permettant d’utiliser l’attribut de nom sur ce serveur pour vérifier que le nom est correct :
```azurecli
az mysql server show --resource-group myResourceGroup --name mysqlserver4demo
```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Répertorier les règles de pare-feu d’un serveur Azure Database pour MySQL 
À l’aide du nom du serveur et du nom de groupe de ressources, répertoriez les règles de pare-feu existantes sur le serveur. Notez que l’attribut de nom de serveur est spécifié dans le commutateur **-server** et non pas dans le commutateur **--name**.
```azurecli
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo
```
Le cas échéant, une liste des règles s’affiche, au format JSON par défaut. Vous pouvez utiliser le commutateur **--output table** pour obtenir un format de tableau plus lisible.
```azurecli
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo --output table
```
## <a name="create-firewall-rule-on-azure-database-for-mysql-server"></a>Création d’une règle de pare-feu d’un serveur Azure Database pour MySQL
À l’aide du nom du serveur Azure pour MyQL et du nom du groupe de ressources, créez une nouvelle règle de pare-feu sur le serveur. Indiquez le nom de la règle ainsi que les adresses IP de début et de fin de la règle pour couvrir une plage d’adresses IP autorisant l’accès.
```azurecli
az mysql server firewall-rule create --resource-group myResourceGroup  --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```
Afin d’autoriser l’accès pour une adresse IP unique, fournissez les mêmes adresses IP de début et de fin, comme dans cet exemple.
```azurecli
az mysql server firewall-rule create --resource-group myResourceGroup  
--server mysql --name "Firewall Rule with a Single Address" --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```
En cas de réussite, la commande affiche les détails de la règle de pare-feu que vous avez créée, au format JSON par défaut. En cas d’échec, un message d’erreur apparaît.

## <a name="update-firewall-rule-on-azure-database-for-mysql-server"></a>Mise à jour d’une règle de pare-feu d’un serveur Azure Database pour MySQL 
À l’aide du nom du serveur Azure pour MySQL et du nom du groupe de ressources, mettez à jour une règle de pare-feu existante sur le serveur. Indiquez le nom de la règle de pare-feu existante comme entrée puis les adresses IP de début et de fin à mettre à jour.
```azurecli
az mysql server firewall-rule update --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
En cas de réussite, la commande affiche les détails de la règle de pare-feu que vous avez mise à jour, au format JSON par défaut. En cas d’échec, un message d’erreur apparaît.

> [!NOTE]
> Si la règle de pare-feu n’existe pas, elle sera créée par la commande de mise à jour.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Affichage des détails d’une règle de pare-feu d’un serveur Azure Database pour MySQL
À l’aide du nom du serveur Azure pour MySQL et du nom du groupe de ressources, affichez les détails d’une règle de pare-feu existante sur le serveur. Indiquez le nom de la règle de pare-feu existante comme entrée.
```azurecli
az mysql server firewall-rule show --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
En cas de réussite, la commande affiche les détails de la règle de pare-feu que vous avez spécifiée, au format JSON par défaut. En cas d’échec, un message d’erreur apparaît.

## <a name="delete-firewall-rule-on-azure-database-for-mysql-server"></a>Suppression d’une règle de pare-feu d’un serveur Azure Database pour MySQL
À l’aide du nom du serveur Azure pour MySQL et du nom du groupe de ressources, supprimez une règle de pare-feu existante du serveur. Indiquez le nom de la règle de pare-feu existante.
```azurecli
az mysql server firewall-rule delete --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
En cas de réussite, aucun résultat ne s’affiche. En cas d’échec, un message d’erreur apparaît.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [règles de pare-feu du serveur Azure Database pour MySQL](./concepts-firewall-rules.md)
- [Création et gestion des règles de pare-feu Azure Database pour MySQL à l’aide du portail Azure](./howto-manage-firewall-using-portal.md)
