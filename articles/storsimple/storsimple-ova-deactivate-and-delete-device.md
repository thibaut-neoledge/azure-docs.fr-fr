<properties 
   pageTitle="Désactiver et supprimer un StorSimple Virtual Array | Microsoft Azure"
   description="Explique comment supprimer un appareil StorSimple du service en le désactivant dans un premier temps, puis en le supprimant."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/08/2016"
   ms.author="alkohli" />

# Désactiver et supprimer un StorSimple Virtual Array

## Vue d’ensemble

Quand vous désactivez un StorSimple Virtual Array, vous interrompez la connexion entre l’appareil et le service StorSimple Manager correspondant. La désactivation est une opération DÉFINITIVE et ne peut pas être annulée. Un appareil désactivé ne peut pas être de nouveau enregistré auprès du service StorSimple Manager.

Vous pouvez être amené à désactiver et à supprimer un appareil virtuel StorSimple dans les scénarios suivants :


- Votre appareil est en ligne et vous envisagez de le basculer. Cette opération peut s’avérer nécessaire si vous envisagez d’effectuer une mise à niveau vers un appareil plus grand. Une fois les données de l’appareil transférées et le basculement terminé, vous pouvez supprimer l’appareil.

- Votre appareil est déconnecté et vous envisagez de le basculer. Cette situation peut se produire en cas de sinistre, où une panne dans le centre de données entraîne l’arrêt de votre appareil principal. Vous envisagez de basculer l’appareil vers un appareil secondaire. Une fois les données de l’appareil transférées et le basculement terminé, vous pouvez supprimer l’appareil.

- Vous souhaitez désactiver l’appareil, puis le supprimer.
 

Si vous désactivez un appareil, les données stockées localement ne seront plus accessibles. Seules les données stockées dans le cloud peuvent être récupérées. Si vous envisagez de conserver les données de l’appareil après la désactivation, vous devez préalablement prendre un instantané de cloud de toutes vos données. Cela vous permettra de récupérer toutes les données à un stade ultérieur.


Ce didacticiel explique comment :

- Désactiver un appareil 
- Supprimer un appareil désactivé


## Désactiver un appareil

Suivez la procédure suivante pour désactiver votre appareil.

#### Pour désactiver l’appareil   

1. Accédez à page **Appareils**. Sélectionnez l’appareil à désactiver.

	![Sélection de l’appareil à désactiver](./media/storsimple-ova-deactivate-and-delete-device/deactivate1m.png)

3. En bas de la page, cliquez sur **Désactiver**.

	![Clic sur Désactiver](./media/storsimple-ova-deactivate-and-delete-device/deactivate2m.png)

4. Un message de confirmation s’affiche. Cliquez sur **Oui** pour continuer.

	![Confirmation de la désactivation](./media/storsimple-ova-deactivate-and-delete-device/deactivate3m.png)

	Le processus de désactivation démarre et son exécution dure quelques minutes.

	![Désactivation en cours](./media/storsimple-ova-deactivate-and-delete-device/deactivate4m.png)

3. Après la désactivation, la liste des appareils est actualisée.

	![Désactivation terminée](./media/storsimple-ova-deactivate-and-delete-device/deactivate5m.png)

	Vous pouvez maintenant supprimer cet appareil.

## Supprimer l’appareil

Un appareil doit être désactivé avant d’être supprimé. Si vous supprimez un appareil, il est retiré de la liste des appareils connectés au service. Le service ne peut alors plus gérer l’appareil supprimé. Toutefois, les données associées à l’appareil demeurent dans le cloud. N’oubliez pas qu’elles engendrent des frais par la suite.

Procédez comme suit pour supprimer l’appareil :

#### Pour supprimer l’appareil 

 1. Dans la page **Appareils** du service StorSimple Manager, sélectionnez l’appareil désactivé à supprimer.

	![Sélection de l’appareil à supprimer](./media/storsimple-ova-deactivate-and-delete-device/deactivate5m.png)

 2. En bas de la page, cliquez sur **Supprimer**.
 
	![Clic sur Supprimer](./media/storsimple-ova-deactivate-and-delete-device/deactivate6m.png)

 3. Vous êtes invité à confirmer l’opération. Tapez le nom de l’appareil pour confirmer sa suppression. La suppression de l’appareil ne supprime pas les données de cloud associées à celui-ci. Cliquez sur l’icône en forme de coche pour continuer.
 
	![Confirmation de suppression](./media/storsimple-ova-deactivate-and-delete-device/deactivate7m.png)

 5. La suppression de l’appareil peut nécessiter quelques minutes.

	![Suppression en cours](./media/storsimple-ova-deactivate-and-delete-device/deactivate8m.png)

 	Une fois l’appareil supprimé, la liste des appareils est actualisée.

	![Suppression terminée](./media/storsimple-ova-deactivate-and-delete-device/deactivate9m.png)


## Étapes suivantes

- Pour en savoir plus sur l’utilisation du service StorSimple Manager, consultez [Utiliser le service StorSimple Manager pour gérer votre StorSimple Virtual Array](storsimple-ova-manager-service-administration.md). 

<!---HONumber=AcomDC_0218_2016-->