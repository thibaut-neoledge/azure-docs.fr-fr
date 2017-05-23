---
title: "Création et gestion des règles de pare-feu Azure Database pour MySQL à l’aide du portail Azure | Microsoft Docs"
description: "Création et gestion des règles de pare-feu Azure Database pour MySQL à l’aide du portail Azure"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 744f3503d2abc618095351e1b018cab22adcf091
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-using-the-azure-portal"></a>Création et gestion des règles de pare-feu Azure Database pour MySQL à l’aide du portail Azure
Les règles de pare-feu au niveau du serveur permettent aux administrateurs d’accéder à un serveur Azure Database pour MySQL à partir d’une adresse IP spécifiée ou d’une plage d’adresses IP. 

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Créer une règle de pare-feu au niveau du serveur dans le portail Azure

1. Dans le panneau du serveur MySQL, sous le titre Paramètres, cliquez sur **Sécurité des connexions** afin d’ouvrir le panneau Sécurité des connexions pour la base de données Azure pour MySQL.
![Portail Azure - cliquez sur Sécurité des connexions](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Cliquez sur **Ajouter mon adresse IP** dans la barre d’outils. Cette opération créera automatiquement une règle avec l’adresse IP de votre ordinateur, telle que détectée par le système Azure.
![Portail Azure - cliquez sur Ajouter mon adresse IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Vérifiez votre adresse IP avant d’enregistrer la configuration. Dans certains cas, l’adresse IP identifiée par le portail Azure diffère de l’adresse IP utilisée lors de l’accès à Internet et aux serveurs Azure. Par conséquent, vous devrez peut-être modifier l’adresse IP de début et l’adresse IP de fin pour que la règle fonctionne comme prévu.

Utilisez un moteur de recherche ou tout autre outil en ligne pour vérifier votre adresse IP (par exemple, lancez une recherche « quelle est mon adresse IP »).
![Recherche Bing « quelle est mon adresse IP »](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Cliquez sur **Enregistrer** sur la barre d’outils pour enregistrer cette règle de pare-feu de niveau serveur. Attendez la confirmation que la mise à jour des règles de pare-feu a réussi.
![Portail Azure - cliquez sur Enregistrer](./media/howto-manage-firewall-using-portal/4-save-firewall-rule.png)

> [!NOTE]
> Dans les règles de pare-feu de la base de données MySQL, vous pouvez spécifier une seule adresse IP ou une plage d’adresses. Si vous souhaitez limiter la règle à une seule adresse IP, saisissez la même adresse dans le champ d’adresse IP de début et d’adresse IP de fin. Ouvrir le pare-feu permet aux administrateurs et utilisateurs de se connecter à toute base de données sur le serveur MySQL pour laquelle ils disposent d’informations d’identification valides.
![Portail Azure - règles de pare-feu](./media/howto-manage-firewall-using-portal/5-specify-addresses.png)

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gérer des règles de pare-feu au niveau du serveur existantes via le portail Azure
Répétez les étapes pour gérer les règles de pare-feu.
* Pour ajouter l’ordinateur actuel, cliquez sur **Ajouter mon adresse IP**.
* Pour ajouter des adresses IP supplémentaires, remplissez les champs **Nom de la règle**, **Adresse IP de début** et **Adresse IP de fin**.
* Pour modifier une règle existante, cliquez sur les champs dans la règle et modifiez-les.
* Pour supprimer une règle existante, cliquez sur les points de suspension [...] puis sur **Supprimer**.
* Cliquez sur **Enregistrer** pour enregistrer les modifications.

## <a name="next-steps"></a>Étapes suivantes
- Pour vous aider à vous connecter à un serveur Azure Database pour MySQL, consultez la rubrique [Bibliothèques de connexions pour Azure Database pour MySQL](./concepts-connection-libraries.md)
