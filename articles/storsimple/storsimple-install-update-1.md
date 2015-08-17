<properties 
   pageTitle="Installer Update 1 sur votre appareil StorSimple"
   description="Explique comment installer la solution StorSimple série 8000 Update 1 sur votre appareil."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/18/2015"
   ms.author="alkohli" />

# Installer Update 1 sur votre appareil StorSimple

## Vue d'ensemble

Ce didacticiel explique comment installer Update 1 sur un appareil StorSimple exécutant une version logicielle antérieure à Update 1. Votre appareil peut exécuter les versions logicielles de disponibilité générale Update 0.1, Update 0.2 ou Update 0.3.

Pendant cette installation, si votre appareil exécute une version antérieure à Update 1, des vérifications sont effectuées sur votre appareil. Ces vérifications déterminent l’intégrité de l’appareil, en ce qui concerne l’état du matériel et la connectivité réseau.

Vous serez invité à effectuer une vérification préalable manuelle afin de vous assurer des points suivants :

- Les adresses IP fixes du contrôleur sont routables et peuvent se connecter à l’Internet. Elles sont utilisées pour l’exécution des mises à jour sur votre appareil StorSimple. Pour tester le système, exécutez l’applet de commande suivant sur chaque contrôleur :

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
	    
	    


- Avant de mettre à jour l’appareil, nous vous recommandons de prendre un instantané cloud des données de l’appareil.

Une fois que vous avez vérifié et pris en compte les vérifications manuelles (voir ci-dessus), un ensemble de vérifications préalables à la mise à jour sont effectuées. Il s’agit des actions suivantes :

- **Contrôles d’intégrité des contrôleurs**, destinés à vérifier que les contrôleurs d’appareil sont en bon état et en ligne.

- **Contrôles d’intégrité des composants matériels**, utilisés pour vérifier l’état des composants matériels de votre appareil StorSimple.

- **Contrôles DATA 0**, afin de s’assurer de l’activation de DATA 0 sur votre appareil. Si cette interface n’est pas activée, activez-la, puis réessayez.

- **Contrôles DATA 2 et DATA 3** pour vérifier que les interfaces réseau DATA 2 et DATA 3 ne sont pas activées. Si ces interfaces sont activées, désactivez-les, puis essayez de mettre à jour votre appareil. Cette vérification est effectuée uniquement si vous mettez à jour un appareil exécutant un logiciel de disponibilité générale. Les appareils exécutant les versions 0.1, 0.2 ou 0.3 ne sont pas soumis à cette vérification.

- **Contrôle de passerelle** sur tout appareil exécutant une version antérieure à Update 1. Cette vérification est effectuée uniquement sur les appareils présentant une passerelle configurée pour une interface réseau différente de DATA 0.
 
La solution Update 1 est installée uniquement si l’ensemble des vérifications préalables à la mise à jour sont correctement effectuées. Toute mise à jour ultérieure à l’installation d’Update 1 sur votre appareil StorSimple n’est pas soumise aux contrôles DATA 2, DATA 3 et de passerelle. Les autres vérifications préalables seront exécutées.

## Utiliser le portail de gestion pour installer Update 1

Nous vous recommandons d’utiliser le portail de gestion Microsoft Azure pour mettre à jour un appareil exécutant la version de disponibilité générale. Suivez la procédure suivante pour mettre à jour votre appareil.

[AZURE.INCLUDE [storsimple-install-update-via-portal](../../includes/storsimple-install-update-via-portal.md)]


## Résolution des échecs de mise à jour

**Que faire en cas d’affichage d’une notification d’échec des vérifications préalables à la mise à niveau ?**

Si une vérification préalable est mise en échec, consultez de nouveau la barre des notifications détaillées de la partie inférieure de la page. Elle fournit des indications relatives aux vérifications préalables mises en échec. L’illustration suivante représente un exemple d’apparition de ce type de notification. Dans ce cas, les contrôles d’intégrité du contrôleur et des composants ont échoué. Dans la section **État du matériel**, vous constatez que les composants des contrôleurs 0 et 1 méritent votre attention.
 
  ![Échec de la vérification préalable](./media/storsimple-install-update-1/HCS_PreUpdateCheckFailed-include.png)

Vous devez vous assurer que les deux contrôleurs sont intègres et en ligne. Il est également nécessaire de vérifier que l’intégrité des composants matériels de l’appareil StorSimple est vérifiée sur la page Maintenance. Vous pouvez ensuite essayer d’installer les mises à jour. Si vous n’êtes pas en mesure de corriger les problèmes de composants matériels, sollicitez le Support Microsoft, qui vous indiquera la procédure à suivre.

**Que faire si vous recevez un message d’erreur « Impossible d’installer les mises à jour » et que l’on vous recommande de consulter le guide de résolution des mises à jour pour déterminer l’origine du problème ?**

Cela peut être dû au fait que vous ne disposez d’aucune connectivité aux serveurs Microsoft Update. Il s’agit d’une vérification manuelle obligatoire. Si vous perdez la connectivité au serveur de mise à jour, votre tâche de mise à jour est mise en échec. Pour vérifier la connectivité, exécutez l’applet de commande suivante à partir de l’interface Windows PowerShell de votre appareil StorSimple :

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Exécutez l’applet de commande sur les deux contrôleurs.
 
Si vous avez vérifié l’existence de la connectivité et que le problème persiste, sollicitez le Support Microsoft pour connaître la procédure à suivre.

## Étapes suivantes

En savoir plus sur [Microsoft Azure StorSimple](storsimple-overview.md)

<!---HONumber=August15_HO6-->