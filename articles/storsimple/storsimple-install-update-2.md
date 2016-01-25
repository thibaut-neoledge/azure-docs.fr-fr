<properties 
   pageTitle="Installer Update 2 sur votre appareil StorSimple | Microsoft Azure"
   description="Explique comment installer StorSimple 8000 Series Update 2 sur votre appareil de la gamme StorSimple 8000."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="01/12/2016"
   ms.author="alkohli" />

# Installer Update 2 sur votre appareil StorSimple

## Vue d’ensemble

Ce didacticiel explique comment installer Update 2 sur un appareil StorSimple exécutant une version logicielle antérieure par le biais du portail Azure Classic. Ce didacticiel couvre également les étapes à effectuer pour la mise à jour quand une passerelle est configurée sur une interface réseau différente de DATA 0 de l’appareil StorSimple et que la mise à jour porte sur une version logicielle antérieure à Update 1.

Update 2 comprend des mises à jour logicielles de l’appareil, des mises à jour du pilote LSI et des mises à jour du microprogramme de disque. Les mises à jour logicielles de l’appareil et du pilote LSI sont des mises à jour non perturbatrices qui peuvent être appliquées par le biais du portail Azure Classic. Les mises à jour du microprogramme de disque sont des mises à jour perturbatrices et peuvent uniquement être appliquées par le biais de l’interface Windows PowerShell de l’appareil.

> [AZURE.IMPORTANT]
 
> -  Il est possible que vous ne voyiez pas immédiatement Update 2, car nous effectuons un déploiement échelonné des mises à jour. Revérifiez les mises à jour dans quelques jours, car celle-ci sera bientôt disponible.
> - Un ensemble de vérifications préalables manuelles et automatiques sont effectuées avant l’installation pour déterminer l’intégrité de l’appareil sur le plan de l’état du matériel et de la connectivité réseau. Ces vérifications préalables sont effectuées uniquement si vous appliquez les mises à jour à partir du portail Azure Classic. 
> - Nous vous recommandons d’installer les mises à jour du pilote et du logiciel au moyen du portail Azure Classic. Vous devez uniquement accéder à l’interface Windows PowerShell de l’appareil (pour installer les mises à jour) en cas d’échec de la vérification de la passerelle avant la mise à jour dans le portail. L’installation des mises à jour peut prendre 4 à 7 heures (mises à jour Windows incluses). Les mises à jour du mode de maintenance doivent être installées via l’interface Windows PowerShell de l’appareil. Les mises à jour du mode de maintenance étant des mises à jour perturbatrices, elles entraînent un temps d’arrêt pour votre appareil.
> - Si vous exécutez l’outil StorSimple Snapshot Manager facultatif, veillez à le mettre à niveau vers la version Update 2 avant de mettre à jour l’appareil.

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

## Installer Update 2 par le biais du portail Azure Classic 

Il s’agit de la procédure recommandée pour mettre à jour votre appareil. Procédez comme suit.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

## Installer Update 2 en tant que correctif logiciel 

Utilisez cette procédure uniquement en cas d’échec de la vérification de la passerelle lors de la tentative d’installation des mises à jour par le biais du portail Azure Classic. La vérification échoue, car vous avez une passerelle affectée à une interface réseau différente de DATA 0 et votre appareil exécute une version logicielle antérieure à Update 1.

Les versions logicielles qui peuvent être mises à niveau à l’aide de la méthode du correctif logiciel sont : Update 0.1, Update 0.2, Update 0.3, Update 1, Update 1.1 et Update 1.2. La méthode du correctif logiciel implique les trois étapes suivantes :

- Télécharger les correctifs logiciels à partir du catalogue Microsoft Update
- Installer et vérifier les correctifs logiciels en mode Normal
- Installer et vérifier les correctifs logiciels en mode Maintenance

Les correctifs logiciels appliqués selon cette méthode sont indiqués ci-dessous :

| Ordre | Ko | Nom | Description du package | Type de mise à jour |
|--------|-----------|-------------------------|-----------------------------|-------------|
| 1 | KB3121901 | Mise à jour logicielle | HcsMdsSfotwareUpdate.exe <br></br> CisMdsAgentUpdateBundle.exe | Normal |
| 2 | KB3121900 | Pilote LSI | HcsLsiUpdate.exe | Normal |
| 3 | KB3080728 | Correctif Storport | Storport-KB3080728-x64.msu | Normal |
| 4 | KB3090322 | Correctif Spaceport | Spaceport-KB3090322-x64.msu | Normal |
| 5 | KB3121899 | Microprogramme de disque | DiskFirmwarePackage.exe | Maintenance |


> [AZURE.IMPORTANT]
> 
> - Si votre appareil exécute la version commerciale (GA), contactez le [support technique Microsoft](storsimple-contact-microsoft-support.md) pour obtenir de l’aide avec cette mise à jour.
> - Cette procédure ne doit être effectuée qu’une seule fois pour appliquer Update 2. Pour appliquer les mises à jour ultérieures, vous pouvez utiliser le portail Azure Classic.
> - Chaque installation d’un correctif logiciel peut prendre environ 20 minutes. Le temps total d’installation avoisine les 2 heures. 
> - Avant d’utiliser cette procédure pour appliquer la mise à jour, vérifiez que les deux contrôleurs d’appareil sont en ligne et que tous les composants matériels sont sains.

Exécutez la procédure suivante pour appliquer Update 2 en tant que correctif logiciel.

[AZURE.INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]


## Résolution des échecs de mise à jour

**Que faire en cas d’affichage d’une notification d’échec des vérifications préalables à la mise à niveau ?**

Si une vérification préalable est mise en échec, consultez de nouveau la barre des notifications détaillées de la partie inférieure de la page. Elle fournit des indications relatives aux vérifications préalables mises en échec. L’illustration suivante représente un exemple d’apparition de ce type de notification. Dans ce cas, les contrôles d’intégrité du contrôleur et des composants ont échoué. Dans la section **État du matériel**, vous constatez que les composants **Contrôleur 0** et **Contrôleur 1** requièrent votre attention.
 
  ![Échec de la vérification préalable](./media/storsimple-install-update-2/HCS_PreUpdateCheckFailed-include.png)

Vous devez vous assurer que les deux contrôleurs sont intègres et en ligne. Il est également nécessaire de vérifier que l’intégrité des composants matériels de l’appareil StorSimple est vérifiée sur la page Maintenance. Vous pouvez ensuite essayer d’installer les mises à jour. Si vous n’êtes pas en mesure de corriger les problèmes de composants matériels, sollicitez le Support Microsoft, qui vous indiquera la procédure à suivre.

**Que faire si vous recevez un message d’erreur « Impossible d’installer les mises à jour » et que l’on vous recommande de consulter le guide de résolution des mises à jour pour déterminer l’origine du problème ?**

Cela peut être dû au fait que vous ne disposez d’aucune connectivité aux serveurs Microsoft Update. Il s’agit d’une vérification manuelle obligatoire. Si vous perdez la connectivité au serveur de mise à jour, votre tâche de mise à jour est mise en échec. Pour vérifier la connectivité, exécutez l’applet de commande suivante à partir de l’interface Windows PowerShell de votre appareil StorSimple :

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Exécutez l’applet de commande sur les deux contrôleurs.
 
Si vous avez vérifié l’existence de la connectivité et que le problème persiste, sollicitez le Support Microsoft pour connaître la procédure à suivre.


## Étapes suivantes

Découvrez la [version Update 2](storsimple-update2-release-notes.md).

<!---HONumber=AcomDC_0114_2016-->