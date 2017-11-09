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
ms.date: 09/15/2017
ms.openlocfilehash: 6c1c0f8a0c0e59661b70b787b551b8cfdb024cda
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Sauvegarde et restauration d’un serveur Azure Database pour MySQL à l’aide du portail Azure

## <a name="backup-happens-automatically"></a>La sauvegarde s’effectue automatiquement
Lorsque vous utilisez Azure Database pour MySQL, le service de base de données crée automatiquement une sauvegarde du service toutes les cinq minutes. 

Les sauvegardes sont disponibles pendant 7 jours avec le niveau De base et 35 jours lors de l’utilisation de niveau Standard. Pour plus d’informations, consultez [Niveaux de service d’Azure Database pour MySQL](concepts-service-tiers.md)

À l’aide de cette fonctionnalité de sauvegarde automatique, vous pouvez restaurer le serveur et toutes ses bases de données dans un nouveau serveur à un moment antérieur.

## <a name="restore-in-the-azure-portal"></a>Restauration dans le portail Azure
Azure Database pour MySQL vous permet de restaurer le serveur à un point dans le temps antérieur et dans une nouvelle copie du serveur. Vous pouvez utiliser ce nouveau serveur pour récupérer vos données. 

Par exemple, si une table a été accidentellement supprimée à midi aujourd’hui, vous pourrez restaurer le serveur à un point dans le temps situé juste avant midi et récupérer la table et les données manquantes à partir de cette nouvelle copie du serveur.

La procédure ci-après restaure l’exemple de serveur à un point dans le temps spécifique :

1. Connectez-vous au [portail Azure](https://portal.azure.com/)

2. Recherchez votre serveur Azure Database pour MySQL. Dans le volet gauche, sélectionnez **Toutes les ressources**, puis choisissez votre serveur dans la liste.

3.  En haut du panneau de vue d’ensemble du serveur, cliquez sur **Restaurer** dans la barre d’outils. Le panneau de restauration s’ouvre.
![Cliquez sur le bouton Restaurer](./media/howto-restore-server-portal/click-restore-button.png)

4. Remplissez le formulaire Restaurer avec les informations requises :

- **Point de restauration (UTC)** : à l’aide du sélecteur de dates et d’heures, sélectionnez un point dans le temps à restaurer. L’heure étant spécifiée au format UTC, vous devrez probablement convertir l’heure locale en heure UTC.
- **Restaurer sur un nouveau serveur** : indiquez le nom d’un nouveau serveur vers lequel le serveur existant sera restauré.
- **Emplacement** : la région est automatiquement définie sur celle du serveur source et n’est pas modifiable.
- **Niveau tarifaire** : le niveau tarifaire est automatiquement défini sur celui du serveur source et n’est pas modifiable à cet emplacement. 
![Restauration PITR](./media/howto-restore-server-portal/pitr-restore.png)

5. Cliquez sur **OK** pour restaurer le serveur au point dans le temps spécifié. 

6. Une fois la restauration terminée, recherchez le serveur nouvellement créé, puis vérifiez que les bases de données ont été restaurées correctement.

## <a name="next-steps"></a>Étapes suivantes
- [Bibliothèques de connexions pour Azure Database pour MySQL](concepts-connection-libraries.md).