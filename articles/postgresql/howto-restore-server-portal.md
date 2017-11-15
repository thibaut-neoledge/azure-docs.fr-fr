---
title: Comment restaurer un serveur dans Azure Database pour PostgreSQL | Microsoft Docs
description: "Cet article décrit comment restaurer un serveur Azure Database pour PostgreSQL à l’aide du portail Azure."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 903fd2ff446e1963ab5cfcec745766188b74efcf
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2017
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-portal"></a>Comment sauvegarder et restaurer un serveur Azure Database pour PostgreSQL à l’aide du portail Azure

## <a name="backup-happens-automatically"></a>La sauvegarde s’effectue automatiquement
Lorsque vous utilisez Azure Database pour PostgreSQL, le service de base de données crée automatiquement une sauvegarde du serveur toutes les 5 minutes. 

Les sauvegardes sont disponibles pendant 7 jours avec le niveau De base et 35 jours lors de l’utilisation de niveau Standard. Pour plus d’informations, consultez [Niveaux de service d’Azure Database pour PostgreSQL](concepts-service-tiers.md)

À l’aide de cette fonctionnalité de sauvegarde automatique, vous pouvez restaurer le serveur et toutes ses bases de données dans un nouveau serveur à un moment antérieur.

## <a name="restore-in-the-azure-portal"></a>Restauration dans le portail Azure
Azure Database pour PostgreSQL vous permet de restaurer le serveur à un moment donné et dans une nouvelle copie du serveur. Vous pouvez utiliser ce nouveau serveur pour récupérer vos données. 

Par exemple, si une table a été accidentellement supprimée à midi aujourd'hui, vous pourrez restaurer à l’heure juste avant midi et retrouver la table et les données manquantes à partir de cette nouvelle copie du serveur.

Les étapes suivantes restaurent l’exemple de serveur à un point dans le temps :
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Recherchez votre serveur Azure Database pour PostgreSQL. Dans le portail Azure, cliquez sur **Toutes les ressources** à partir du menu de gauche et saisissez le nom du serveur, par exemple **mypgserver-20170401**, pour rechercher votre serveur existant. Cliquez sur le nom du serveur figurant dans les résultats de la recherche. La page **Présentation** pour votre serveur s’ouvre et propose des options pour poursuivre la configuration de la page.

   ![Portail Azure - Recherche pour localiser votre serveur](media/postgresql-howto-restore-server-portal/1-locate.png)

3. Dans la barre d’outils de la page de vue d’ensemble du serveur, cliquez sur **Restaurer**. La page Restaurer s’ouvre.

   ![Azure Database pour PostgreSQL - Vue d’ensemble - Bouton Restaurer](./media/postgresql-howto-restore-server-portal/2_server.png)

4. Remplissez le formulaire Restaurer avec les informations requises :

   ![Azure Database pour PostgreSQL - Informations de restauration ](./media/postgresql-howto-restore-server-portal/3_restore.png)
  - **Point de restauration :** sélectionnez un point dans le temps avant la modification du serveur.
  - **Serveur cible :** spécifiez un nouveau nom de serveur sur lequel vous souhaitez effectuer la restauration.
  - **Emplacement :** vous ne pouvez pas sélectionner la région. Par défaut, elle est identique à celle du serveur source.
  - **Niveau tarifaire** : vous ne pouvez pas modifier cette valeur lors de la restauration d’un serveur. Identique au serveur source. 

5. Cliquez sur **OK** pour restaurer le serveur à restaurer à un moment donné. 

6. Une fois la restauration terminée, recherchez le nouveau serveur créé pour vérifier que les données ont été restaurées correctement.

## <a name="next-steps"></a>Étapes suivantes
- [Bibliothèques de connexions pour Azure Database pour PostgreSQL](concepts-connection-libraries.md)
