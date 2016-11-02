<properties 
   pageTitle="Activation ou désactivation de votre appareil StorSimple | Microsoft Azure"
   description="Explique comment activer un nouvel appareil StorSimple, comment activer un appareil qui a été arrêté ou qui a subi une panne de courant, et comment désactiver un appareil en cours d'exécution."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/23/2016"
   ms.author="alkohli" />

# Activation ou désactivation de votre appareil StorSimple 

## Vue d'ensemble

L'arrêt d'un appareil Microsoft Azure StorSimple n'est pas requis dans le cadre du fonctionnement normal du système. Toutefois, vous devrez peut-être activer un nouvel appareil ou un appareil qui a dû être arrêté. En règle générale, un arrêt est nécessaire dans les cas où vous devez remplacer du matériel défectueux, physiquement déplacer une unité ou mettre un appareil hors service. Ce didacticiel décrit la procédure requise pour la mise sous tension et l'arrêt de votre appareil StorSimple dans différents scénarios.

Le tableau suivant répertorie les différents scénarios de mise sous tension et d'arrêt de votre appareil StorSimple et fournit des liens vers les procédures appropriées.

|Scénario|Rubriques de référence|
|:-------|:---------------|
|Activer un nouvel appareil|[Activer un nouvel appareil](#turn-on-a-new-device)<ul><li>[Nouvel appareil avec boîtier principal uniquement](#new-device-with-primary-enclosure-only)</li><li>[Nouvel appareil avec boîtier EBOD](#new-device-with-ebod-enclosure)</li></ul>|
|Activer un appareil après l'arrêt|[Activer un appareil après l'arrêt](#turn-on-a-device-after-shutdown)<ul><li>[Appareil avec boîtier principal uniquement](#device-with-primary-enclosure-only)</li><li>[Appareil avec boîtier EBOD](#device-with-ebod-enclosure)</li></ul>|
|Activer un appareil après une panne de courant|[Activer un appareil après une panne de courant](#turn-on-a-device-after-a-power-loss)<ul><li>[Appareil avec boîtier principal uniquement](#8100)</li><li>[Appareil avec boîtier EBOD](#8600)</li></ul>|
|Activer un appareil après la perte de connexion au boîtier principal et à EBOD|[Activer un appareil après la perte de connexion au boîtier principal et EBOD](#turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost)|
|Arrêter un appareil en cours d'exécution|[Arrêter un appareil en cours d'exécution](#turn-off-a-running-device)<ul><li>[Appareil avec boîtier principal uniquement](#8100a)</li><li>[Appareil avec boîtier EBOD](#8600a)</li></ul>|

## Activer un nouvel appareil

Les étapes d’activation initiale d’un appareil StorSimple diffèrent selon le modèle de l’appareil (8100 ou 8600). Le modèle 8100 a un seul boîtier principal, tandis que le modèle 8600 est un appareil à deux boîtiers : un boîtier principal et un boîtier EBOD. Les étapes détaillées pour les deux modèles sont traitées dans les sections suivantes.

- [Nouvel appareil avec boîtier principal uniquement](#new-device-with-primary-enclosure-only)

- [Nouvel appareil avec boîtier EBOD](#new-device-with-ebod-enclosure)

### Nouvel appareil avec boîtier principal uniquement

Le StorSimple 8100 est un appareil à un seul boîtier. Votre appareil est doté de modules d’alimentation et de refroidissement (PCM) en double. Chaque PCM doit être installé et connecté à des sources d’alimentation différentes pour garantir une haute disponibilité.

Procédez comme suit pour brancher les câbles d’alimentation de votre appareil.

[AZURE.INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

>[AZURE.NOTE]Pour les instructions complètes relatives au câblage et à la configuration de l'appareil, consultez [Installation de votre appareil StorSimple 8100](storsimple-8100-hardware-installation.md). Assurez-vous que vous suivez les instructions avec précision.

### Nouvel appareil avec boîtier EBOD

Le StorSimple 8600 possède un boîtier principal et un boîtier EBOD. Les unités doivent donc être reliées entre elles pour l'alimentation et la connectivité SAS (Serial Attached SCSI).

Lorsque vous configurez cet appareil pour la première fois, commencez par les étapes du câblage SAS, puis effectuez les étapes pour le câblage d'alimentation.

[AZURE.INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

>[AZURE.NOTE]Pour les instructions complètes relatives au câblage et à la configuration de l'appareil, consultez [Installation de votre appareil StorSimple 8600](storsimple-8600-hardware-installation.md). Assurez-vous que vous suivez les instructions avec précision.

## Activer un appareil après l'arrêt

Les étapes d’activation d’un appareil StorSimple après qu’il a été arrêté diffèrent selon le modèle de l’appareil (8100 ou 8600). Le modèle 8100 a un seul boîtier principal, tandis que le modèle 8600 est un appareil à deux boîtiers : un boîtier principal et un boîtier EBOD.

- [Appareil avec boîtier principal uniquement](#device-with-primary-enclosure-only)

- [Appareil avec boîtier EBOD](#device-with-ebod-enclosure)

### Appareil avec boîtier principal uniquement

Après un arrêt, utilisez la procédure suivante pour activer un appareil StorSimple avec un boîtier principal et aucun boîtier EBOD.

#### Pour activer un appareil avec un boîtier principal uniquement

1. Assurez-vous que les commutateurs d'alimentation sont en position d'arrêt sur les PCM (Modules d'alimentation et de refroidissement). Si les commutateurs ne sont pas en position d'arrêt, placez-les en position d'arrêt et attendez que les témoins lumineux s'éteignent.

2. Activez l'appareil en mettant en marche les commutateurs d'alimentation des deux PCM. L'appareil doit s'allumer.

3. Vérifiez les éléments suivants pour vérifier que l'appareil est entièrement activé :

    1. Les témoins lumineux OK des deux modules PCM sont verts.

    2. Les témoins lumineux d'état sur les deux contrôleurs sont vert fixe.

    3. Le témoin lumineux bleu sur l'un des contrôleurs clignote, ce qui indique que le contrôleur est actif.

    Si l'une de ces conditions n'est pas respectée, votre appareil n'est pas intègre. Veuillez [contacter le support Microsoft](storsimple-contact-microsoft-support.md).

### Appareil avec boîtier EBOD

Après un arrêt, utilisez la procédure suivante pour activer un appareil StorSimple avec un boîtier principal et un boîtier EBOD. Exécutez chaque étape dans l'ordre, exactement comme indiqué. Dans le cas contraire, vous pourriez causer une perte de données.

#### Pour activer un appareil avec un boîtier principal et un boîtier EBOD

1. Assurez-vous que le boîtier EBOD est connecté au boîtier principal. Pour plus d'informations, consultez [Installation de votre appareil StorSimple 8600](storsimple-8600-hardware-installation.md).

2. Assurez-vous que les PCM (Modules d'alimentation et de refroidissement) sont en position d'arrêt sur le boîtier EBOD et le boîtier principal. Si les commutateurs ne sont pas en position d'arrêt, placez-les en position d'arrêt et attendez que les témoins lumineux s'éteignent.

3. Activez le boîtier EBOD en premier en mettant en marche les commutateurs d'alimentation des deux PCM. Les témoins lumineux des PCM doivent être verts. Un témoin lumineux de contrôleur EBOD vert sur cette unité indique que le boîtier EBOD est activé.

4. Activez le boîtier principal en mettant en marche les commutateurs d'alimentation des deux PCM. L'ensemble du système doit désormais être activé.

5. Vérifiez que les témoins lumineux SAS sont verts, ce qui garantit que la connexion entre le boîtier EBOD et le boîtier principal est bonne.

## Activer un appareil après une panne de courant

Une panne de courant ou une interruption peut entraîner l’arrêt d’un appareil StorSimple. La panne peut se produire sur l'un des blocs d'alimentation ou les deux. Les étapes de récupération sont différentes selon qu'il s'agit d'un appareil de modèle 8100 ou 8600. Le modèle 8100 a un seul boîtier principal, tandis que le modèle 8600 est un appareil à deux boîtiers : un boîtier principal et un boîtier EBOD. Cette section décrit la procédure de récupération pour chaque scénario.

- [Appareil avec boîtier principal uniquement](#8100)

- [Appareil avec boîtier EBOD](#8600)

### Appareil avec boîtier principal uniquement <a name="8100">

Le système peut poursuivre son fonctionnement normal même en cas de panne de l'une de ses alimentations. Toutefois, pour garantir la haute disponibilité de l'appareil, restaurez l'alimentation dès que possible.

En cas de panne de courant ou d'interruption sur les deux alimentations, le système s'arrête de façon ordonnée et contrôlée. Lorsque l'alimentation est restaurée, le système redémarre automatiquement.

### Appareil avec boîtier EBOD <a name="8600">

#### Panne de courant sur une alimentation

Le système peut poursuivre son fonctionnement normal même en cas de panne de l'une de ses alimentations sur le boîtier principal ou le boîtier EBOD. Toutefois, pour garantir la haute disponibilité de l'appareil, restaurez l'alimentation dès que possible.

#### Panne de courant sur les deux alimentations du boîtier principal et du boîtier EBOD

En cas de panne de courant ou d'interruption sur les deux alimentations, le boîtier EBOD s'arrête immédiatement et le boîtier principal s'arrête de façon ordonnée et contrôlée. Lorsque l'alimentation est restaurée, l'appareil redémarre automatiquement.

En cas de mise hors tension manuelle, procédez comme suit pour restaurer l'alimentation du système.

1. Activez le boîtier EBOD.

2. Une fois le boîtier EBOD activé, activez le boîtier principal.

### Panne de courant sur les deux alimentations du boîtier EBOD

Lors du câblage, vous devez vous assurer que le boîtier EBOD n'est jamais connecté seul à une unité de distribution de l'alimentation (PDU) distincte. Si le boîtier EBOD et le boîtier principal échouent en même temps, le système effectuera une récupération.

Si seul le boîtier EBOD échoue sur les deux alimentations, le système n'effectue pas de récupération automatique. Procédez comme suit pour activer le système et le restaurer :

1. Si le boîtier principal est activé, désactivez les deux modules PCM.

2. Attendez quelques minutes que le système s'arrête.

3. Activez le boîtier EBOD.

4. Une fois le boîtier EBOD activé, activez le boîtier principal.

## Activer un appareil après la perte de connexion au boîtier principal et EBOD

Si la connexion est perdue entre le contrôleur de secours et le contrôleur EBOD correspondant, l'appareil continue à fonctionner. Si la connexion entre le contrôleur actif du système et le contrôleur EBOD correspondant est perdue, un basculement doit se produire et l'appareil doit continuer à fonctionner normalement.

Lorsque les deux câbles SCSI (SAS) sont retirés ou que la connexion entre le boîtier EBOD et le boîtier principal est rompue, l'appareil ne fonctionne plus. Dans ce cas, procédez comme suit.

### Pour activer l'appareil après la perte de connexion

1. Accédez à l'arrière de l'appareil.

2. Si la connexion par câble SAS entre le boîtier EBOD et le boîtier principal est interrompue, tous les témoins lumineux relatifs à SAS sur le boîtier EBOD seront éteints.

3. Arrêtez les deux modules PCM sur le boîtier EBOD et le boîtier principal.

4. Attendez que tous les témoins lumineux à l'arrière des deux boîtiers s'éteignent.

5. Réinsérez les câbles SAS et vérifiez qu'il existe une bonne connexion entre le boîtier EBOD et le boîtier principal.

6. Activez le boîtier EBOD en premier en mettant en marche les commutateurs PCM des deux PCM.

7. Assurez-vous que le boîtier EBOD est activé en vérifiant que le témoin lumineux vert est allumé.

8. Mettez sous tension le boîtier principal.

9. Assurez-vous que le boîtier principal est activé en vérifiant que le témoin lumineux vert du contrôleur est allumé.

10. Assurez-vous que la connexion du boîtier EBOD avec le boîtier principal est bonne en vérifiant que les témoins lumineux SAS (quatre par contrôleur EBOD) sont tous allumés.

>[AZURE.IMPORTANT] Si les câbles SAS sont défectueux ou si la connexion entre le boîtier EBOD et le boîtier principal n'est pas bonne, lorsque vous activez le système, celui-ci passera en mode de récupération. Dans ce cas, veuillez [contacter le support Microsoft](storsimple-contact-microsoft-support.md).

## Désactiver un appareil en cours d'exécution

Il peut être nécessaire d’arrêter un appareil StorSimple en cours d’exécution s’il est déplacé, s’il est mis hors service ou s’il contient un composant défectueux qui doit être remplacé. Les étapes diffèrent selon le modèle de l’appareil StorSimple (8100 ou 8600). Le modèle 8100 a un seul boîtier principal, tandis que le modèle 8600 est un appareil à deux boîtiers : un boîtier principal et un boîtier EBOD. Cette section décrit les étapes pour arrêter un appareil en cours d'exécution.

- [Appareil avec boîtier principal](#8100a)

- [Appareil avec boîtier EBOD](#8600a)

### Appareil avec boîtier principal <a name="8100a"> 

Pour arrêter l’appareil de façon correcte et contrôlée, vous pouvez utiliser le portail Azure Classic ou Windows PowerShell pour StorSimple.

>[AZURE.IMPORTANT] N'arrêtez pas un appareil en cours d'exécution à l'aide du bouton d'alimentation situé à l'arrière de l'appareil.
>
>Avant d'arrêter l'appareil, assurez-vous que tous ses composants sont intègres. Dans le portail Azure Classic accédez à **Périphériques** > **Maintenance** > **État du matériel** et assurez-vous que l’état de tous les composants est vert. Cela est vrai uniquement pour un système sain. Si le système est sur le point d’être arrêté pour procéder au remplacement d'un composant défaillant, vous verrez s'afficher un état Échec (rouge) ou un état Détérioré (jaune) pour les composants respectifs sous **État du matériel**.

Une fois que vous accédez à Windows PowerShell pour StorSimple ou au portail Azure Classic, suivez les étapes de la rubrique [Arrêter un appareil StorSimple](storsimple-manage-device-controller.md#shut-down-a-storsimple-device).

### Appareil avec boîtier EBOD <a name="8600a">

>[AZURE.IMPORTANT] Avant d'arrêter le boîtier principal et le boîtier EBOD, assurez-vous que tous les composants de l'appareil sont intègres. Dans le portail de gestion, accédez à **Périphériques** > **Maintenance** > **État du matériel** et assurez-vous que tous les composants sont intègres.

#### Pour arrêter un appareil en cours d'exécution avec un boîtier EBOD

1. Suivez les étapes répertoriées dans [Arrêter un appareil StorSimple](storsimple-manage-device-controller.md#shut-down-a-storsimple-device) pour le boîtier principal.

2. Lorsque le boîtier principal est arrêté, arrêtez le boîtier EBOD en plaçant les commutateurs des deux modules PCM en position d'arrêt.

3. Pour être sûr que le boîtier EBOD s'est arrêté, vérifiez que tous les témoins lumineux à l'arrière du boîtier EBOD sont éteints.

>[AZURE.NOTE] Les câbles SAS qui sont utilisés pour connecter le boîtier EBOD au boîtier principal ne doivent pas être retirés tant que le système n'a pas été arrêté.

## Étapes suivantes

[Contactez le support Microsoft](storsimple-contact-microsoft-support.md) si vous rencontrez des problèmes lors de l'activation ou l'arrêt d'un appareil StorSimple.

<!---HONumber=AcomDC_0831_2016-->