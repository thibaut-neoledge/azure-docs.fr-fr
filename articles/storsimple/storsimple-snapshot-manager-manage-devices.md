<properties 
   pageTitle="Gérer les appareils avec le Gestionnaire d’instantanés StorSimple | Microsoft Azure"
   description="Décrit comment utiliser le composant logiciel enfichable MMC du Gestionnaire d’instantanés StorSimple pour connecter et gérer des appareils StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="12/02/2015"
   ms.author="v-sharos" />

# Utiliser le Gestionnaire d’instantanés StorSimple pour connecter et gérer des appareils StorSimple

## Vue d'ensemble

Vous pouvez utiliser les nœuds du volet **Étendue** du Gestionnaire d’instantanés StorSimple afin de vérifier les données importées de l’appareil StorSimple et d’actualiser les appareils de stockage connectés. Par ailleurs, lorsque vous cliquez sur le nœud **Appareils**, vous pouvez consulter une liste des appareils connectés et des informations de statut correspondantes dans le volet **Résultats**.

![Appareils connectés](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Figure 1 : Appareil connecté du Gestionnaire d’instantanés StorSimple**

En fonction de vos sélections **Affichage**, le volet **Résultats** indique les informations suivantes à propos de chaque appareil. (Pour plus d’informations sur la configuration d’une vue, accédez au [Menu Affichage](storsimple-use-snapshot-manager.md#view-menu).)


| Colonne de résultats |Description |
|:----------------|:--------------------| 
| Nom | Le nom de l’appareil, tel que configuré dans le portail Azure Classic|
| Modèle | Le numéro de modèle de l’appareil|
| Version | La version du logiciel installé sur l’appareil |
| Statut | La disponibilité de l’appareil |
| Dernière synchronisation | Date et heure de la dernière synchronisation de l’appareil |
| Numéro de série | Le numéro de série de l’appareil |
 
Si vous cliquez avec le bouton droit sur le nœud **Appareils** du volet **Étendue**, vous pouvez sélectionner les actions suivantes :

- Ajout ou remplacement d’un appareil 
- Connexion d’un appareil et vérification des importations 
- Actualisation des appareils connectés 

Si vous cliquez sur le nœud **Appareils** puis cliquez avec le bouton droit sur le volet **Résultats**, vous pouvez sélectionner les actions suivantes :

- Authentification d’un appareil 
- Affichage des détails sur l’appareil 
- Actualisation d’un appareil 
- Suppression de la configuration d’un appareil 
- Modification du mot de passe d’un appareil

>[AZURE.NOTE]L’ensemble de ces actions sont également disponibles dans le volet **Actions**.
 
Ce didacticiel explique comment utiliser le Gestionnaire d’instantanés StorSimple pour connecter et gérer les appareils et effectuer les tâches suivantes :

- Ajout ou remplacement d’un appareil 
- Connexion d’un appareil et vérification des importations 
- Actualisation des appareils connectés 
- Authentification d’un appareil 
- Affichage des détails sur l’appareil 
- Actualisation d’un appareil 
- Suppression de la configuration d’un appareil 
- Modification d’un mot de passe expiré d’appareil
- Remplacement d’un appareil défaillant

>[AZURE.NOTE]Pour consulter des informations générales sur l’utilisation de l’interface du Gestionnaire d’instantanés StorSimple, accédez à la page [Interface utilisateur du Gestionnaire d’instantanés StorSimple](storsimple-use-snapshot-manager.md).


## Ajout ou remplacement d’un appareil

Exécutez la procédure suivante pour ajouter ou remplacer un appareil StorSimple.

#### Pour ajouter ou remplacer un appareil

1. Cliquez sur l’icône de bureau pour démarrer le Gestionnaire d’instantanés StorSimple.

2. Dans le volet **Étendue**, cliquez avec le bouton droit sur le nœud **Appareils**, puis cliquez sur **Configurer un appareil**. La boîte de dialogue **Configurer un appareil** apparaît.

    ![Configurer un appareil StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png)

3. Dans la liste déroulante **Appareil**, sélectionnez l’adresse IP de l’appareil ou de l’appareil virtuel.

4. Dans la zone de texte **Mot de passe**, entrez le mot de passe du Gestionnaire d’instantanés StorSimple que vous avez créé pour l’appareil dans le portail Azure Classic. Cliquez sur **OK**. Le Gestionnaire d’instantanés StorSimple recherche l’appareil que vous avez identifié.

    - Si l’appareil est disponible, le Gestionnaire d’instantanés StorSimple ajoute une connexion. 

    - Si, pour une raison quelconque, l’appareil est indisponible, le Gestionnaire d’instantanés StorSimple renvoie un message d’erreur. Cliquez sur **OK** pour fermer le message d’erreur, puis cliquez sur **Annuler** pour fermer la boîte de dialogue **Configurer un appareil**.

## Connexion d’un appareil et vérification des importations

Appliquez la procédure suivante pour connecter un appareil StorSimple et vérifier que les groupes de volumes existants qui présentent des sauvegardes associées sont importés.

#### Pour connecter un appareil et vérifier les importations

1. Pour connecter un appareil au Gestionnaire d’instantanés StorSimple, suivez les instructions de la section Ajouter ou remplacer un appareil. Lorsqu’il se connecte à un appareil, le Gestionnaire d’instantanés StorSimple réagit de la manière suivante :

    - Si, pour une raison quelconque, l’appareil est indisponible, le Gestionnaire d’instantanés StorSimple renvoie un message d’erreur. 

   - Si l’appareil est disponible, le Gestionnaire d’instantanés StorSimple ajoute une connexion. Lorsque vous sélectionnez un appareil, il apparaît dans le volet **Résultats**, et le champ de statut indique que l’appareil est **Disponible**. Le Gestionnaire d’instantanés StorSimple importe les groupes de volumes configurés pour l’appareil, à condition que les groupes de volumes présentent des sauvegardes associées. Les stratégies de sauvegarde ne sont pas importées. Les groupes de volumes qui ne présentent pas de sauvegardes associées ne sont pas importés.

2. Cliquez sur l’icône de bureau pour démarrer le Gestionnaire d’instantanés StorSimple.

3. Cliquez sur le nœud supérieur du volet **Étendue**, puis cliquez sur **Basculer l’affichage des importations**.

    ![Sélectionner Basculer l’affichage des importations](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png)

4. La boîte de dialogue **Basculer l’affichage des importations** apparaît ; elle indique le statut des groupes de volumes et des sauvegardes importés. Cliquez sur **OK**.

Une fois que les groupes de volumes et les sauvegardes ont été importés, vous pouvez utiliser le Gestionnaire d’instantanés StorSimple pour les gérer de la manière dont vous géreriez des groupes de volumes et des sauvegardes créés et configurés avec le Gestionnaire d’instantanés StorSimple.

## Actualisation des appareils connectés

Exécutez la procédure suivante afin de synchroniser les appareils StorSimple connectés avec le Gestionnaire d’instantanés StorSimple.

####Pour actualiser les appareils connectés

1. Cliquez sur l’icône de bureau pour démarrer le Gestionnaire d’instantanés StorSimple.

2. Dans le volet **Étendue**, cliquez avec le bouton droit sur **Appareils**, puis cliquez sur **Actualiser la liste des appareils**. Cette action synchronise les appareils connectés avec le Gestionnaire d’instantanés StorSimple, ce qui vous permet de consulter les groupes de volumes et les sauvegardes, notamment les récents ajouts.

    ![Actualiser les appareils StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)
 
L’action **Actualiser la liste des appareils** permet de récupérer les nouveaux groupes de volumes et les sauvegardes associées des appareils connectés. Contrairement à l’action **Relancer l’analyse des volumes** disponible pour le nœud **Volumes**, l’action **Actualiser la liste des appareils** ne restaure pas le registre de sauvegarde.

## Authentification d’un appareil

Exécutez la procédure suivante afin d’authentifier un appareil StorSimple avec le Gestionnaire d’instantanés StorSimple.

#### Pour authentifier un appareil

1. Cliquez sur l’icône de bureau pour démarrer le Gestionnaire d’instantanés StorSimple.

2. Dans le volet **Étendue**, cliquez sur **Appareils**.

3. Dans le volet **Résultats**, cliquez avec le bouton droit sur le nom de l’appareil, puis cliquez sur **Authentifier**.

4. La boîte de dialogue **Authentifier** apparaît. Saisissez le mot de passe de l’appareil, puis cliquez sur **OK**.

    ![Boîte de dialogue Authentifier](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png)
 
## Affichage des détails sur l’appareil

Exécutez la procédure suivante pour consulter les détails sur un appareil StorSimple et, si nécessaire, synchroniser de nouveau l’appareil avec le Gestionnaire d’instantanés StorSimple.

#### Pour afficher et synchroniser de nouveau les détails sur l’appareil

1. Cliquez sur l’icône de bureau pour démarrer le Gestionnaire d’instantanés StorSimple.

2. Dans le volet **Étendue**, cliquez sur **Appareils**.

3. Dans le volet **Résultats**, cliquez sur le nom de l’appareil, puis cliquez sur **Détails**.

4\. La boîte de dialogue **Informations sur l’appareil** s’affiche. Cette zone indique le nom, le modèle, la version, le numéro de série, le statut, le nom qualifié iSCSI cible, ainsi que la date et l’heure de la dernière synchronisation.

   - Cliquez sur **Resynchroniser** pour synchroniser l’appareil.

   - Cliquez sur **OK** ou sur **Annuler** pour fermer la boîte de dialogue.

    ![Détails de l’appareil](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png)
 
## Actualisation d’un appareil

Exécutez la procédure suivante pour synchroniser de nouveau un appareil StorSimple avec le Gestionnaire d’instantanés StorSimple.

#### Pour actualiser un appareil

1. Cliquez sur l’icône de bureau pour démarrer le Gestionnaire d’instantanés StorSimple. 

2. Dans le volet **Étendue**, cliquez sur **Appareils**.

3. Dans le volet **Résultats**, cliquez avec le bouton droit sur le nom de l’appareil, puis cliquez sur **Actualiser l’appareil**. L’appareil est synchronisé avec le Gestionnaire d’instantanés StorSimple.

## Suppression de la configuration d’un appareil

Utilisez la procédure suivante pour supprimer une configuration d’appareil StorSimple du Gestionnaire d’instantanés StorSimple.

#### Pour supprimer une configuration d’appareil

1. Cliquez sur l’icône de bureau pour démarrer le Gestionnaire d’instantanés StorSimple. 

2. Dans le volet **Étendue**, cliquez sur **Appareils**.

3. Dans le volet **Résultats**, cliquez avec le bouton droit sur le nom de l’appareil, puis cliquez sur **Supprimer**.

4. Le message suivant s’affiche. Cliquez sur **Oui** pour supprimer la configuration, ou sur **Non** pour annuler la suppression.

    ![Supprimer la configuration de l’appareil](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## Modification d’un mot de passe expiré d’appareil

Vous devez entrer un mot de passe pour authentifier un appareil StorSimple avec le Gestionnaire d’instantanés StorSimple. Vous définissez ce mot de passe lorsque vous utilisez l’interface Windows PowerShell pour configurer l’appareil. Toutefois, le mot de passe peut expirer. Le cas échéant, vous avez la possibilité de le modifier à l’aide du portail Azure Classic. Ensuite, l’appareil ayant été configuré dans le Gestionnaire d’instantanés StorSimple avant l’expiration du mot de passe, vous devez de nouveau authentifier l’appareil dans le Gestionnaire d’instantanés StorSimple.

#### Pour modifier le mot de passe expiré

1. Dans le portail Azure Classic, démarrez le service StorSimple Manager.

2. Cliquez sur **Appareils** > **Configurer** pour l’appareil.

3. Faites défiler jusqu’à la section du Gestionnaire d’instantanés StorSimple. Entrez un mot de passe comportant entre 14 et 15 caractères. Assurez-vous que le mot de passe contient un mélange de majuscules, de minuscules, de chiffres et de caractères spéciaux.

4. Entrez de nouveau le mot de passe pour le confirmer.

5. Cliquez sur **Enregistrer** au bas de la page.

#### Pour authentifier de nouveau l’appareil

1. Démarrez le Gestionnaire d’instantanés StorSimple.

2. Dans le volet **Étendue**, cliquez sur **Appareils**. Une liste des appareils configurés apparaît dans le volet **Résultats**.

3. Sélectionnez l’appareil, cliquez avec le bouton droit, puis cliquez sur **Authentifier**.

4. Dans la fenêtre **Authentifier** saisissez le nouveau mot de passe.

5. Sélectionnez l’appareil, cliquez avec le bouton droit, puis sélectionnez **Actualiser l’appareil**. L’appareil est synchronisé avec le Gestionnaire d’instantanés StorSimple.

## Remplacement d’un appareil défaillant

Si un appareil StorSimple défaillant est remplacé par un appareil de secours (basculement), procédez comme suit pour connecter le nouvel appareil et afficher les sauvegardes associées.

#### Pour se connecter à un nouvel appareil après un basculement

1. Reconfigurez la connexion iSCSI au nouvel appareil. Pour obtenir les instructions, accédez à « Étape 7 : Montage, initialisation et formatage d’un volume » de la page [Déploiement de votre appareil StorSimple local](storsimple-deployment-walkthrough.md). 

>[AZURE.NOTE]Si le nouvel appareil StorSimple possède la même adresse que l’ancien, il est possible que vous puissiez connecter l’ancienne configuration.

2. Arrêtez le service de gestion Microsoft StorSimple :

    1. Démarrez le Gestionnaire de serveur.

    2. Sur le tableau de bord du Gestionnaire de serveur, dans le menu **Outils**, sélectionnez **Services**.

    3. Dans la fenêtre **Services**, sélectionnez le **Service de gestion Microsoft StorSimple**.

    4. Dans le volet droit, sous **Service de gestion Microsoft StorSimple**, cliquez sur **Arrêter le service**.

3. Supprimez les informations de configuration relatives à l’ancien appareil :

    1. Dans l’Explorateur de fichiers, accédez à C:\\ProgramData\\Microsoft\\StorSimple\\BACatalog. 

    2. Supprimez les fichiers du dossier BACatalog.

4. Redémarrez le service de gestion Microsoft StorSimple :

    1. Sur le tableau de bord du Gestionnaire de serveur, dans le menu **Outils**, sélectionnez **Services**. 

    2. Dans la fenêtre **Services**, sélectionnez le **Service de gestion Microsoft StorSimple**.

    3. Dans le volet droit, sous **Service de gestion Microsoft StorSimple**, cliquez sur **Redémarrer le service**.

5. Démarrez le Gestionnaire d’instantanés StorSimple.

6. Pour configurer le nouvel appareil StorSimple, exécutez la procédure décrite dans Étape 2 : Connexion d’un appareil StorSimple de la page [Déployer le Gestionnaire d’instantanés StorSimple](storsimple-snapshot-manager-deployment.md).

7. Cliquez avec le bouton droit sur le nœud de niveau supérieur du volet **Étendue** (Gestionnaire d’instantanés StorSimple dans l’exemple), puis cliquez sur **Basculer l’affichage des importations**.

8. Un message s’affiche lorsque les groupes de volumes et les sauvegardes importés sont visibles dans le Gestionnaire d’instantanés StorSimple. Cliquez sur **OK**.

## Étapes suivantes

- Découvrez comment [utiliser le Gestionnaire d’instantanés StorSimple pour gérer votre solution StorSimple](storsimple-snapshot-manager-admin.md).
- Découvrez comment [utiliser le Gestionnaire d’instantanés StorSimple pour afficher et gérer les volumes](storsimple-snapshot-manager-manage-volumes.md).

<!---HONumber=AcomDC_0121_2016-->