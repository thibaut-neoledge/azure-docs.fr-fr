<properties 
   pageTitle="Remplacer un lecteur de disque sur un appareil StorSimple | Microsoft Azure"
   description="Explique comment remplacer un lecteur de disque sur un appareil principal ou un boîtier EBOD StorSimple."
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
   ms.date="12/02/2015"
   ms.author="alkohli" />

# Remplacer un lecteur de disque sur votre appareil StorSimple

## Vue d'ensemble

Ce didacticiel explique comment vous pouvez retirer et remplacer un lecteur de disque dur défectueux ou défaillant sur un appareil Microsoft Azure StorSimple. Pour remplacer un lecteur de disque, vous devez :

- Désengager le verrou anti-effraction

- Retirer le lecteur de disque

-     Installez le lecteur de disque de remplacement

>[AZURE.IMPORTANT] Avant de retirer et de remplacer un lecteur de disque, passez en revue les informations de sécurité dans [Remplacement de composants matériels StorSimple](storsimple-hardware-component-replacement.md).

## Désengager le verrou anti-effraction

Cette procédure explique comment les verrous anti-effraction sur votre appareil StorSimple peuvent être engagés ou désengagés quand vous remplacez les lecteurs de disque. Les verrous anti-effraction sont montés dans les poignées du support de lecteur et ils sont accessibles via une petite ouverture dans la partie verrou de la poignée. Les lecteurs sont fournis avec les verrous en position verrouillée.

#### Pour déverrouiller le verrou anti-effraction

1. Insérez soigneusement la clé de verrouillage (un tournevis « anti-effraction » T10 fourni par Microsoft) dans l’ouverture de la poignée et dans son emplacement. 

    >[AZURE.NOTE] Si le verrou anti-effraction est activé, l’indicateur rouge est visible dans l’ouverture.

    ![Lecteur de disque verrouillé](./media/storsimple-disk-drive-replacement/IC741056.png)

    **Figure 1** : Verrou anti-effraction engagé

    |Étiquette|Description|
    |:----|:----------|
    |1|Ouverture de l’indicateur|
    |2|Verrou anti-effraction|

2. Faites tourner la clé dans le sens inverse des aiguilles d’une montre jusqu’à ce que l’indicateur rouge ne soit plus visible dans l’ouverture au-dessus de la clé.

3. Retirez la clé.

    ![Lecteur de disque déverrouillé](./media/storsimple-disk-drive-replacement/IC741057.png)

    **Figure 2** : Lecteur de disque déverrouillé

4. Le lecteur de disque peut maintenant être retiré.

Suivez les étapes en sens inverse pour engager le verrou.

## Retirer le lecteur de disque

Votre appareil StorSimple prend en charge une configuration des espaces de stockage en RAID 10. Ceci implique qu’il peut fonctionner normalement avec un disque défectueux, qui peut être un disque SSD ou un disque dur.

>[AZURE.IMPORTANT]
>
>- Si votre système a plusieurs disques défectueux, ne retirez jamais en même temps plusieurs disques SSD ou disques durs du système. Ceci peut entraîner la perte de données.
>
>- Veillez à placer un disque SSD de remplacement à un emplacement qui contenait auparavant un disque SSD. De même, veillez à placer un disque dur de remplacement à un emplacement qui contenait auparavant un disque dur.
>
>- Dans le portail Azure Classic, les emplacements sont numérotés de 0 à 11. Par conséquent, si le portail indique qu’un disque à l’emplacement 2 est défectueux, sur l’appareil, vous trouvez le disque défectueux au troisième emplacement à partir du coin supérieur gauche.

Les lecteurs peuvent être retirés et remplacés pendant que le système fonctionne.

#### Pour retirer un lecteur

1. Pour identifier le disque défectueux, dans le portail Azure Classic, accédez à **Appareils** > **Maintenance** > **État du matériel**. Comme un disque défectueux peut se trouver dans le boîtier principal et/ou dans un boîtier EBOD (si vous utilisez un modèle 8600), vérifiez l’état des disques sous **Composants partagés** et sous **Composants partagés du boîtier EBOD**. Un disque défectueux dans un des boîtiers est affiché avec un état rouge.

2. Recherchez les lecteurs à l’avant du boîtier principal ou du boîtier EBOD. Pour le disque défectueux, le LED ambre est allumé.

3. Si le disque est déverrouillé, passez à l’étape suivante. Si le disque est verrouillé, déverrouillez-le en suivant la procédure décrite dans [Désengager le verrou anti-effraction](#disengage-the-antitamper-lock).

4. Appuyez sur le verrou noir du module de support de lecteur et tirez sur la poignée du support de lecteur vers l’avant du châssis.

    ![Libération de la poignée du lecteur de disque](./media/storsimple-disk-drive-replacement/IC741051.png)

    **Figure 3** : Libération de la poignée du lecteur

5. Quand la poignée du support de lecteur est entièrement en extension, faites glisser le support de lecteur hors du châssis.

    ![Retrait du disque hors du lecteur de disque](./media/storsimple-disk-drive-replacement/IC741052.png)
    
    **Figure 4** : Retrait du lecteur de disque hors du châssis

##     Installez le lecteur de disque de remplacement

Quand un lecteur est défectueux dans votre appareil Microsoft Azure StorSimple et que vous l’avez retiré, suivez cette procédure pour le remplacer par un nouveau lecteur.

#### Pour insérer un lecteur

1. Assurez-vous que la poignée du support de lecteur est entièrement en extension, comme illustré dans l’image suivante.

    ![Lecteur de disque avec la poignée en extension](./media/storsimple-disk-drive-replacement/IC741044.png)

    **Figure 5** : Lecteur avec la poignée en extension

2. Faites glisser le support de lecteur jusqu’au bout dans le châssis.

    ![Insertion du disque dans le support de lecteur disque](./media/storsimple-disk-drive-replacement/IC741045.png)

    **Figure 6** : Insertion du support de lecteur dans le châssis

3. Le support de lecteur étant inséré, fermez la poignée du support de lecteur tout en continuant à pousser le support de lecteur dans le châssis, jusqu’à ce que la poignée du support de lecteur s’enclenche en position verrouillée.

4. Utilisez la clé de verrouillage qui a été fournie par Microsoft (tournevis Torx anti-effraction) pour sécuriser la poignée du support à son emplacement en tournant la vis de verrouillage d’un quart de tour dans le sens des aiguilles d’une montre.

5. Vérifiez que le remplacement a réussi et que le lecteur est opérationnel en accédant au portail Azure Classic, puis à **Maintenance** > **État du matériel**. Sous **Composants partagés** ou **Composants partagés du boîtier EBOD**, l’état du disque doit être en vert, indiquant qu’il est sain.

    >[AZURE.NOTE] Plusieurs heures peuvent être nécessaires pour que l’état du disque passe en vert après le remplacement.

## Étapes suivantes

En savoir plus sur le [Remplacement des composants matériels StorSimple](storsimple-hardware-component-replacement.md).

<!---HONumber=AcomDC_0224_2016-->