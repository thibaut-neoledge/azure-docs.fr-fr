<properties 
   pageTitle="Installation d’Update 1.2 sur votre appareil StorSimple | Microsoft Azure"
   description="Explique comment installer StorSimple série 8000 Update 1.2 sur votre appareil StorSimple série 8000."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/01/2015"
   ms.author="alkohli" />

# Installation d’Update 1.2 sur votre appareil StorSimple

## Vue d'ensemble

Ce didacticiel explique comment installer Update 1.2 sur un appareil StorSimple exécutant une version logicielle antérieure à Update 1. Ce didacticiel couvre également les étapes supplémentaires requises pour la mise à jour lorsqu’une passerelle est configurée sur une interface réseau différente de DATA 0 de l’appareil StorSimple.

Update 1.2 comprend des mises à jour logicielles de l’appareil, des mises à jour du pilote LSI et des mises à jour du microprogramme de disque. Les mises à jour logicielles et du pilote LSI sont des mises à jour non perturbatrices et peuvent être appliquées par le biais du portail Azure Classic. Les mises à jour du microprogramme de disque sont des mises à jour perturbatrices et peuvent uniquement être appliquées par le biais de l’interface Windows PowerShell de l’appareil.

Selon la version en cours d’exécution sur votre appareil, vous pouvez déterminer si Update 1.2 sera appliqué. Vous pouvez vérifier la version logicielle de votre appareil en accédant à la section **Aperçu rapide** du **tableau de bord** de celui-ci.

</br>

| Si vous exécutez la version logicielle… | Que se passe-t-il dans le portail ? |
|---------------------------------|--------------------------------------------------------------|
| Version commerciale - GA | Si vous exécutez la version commerciale (GA), n’appliquez pas cette mise à jour. Veuillez [contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour mettre à jour votre appareil.|
| Update 0.1 | Le portail applique Update 1.2. |
| Update 0.2 | Le portail applique Update 1.2. |
| Update 0.3 | Le portail applique Update 1.2. |
| Update 1 | Cette mise à jour ne sera pas disponible. |
| Update 1.1 | Cette mise à jour ne sera pas disponible. |

</br>

> [AZURE.IMPORTANT]
 
> -  Vous ne voyez pas immédiatement Update 1.2, car nous effectuons un déploiement échelonné des mises à jour. Revérifiez les mises à jour dans quelques jours, car celle-ci sera bientôt disponible.
> - Cette mise à jour comprend un ensemble de vérifications préalables manuelles et automatiques pour déterminer l’intégrité de l’appareil en termes d’état matériel et de connectivité réseau. Ces vérifications préalables sont effectuées uniquement si vous appliquez les mises à jour à partir du portail Azure Classic. 
> - Nous vous recommandons d’installer les mises à jour du pilote et du logiciel au moyen du portail Azure Classic. Vous devez uniquement accéder à l’interface Windows PowerShell de l’appareil (pour installer les mises à jour) en cas d’échec de la vérification de la passerelle avant la mise à jour dans le portail. L’installation des mises à jour peut prendre 5 à 10 heures (y compris les mises à jour Windows). Les mises à jour du mode de maintenance doivent être installées via l’interface Windows PowerShell de l’appareil. Les mises à jour du mode de maintenance étant des mises à jour perturbatrices, elles entraînent un temps d’arrêt pour votre appareil.

## Préparation des mises à jour
Vous devez effectuer les étapes suivantes avant d’analyser et d’appliquer la mise à jour :


1. Prenez un instantané cloud des données de l’appareil.

2. Assurez-vous que les adresses IP fixes du contrôleur sont routables et peuvent se connecter à Internet. Ces adresses IP fixes seront utilisées pour mettre en service les mises à jour sur votre appareil. Vous pouvez tester cette fonctionnalité en exécutant l’applet de commande suivante sur chaque contrôleur à partir de l’interface Windows PowerShell de l’appareil :

 	`Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network> `
 
	**Résultat de l’exemple pour Test-Connection lorsque des adresses IP fixes peuvent se connecter à Internet**

	    
		Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com
	    
	    Source	  Destination 	IPV4Address      IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	
		Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

	    Source	  Destination 	  IPV4Address    IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200

Une fois que vous avez terminé ces vérifications préalables manuelles, vous pouvez passer à l’analyse et à l’installation des mises à jour.

## Installation d’Update 1.2 via le portail Azure Classic 

Utilisez cette procédure uniquement si vous disposez d’une passerelle configurée sur l’interface réseau DATA 0 de votre appareil. Suivez la procédure suivante pour mettre à jour votre appareil.

[AZURE.INCLUDE [storsimple-install-update-via-portal](../../includes/storsimple-install-update-via-portal.md)]

## Installation d’Update 1.2 sur un appareil présentant une passerelle configurée pour une interface réseau différente de DATA 0. 

Vous devez utiliser cette procédure uniquement si la vérification de la passerelle échoue lors de la tentative d’installation des mises à jour par le biais du portail Azure Classic. La vérification échoue car vous avez une passerelle affectée à une interface réseau différente de DATA 0 et votre appareil exécute une version logicielle antérieure à Update 1. Si votre appareil ne possède pas de passerelle sur une interface réseau différente de DATA 0, vous pouvez le mettre à jour directement à partir du portail Azure Classic. Consultez la section [Installation d’Update 1.2 via le portail Azure Classic](#install-update-12-via-the-azure-portal).

Les versions logicielles qui peuvent être mises à niveau à l’aide de cette méthode sont : Update 0.1, Update 0.2 et Update 0.3.


> [AZURE.IMPORTANT]
> 
> - Si votre appareil exécute la version commerciale (GA), contactez le [support technique Microsoft](storsimple-contact-microsoft-support.md) pour obtenir de l’aide avec cette mise à jour.
> - Cette procédure ne doit être effectuée qu’une seule fois pour appliquer Update 1.2. Pour appliquer les mises à jour ultérieures, vous pouvez utiliser le portail Azure Classic.

Si votre appareil exécute un logiciel antérieur à Update 1 et qu’il possède une passerelle définie pour une interface réseau différente de DATA 0, vous pouvez appliquer Update 1.2 des deux manières suivantes :

- **Option 1** : téléchargez la mise à jour et appliquez-la à l’aide de l’applet de commande `Start-HcsHotfix` à partir de l’interface Windows PowerShell de l’appareil. Il s’agit de la méthode recommandée. **N’utilisez pas cette méthode pour appliquer Update 1.2 si votre appareil exécute Update 1.0 ou Update 1.1.** 

- **Option 2** : supprimez la configuration de la passerelle et installez la mise à jour directement à partir du portail Azure Classic.


Des instructions détaillées relatives à chacune des procédures sont fournies dans les sections suivantes.

## Option 1 : utiliser Windows PowerShell pour StorSimple pour appliquer Update 1.2 en tant que correctif logiciel

Vous devez utiliser cette procédure uniquement si vous exécutez Update 0.1, 0.2 ou 0.3 et si la vérification de votre passerelle a échoué lors de la tentative d’installation des mises à jour à partir du portail Azure Classic. Si vous exécutez la version commerciale (GA) du logiciel, contactez le [support technique Microsoft](storsimple-contact-microsoft-support.md) pour mettre à jour votre appareil.

Avant d'exécuter cette procédure pour appliquer la mise à jour, vérifiez les points suivants :

- Les deux contrôleurs d’appareil sont en ligne.

Exécutez la procédure suivante pour appliquer Update 1.2. **Les mises à jour peuvent prendre environ 2 heures (environ 30 minutes pour le logiciel, 30 minutes pour le pilote, 45 minutes pour le microprogramme de disque).**

[AZURE.INCLUDE [storsimple-install-update-option1](../../includes/storsimple-install-update-option1.md)]


## Option 2 : utiliser le portail Azure Classic pour appliquer Update 1.2 après la suppression de la configuration de la passerelle

Cette procédure s’applique uniquement aux appareils StorSimple exécutant une version logicielle antérieure à Update 1, avec une passerelle définie sur une interface réseau différente de DATA 0. Vous devez effacer le paramètre de passerelle avant d’appliquer la mise à jour.
 
La mise à jour peut prendre quelques heures. Si vos hôtes se trouvent dans des sous-réseaux différents, le retrait de la configuration de passerelle sur les interfaces iSCSI pourrait entraîner un temps d’arrêt. Pour réduire le temps d’arrêt, nous vous recommandons de configurer DATA 0 pour le trafic iSCSI.
 
Effectuez les étapes suivantes pour désactiver l’interface réseau avec la passerelle, puis appliquez la mise à jour.
 
[AZURE.INCLUDE [storsimple-install-update-option2](../../includes/storsimple-install-update-option2.md)]

## Résolution des échecs de mise à jour

**Que faire en cas d’affichage d’une notification d’échec des vérifications préalables à la mise à niveau ?**

Si une vérification préalable est mise en échec, consultez de nouveau la barre des notifications détaillées de la partie inférieure de la page. Elle fournit des indications relatives aux vérifications préalables mises en échec. L’illustration suivante représente un exemple d’apparition de ce type de notification. Dans ce cas, les contrôles d’intégrité du contrôleur et des composants ont échoué. Dans la section **État du matériel**, vous constatez que les composants **Contrôleur 0** et **Contrôleur 1** requièrent votre attention.
 
  ![Échec de la vérification préalable](./media/storsimple-install-update-1/HCS_PreUpdateCheckFailed-include.png)

Vous devez vous assurer que les deux contrôleurs sont intègres et en ligne. Il est également nécessaire de vérifier que l’intégrité des composants matériels de l’appareil StorSimple est vérifiée sur la page Maintenance. Vous pouvez ensuite essayer d’installer les mises à jour. Si vous n’êtes pas en mesure de corriger les problèmes de composants matériels, sollicitez le Support Microsoft, qui vous indiquera la procédure à suivre.

**Que faire si vous recevez un message d’erreur « Impossible d’installer les mises à jour » et que l’on vous recommande de consulter le guide de résolution des mises à jour pour déterminer l’origine du problème ?**

Cela peut être dû au fait que vous ne disposez d’aucune connectivité aux serveurs Microsoft Update. Il s’agit d’une vérification manuelle obligatoire. Si vous perdez la connectivité au serveur de mise à jour, votre tâche de mise à jour est mise en échec. Pour vérifier la connectivité, exécutez l’applet de commande suivante à partir de l’interface Windows PowerShell de votre appareil StorSimple :

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Exécutez l’applet de commande sur les deux contrôleurs.
 
Si vous avez vérifié l’existence de la connectivité et que le problème persiste, sollicitez le Support Microsoft pour connaître la procédure à suivre.


## Étapes suivantes

En savoir plus sur la [version Update 1.2](storsimple-update1-release-notes.md).

<!---HONumber=AcomDC_1203_2015-->