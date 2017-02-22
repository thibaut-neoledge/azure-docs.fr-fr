---
title: "Fonctionnalités de sécurité pour la protection des sauvegardes hybrides à l’aide de la Sauvegarde Azure | Documents Docs"
description: "Découvrez comment utiliser les fonctionnalités de sécurité dans la Sauvegarde Azure pour renforcer la sécurité des sauvegardes"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 47bc8423-0a08-4191-826d-3f52de0b4cb8
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2016
ms.author: pajosh
translationtype: Human Translation
ms.sourcegitcommit: 4a846e86fa8cefbee04e1e30078a0d2aabdd834d
ms.openlocfilehash: 1b7df71f103e8284bdcc7bc3a27fe44815b12305


---
# <a name="security-features-for-protecting-hybrid-backups-using-azure-backup"></a>Fonctionnalités de sécurité pour la protection des sauvegardes hybrides à l’aide de la Sauvegarde Azure
De plus en plus de clients sont confrontés à des problèmes de sécurité tels que les programmes malveillants, le ransomware, l’intrusion, etc. Ces problèmes de sécurité entraînent une perte de données et le coût des failles de sécurité ne cesse d’augmenter. Pour vous protéger contre ces attaques, la Sauvegarde Azure fournit désormais des fonctionnalités de sécurité pour protéger les sauvegardes hybrides. Cet article explique comment activer et tirer parti de ces fonctionnalités à l’aide de l’Agent Microsoft Azure Recovery Services et du serveur de sauvegarde Microsoft Azure. Ces fonctionnalités sont basées sur trois piliers de la sécurité :

1. **Prévention** : une couche supplémentaire d’authentification est ajoutée à chaque fois qu’une opération critique (par ex. Modifier la phrase secrète) est effectuée. Cette validation permet de garantir que ces opérations ne peuvent être effectuées que par les utilisateurs ayant des informations d’identification Azure valides.
2. **Alertes** : un e-mail de notification est envoyé à l’administrateur de l’abonnement à chaque fois qu’une opération critique (par ex. Supprimer les données de sauvegarde) est effectuée. Cet e-mail garantit que l’utilisateur est averti de ces actions en temps voulu.
3. **Récupération** : les données de sauvegarde supprimées sont conservées pendant 14 jours supplémentaires à compter de la date de suppression. Ceci garantit que les données peuvent être récupérées dans un délai donné, afin d’éviter toute perte de données, même en cas d’attaque. En outre, les points de récupération minimum sont conservés en plus grand nombre pour offrir une protection contre les données corrompues.

> [!NOTE]
> Vous ne devez activer les fonctionnalités de sécurité que si vous utilisez : <br/>
> * **Agent MAB** : version minimale de l’agent 2.0.9052. Une fois que vous avez activé ces fonctionnalités, vous devez mettre à niveau vers cette version de l’agent pour effectuer des opérations critiques comme Modifier la phrase secrète, Arrêter la sauvegarde avec suppression des données <br/>
> * **Serveur de sauvegarde Azure** : version minimale de l’agent MAB 2.0.9052 avec le serveur de sauvegarde Azure Mise à jour 1 <br/>
> * **DPM** : n’activez pas ces fonctionnalités pour DPM. Ces fonctionnalités seront bientôt disponibles, donc leur activation n’aura aucun impact sur les fonctionnalités existantes. <br/>
> * **Sauvegarde de machine virtuelle IaaS** : n’activez pas ces fonctionnalités pour la sauvegarde de machine virtuelle IaaS. Ces fonctionnalités ne sont pas encore disponibles pour la sauvegarde de machine virtuelle IaaS, donc leur activation n’aura aucun impact sur la sauvegarde de machine virtuelle IaaS.
> * Une fois activées, les fonctionnalités de sécurité sont disponibles pour toutes les machines de l’Agent Azure Recovery Services (MARS) et tous les serveurs de sauvegarde Azure inscrits auprès du coffre. <br/>
> * L’activation de ce paramètre ne s’effectue qu’une seule fois et vous ne pouvez pas désactiver ces fonctionnalités après leur activation. <br/>
> * Cette fonctionnalité n’est disponible que pour le coffre Recovery Services.
>
>

## <a name="enabling-security-features"></a>Activation des fonctionnalités de sécurité
Les utilisateurs qui créent un coffre Recovery Services peuvent profiter de toutes les fonctionnalités de sécurité. Pour les coffres Recovery Services existants, utilisez la procédure suivante pour activer ces fonctionnalités :

1. Connectez-vous au portail Azure à l’aide des informations d’identification Azure
2. Tapez Recovery Services dans le menu Hub pour accéder à la liste des services de récupération.

    ![Créer un archivage de Recovery Services - Étape 1](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    La liste des coffres Recovery Services s’affiche. Dans cette liste, sélectionnez un coffre.

    Le tableau de bord de l’archivage sélectionné s'ouvre.
3. Dans la liste d’éléments qui s’affiche sous le coffre, cliquez sur **Propriétés** sous **Paramètres**.

    ![Ouverture des propriétés du coffre](./media/backup-azure-security-feature/vault-list-properties.png)
4. Cliquez sur **Mettre à jour** sous **Paramètres de sécurité**.

    ![Ouverture des paramètres de sécurité](./media/backup-azure-security-feature/security-settings-update.png)

    Le lien Mettre à jour ouvre le panneau Paramètres de sécurité, ce qui vous permet d’activer ces fonctionnalités et d’en obtenir une courte présentation.
5. Sélectionnez une valeur dans la liste déroulante **Avez-vous configuré Azure Multi-Factor Authentication ?** pour confirmer si vous avez activé [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md). Si tel est le cas, vous êtes invité à vous authentifier à partir d’un autre appareil (par exemple, un téléphone mobile) lors de votre connexion au portail Azure.

   Dans le cadre des fonctionnalités de sécurité, lorsque des opérations critiques sont exécutées dans la Sauvegarde Azure, vous devez entrer un code PIN de sécurité disponible sur le portail Azure. L’activation d’Azure Multi-Factor Authentication ajoute une couche de sécurité supplémentaire, garantissant ainsi que seuls les utilisateurs autorisés avec des informations d’identification Azure valides et authentifiés à partir d’un deuxième appareil peuvent accéder au portail Azure et exécuter des opérations critiques.
6. Utilisez le bouton bascule **Activer** et cliquez sur **Enregistrer** en haut de la page pour enregistrer les paramètres de sécurité, comme illustré dans la figure. Vous ne pouvez sélectionner **Activer** que si vous avez sélectionné une valeur dans la liste « Avez-vous configuré Azure Multi-Factor Authentication ? » .

    ![Activation des paramètres de sécurité](./media/backup-azure-security-feature/enable-security-settings.png)

## <a name="recovering-deleted-backup-data"></a>Récupération de données de sauvegarde supprimées
Par mesure de sécurité, la Sauvegarde Azure conserve les données de sauvegarde supprimées pendant 14 jours supplémentaires et ne les supprime pas immédiatement si l’opération Arrêter la sauvegarde avec suppression des données de sauvegarde est effectuée. Pour restaurer ces données dans le délai de 14 jours, procédez comme suit :

Pour les utilisateurs **Agent Microsoft Recovery Services (MARS)** :

1. Si l’ordinateur sur lequel les sauvegardes étaient effectuées est toujours disponible, utilisez [Récupération des données sur le même ordinateur](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-the-same-machine) dans l’Agent Microsoft Recovery Services (MARS) pour effectuer la récupération à partir de tous les anciens points de récupération.
2. Si l’ordinateur mentionné ci-dessus n’est pas disponible, utilisez [Récupération sur un autre ordinateur](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) pour utiliser un autre ordinateur Agent Microsoft Recovery Services (MARS) afin d’obtenir ces données.

Pour les utilisateurs **Serveur de sauvegarde Azure** :

1. Si le serveur sur lequel les sauvegardes étaient effectuées est toujours disponible, protégez à nouveau les sources de données supprimées et utilisez la fonctionnalité Récupérer les données pour effectuer la récupération à partir de tous les anciens points de récupération.
2. Si l’ordinateur mentionné ci-dessus n’est pas disponible, utilisez [Récupérer les données à partir d’un autre serveur de sauvegarde Azure](backup-azure-alternate-dpm-server.md#recover-data-from-another-azure-backup-server) pour utiliser un autre serveur de sauvegarde Azure afin d’obtenir ces données.

## <a name="preventing-attacks"></a>Prévention des attaques
Dans le cadre de cette fonctionnalité, des vérifications ont été ajoutées pour s’assurer que seuls les utilisateurs valides peuvent effectuer diverses opérations.

### <a name="authentication-to-perform-critical-operations"></a>Authentification pour exécuter des opérations critiques
Dans le cadre de l’ajout d’une couche supplémentaire d’authentification pour les opérations critiques, vous êtes invité à entrer le code PIN de sécurité lors de l’exécution de l’action Arrêter la protection avec les opérations Supprimer les données et Modifier la phrase secrète.

Pour recevoir le code PIN de sécurité, procédez comme suit :

1. Connectez-vous au portail Azure.
2. Accédez à Coffre Recovery Services > Paramètres > Propriétés.
3. Cliquez sur **Générer** sous Code PIN de sécurité. Le lien Générer ouvre un panneau qui contient le code PIN de sécurité à saisir dans l’interface utilisateur de l’Agent Azure Recovery Services.
    Ce code PIN n’est valide que pendant 5 minutes, après quoi il est généré automatiquement.

### <a name="maintaining-minimum-retention-range"></a>Maintien de la plage de rétention minimale
Les vérifications suivantes ont été ajoutées pour s’assurer qu’il existe toujours un nombre valide de points de récupération disponibles :

1. Pour une rétention quotidienne, une rétention de **sept** jours minimum doit être appliquée
2. Pour une rétention hebdomadaire, une rétention de **quatre** semaines minimum doit être appliquée
3. Pour une rétention mensuelle, une rétention de **trois** mois minimum doit être appliquée
4. Pour une rétention annuelle, une rétention d’**un** an minimum doit être appliquée

## <a name="notifications-for-critical-operations"></a>Notifications pour les opérations critiques
Lorsque certaines opérations critiques sont effectuées, l’administrateur de l’abonnement reçoit un e-mail de notification contenant les détails de l’opération. Si vous souhaitez configurer des adresses de messagerie supplémentaires pour la réception des notifications, vous pouvez utiliser le portail Azure.

Les fonctionnalités de sécurité mentionnées dans cet article fournissent des mécanismes de défense contre les attaques ciblées empêchant ainsi les pirates d’atteindre les sauvegardes. Plus important encore, ces fonctionnalités permettent de récupérer les données si une attaque se produit malgré tout.

## <a name="next-steps"></a>Étapes suivantes
* [Bien démarrer avec le coffre Azure Recovery Services](backup-azure-vms-first-look-arm.md) pour activer ces fonctionnalités
* [Télécharger le dernier Agent Azure Recovery Services](http://aka.ms/azurebackup_agent) pour protéger les ordinateurs Windows et protéger vos données de sauvegarde contre les attaques
* [Télécharger le dernier serveur de sauvegarde Azure](https://aka.ms/latest_azurebackupserver) pour protéger les charges de travail et protéger vos données de sauvegarde contre les attaques



<!--HONumber=Feb17_HO1-->


