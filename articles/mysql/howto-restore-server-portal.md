---
title: Guide pratique pour restaurer un serveur dans Azure Database pour MySQL | Microsoft Docs
description: "Cet article décrit comment restaurer un serveur Azure Database pour MySQL à l’aide du portail."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0603119da20e74b423072ce6afdb8c9f20830383
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---

# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-portal"></a>Guide pratique pour sauvegarder et restaurer un serveur Azure Database pour MySQL à l’aide du portail Azure

## <a name="backup-happens-automatically"></a>La sauvegarde s’effectue automatiquement
Lorsque vous utilisez Azure Database pour MySQL, le service de base de données crée automatiquement une sauvegarde du service toutes les 5 minutes. 

Les sauvegardes sont disponibles pendant 7 jours avec le niveau De base et 35 jours lors de l’utilisation de niveau Standard. Pour plus d’informations, consultez [Niveaux de service d’Azure Database pour MySQL](concepts-service-tiers.md)

À l’aide de cette fonctionnalité de sauvegarde automatique, vous pouvez restaurer le serveur et toutes ses bases de données dans un nouveau serveur à un moment antérieur.

## <a name="restore-in-the-azure-portal"></a>Restauration dans le portail Azure
Azure Database pour MySQL vous permet de restaurer le serveur à un moment donné et dans une nouvelle copie du serveur. Vous pouvez utiliser ce nouveau serveur pour récupérer vos données. 

Par exemple, si une table a été accidentellement supprimée à midi aujourd'hui, vous pourrez restaurer à l’heure juste avant midi et retrouver la table et les données manquantes à partir de cette nouvelle copie du serveur.

Les étapes suivantes restaurent l’exemple de serveur à un point dans le temps :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Recherchez votre serveur Azure Database pour MySQL. Dans le volet gauche, sélectionnez **Toutes les ressources**, puis choisissez votre serveur dans la liste.

3.  En haut du panneau de vue d’ensemble du serveur, cliquez sur **Restaurer** dans la barre d’outils. Le panneau de restauration s’ouvre.
![Cliquez sur le bouton Restaurer](./media/howto-restore-server-portal/click-restore-button.png)

4. Remplissez le formulaire Restaurer avec les informations requises :

- **Point de restauration (UTC)** : à l’aide du sélecteur de date et d’heure, sélectionnez un point dans le temps à restaurer. L’heure étant spécifiée au format UTC, vous devrez probablement convertir l’heure locale en heure UTC.
- **Restaurer sur un nouveau serveur** : indiquez le nom d’un nouveau serveur vers lequel le serveur existant sera restauré.
- **Emplacement**: le champ de la région est automatiquement défini sur la région du serveur source et ne peut pas être modifié.
- **Niveau de tarification** : le niveau de tarification est automatiquement défini sur le même niveau de tarification que le serveur source et ne peut pas être modifié ici. 
![Restauration PITR](./media/howto-restore-server-portal/pitr-restore.png)

5. Cliquez sur **OK** pour restaurer le serveur à restaurer à un moment donné. 

6. Une fois la restauration terminée, recherchez le nouveau serveur créé pour vérifier que les bases de données ont été restaurées correctement.

## <a name="next-steps"></a>Étapes suivantes
- [Bibliothèques de connexions pour Azure Database pour MySQL](concepts-connection-libraries.md)
