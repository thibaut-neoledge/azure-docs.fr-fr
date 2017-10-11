---
title: "Sauvegarder un serveur Exchange dans une sauvegarde Microsoft Azure avec le serveur de sauvegarde Azure | Microsoft Docs"
description: "Découvrez comment sauvegarder un serveur Exchange dans une sauvegarde Microsoft Azure avec le serveur de sauvegarde Azure."
services: backup
documentationcenter: 
author: pvrk
manager: shivamg
editor: 
ms.assetid: e46557e8-2eaf-4ee0-99ea-00fbb8687dca
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: pullabhk
ms.openlocfilehash: 60b784fd00013c2b9504f8635c6b5c4c592563be
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-azure-backup-server"></a>Sauvegarder un serveur Exchange dans une sauvegarde Microsoft Azure avec le serveur de sauvegarde Azure
Cet article explique comment configurer un serveur de sauvegarde Azure pour sauvegarder un serveur Microsoft Exchange dans une sauvegarde Microsoft Azure.  

## <a name="prerequisites"></a>Composants requis
Avant de continuer, assurez-vous que le serveur de sauvegarde Azure est [installé et prêt](backup-azure-microsoft-azure-backup.md).

## <a name="mabs-protection-agent"></a>Agent de protection du serveur de sauvegarde Azure
Pour installer l’agent de protection du serveur de sauvegarde Azure sur le serveur Exchange, procédez comme suit :

1. Assurez-vous que les pare-feux sont correctement configurés. Consultez la page [Configuration d’exceptions de pare-feu pour l’agent](https://technet.microsoft.com/library/Hh758204.aspx).
2. Installez l’agent sur le serveur Exchange, en cliquant sur **Gestion > Agents > Installer** dans la console administrateur du serveur de sauvegarde Azure. Pour obtenir des instructions détaillées, consultez la page [Installation de l’agent de protection du serveur de sauvegarde Azure](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396).

## <a name="create-a-protection-group-for-the-exchange-server"></a>Créer un groupe de protection pour le serveur Exchange
1. Dans la console administrateur du serveur de sauvegarde Azure, cliquez sur **Protection**, puis sélectionnez **Nouveau** dans la barre d’outils pour ouvrir l’assistant **Créer un groupe de protection**.
2. Dans l’écran d’**accueil** de l’assistant, cliquez sur **Suivant**.
3. Dans l’écran **Sélectionner le type de groupe de protection**, sélectionnez **Serveurs**, puis cliquez sur **Suivant**.
4. Sélectionnez la base de données du serveur Exchange que vous souhaitez protéger, puis cliquez sur **Suivant**.

   > [!NOTE]
   > Si vous protégez Exchange 2013, vérifiez les [Conditions préalables pour Exchange 2013](https://technet.microsoft.com/library/dn751029.aspx).
   >
   >

    Dans l’exemple suivant, la base de données Exchange 2010 est sélectionnée.

    ![Sélectionner les membres du groupe](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Sélectionnez la méthode de protection des données.

    Attribuez un nom au groupe de protection, puis sélectionnez les deux options suivantes :

   * Je souhaite une protection à court terme à l’aide de Disque.
   * Je voudrais une protection en ligne.
6. Cliquez sur **Suivant**.
7. Sélectionnez l’option **Exécuter Eseutil pour vérifier l’intégrité des données** si vous souhaitez vérifier l’intégrité des bases de données Exchange Server.

    Une fois cette option sélectionnée, une vérification de la cohérence de sauvegarde s’exécute sur le serveur de sauvegarde Azure, afin d’éviter le trafic d’E/S généré lors de l’exécution de la commande **eseutil** sur le serveur Exchange.

   > [!NOTE]
   > Pour utiliser cette option, vous devez copier les fichiers Ese.dll et Eseutil.exe dans le répertoire C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin sur le serveur de sauvegarde Azure. Dans le cas contraire, l’erreur suivante est déclenchée :   
   > ![erreur eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Cliquez sur **Suivant**.
9. Sélectionnez la base de données pour **Sauvegarde de copie**, puis cliquez sur **Suivant**.

   > [!NOTE]
   > Si vous ne sélectionnez pas « Sauvegarde complète » pour au moins une copie DAG d’une base de données, les journaux ne seront pas tronqués.
   >
   >
10. Configurez les objectifs de **Sauvegarde à court terme**, puis cliquez sur **Suivant**.
11. Vérifiez l’espace disque disponible, puis cliquez sur **Suivant**.
12. Sélectionnez l’heure à laquelle le serveur de sauvegarde Azure doit créer la réplication initiale, puis cliquez sur **Suivant**.
13. Sélectionnez les options de vérification de cohérence, puis cliquez sur **Suivant**.
14. Choisissez la base de données que vous souhaitez sauvegarder sur Azure, puis cliquez sur **Suivant**. Par exemple :

    ![Spécifier les données de protection en ligne](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Définissez la planification pour **Azure Backup**, puis cliquez sur **Suivant**. Par exemple :

    ![Spécifier la planification de sauvegarde en ligne](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Notez que les points de récupération en ligne sont basés sur des points de récupération complète express. Par conséquent, vous devez planifier le point de récupération en ligne après l’heure spécifiée pour le point de récupération complète express.
    >
    >
16. Configurer la stratégie de rétention pour **Azure Backup**, puis cliquez sur **Suivant**.
17. Choisissez une option de réplication en ligne, puis cliquez sur **Suivant**.

    Si vous disposez d’une base de données volumineuse, la création de la sauvegarde initiale sur le réseau peut prendre un long moment. Pour éviter ce problème, vous pouvez créer une sauvegarde hors connexion.  

    ![Spécifier la stratégie de rétention en ligne](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Confirmez les paramètres, puis cliquez sur **Créer un groupe**.
19. Cliquez sur **Fermer**.

## <a name="recover-the-exchange-database"></a>Récupérer la base de données Exchange
1. Pour récupérer une base de données Exchange, cliquez sur **Récupération** dans la console administrateur du serveur de sauvegarde Azure.
2. Localisez la base de données Exchange que vous souhaitez récupérer.
3. Sélectionnez un point de récupération en ligne dans la liste déroulante *Heure de récupération* .
4. Cliquez sur **Récupérer** pour lancer **l’Assistant Récupération**.

Pour les points de récupération en ligne, il existe cinq types de récupération :

* **Récupérer à l’emplacement d’origine du serveur Exchange :** les données seront récupérées sur le serveur Exchange d’origine.
* **Récupérer vers une autre base de données sur un serveur Exchange :** les données seront récupérées dans une autre base de données sur un autre serveur Exchange.
* **Récupérer dans une base de données de récupération :** les données seront récupérées dans une base de données de récupération Exchange (RDB).
* **Copier dans un dossier réseau :** les données seront récupérées dans un dossier réseau.
* **Copier sur bande :** si une bibliothèque de bandes ou un lecteur de bandes autonome sont attachés et configurés sur le serveur de sauvegarde Azure, le point de récupération est copié sur une bande disponible.

    ![Choisir la réplication en ligne](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Étapes suivantes
* [Azure Backup - Forum Aux Questions](backup-azure-backup-faq.md)
