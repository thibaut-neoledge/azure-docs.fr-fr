<properties 
   pageTitle="Modifier les paramètres DATA 0 sur un appareil StorSimple | Microsoft Azure"
   description="Découvrez comment utiliser Windows PowerShell for StorSimple pour reconfigurer l'interface réseau DATA 0 sur votre appareil StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="alkohli" />

# Modification des paramètres de l’interface réseau DATA 0 sur votre périphérique StorSimple

## Vue d’ensemble

Votre appareil Microsoft Azure StorSimple dispose de six interfaces réseau, de DATA 0 à DATA 5. L’interface DATA 0 est toujours configurée via l'interface Windows PowerShell ou la console série, et elle est automatiquement activée pour le cloud. L’interface DATA 0 est d'abord configuré via l’Assistant Installation lors du déploiement initial de l’appareil StorSimple. Si l’appareil est en cours d’exécution, vous devrez peut-être reconfigurer les paramètres DATA 0. Ce didacticiel fournit deux méthodes permettant de modifier les paramètres de réseau DATA 0 via Windows PowerShell for StorSimple.

Après avoir lu ce didacticiel, vous pourrez :

- Modification des paramètres réseau de DATA 0 via l'Assistant Installation
- Modification des paramètres réseau de DATA 0 via l’applet de commande `Set-HcsNetInterface`


## Modification des paramètres réseau de DATA 0 via l’Assistant Installation
Vous pouvez reconfigurer les paramètres réseau de DATA 0 en vous connectant à l'interface Windows PowerShell de votre appareil StorSimple et en ouvrant une session de l'Assistant Installation. Pour modifier les paramètres de DATA 0, procédez comme suit :

#### Pour modifier les paramètres réseau de DATA 0 via l’Assistant Installation

1. Dans le menu de la console série, sélectionnez l’option 1, **Ouvrir une session avec un accès total**. Lorsque vous y êtes invité, fournissez le **mot de passe administrateur de l’appareil**. Le mot de passe par défaut est `Password1`.

2.  À l’invite de commandes, tapez :


	`Invoke-HcsSetupWizard`

3. Un Assistant Installation s’affiche pour vous aider à configurer l’interface DATA 0 de votre appareil. Indiquez les nouvelles valeurs de l'adresse IP, de la passerelle et du masque réseau.

> [AZURE.NOTE]Les contrôleurs d’adresses IP fixes devront être reconfigurés via la page **Configurer** de l’appareil StorSimple dans le portail Azure Classic. Pour plus d'informations, consultez la rubrique [Modification des interfaces réseau](storsimple-modify-device-config.md#modify-network-interfaces).


## Modification des paramètres réseau de DATA 0 via l’applet de commande Set-HcsNetInterface
Vous pouvez également reconfigurer l’interface réseau DATA 0 via l’applet de commande `Set-HcsNetInterface`. L’applet de commande est exécutée à partir de l’interface Windows PowerShell de votre appareil StorSimple. Pour modifier les paramètres de DATA 0, procédez comme suit :

#### Pour modifier les paramètres réseau de DATA 0 via l’applet de commande Set-HcsNetInterface

1. Dans le menu de la console série, sélectionnez l’option 1, **Ouvrir une session avec un accès total**. Lorsque vous y êtes invité, fournissez le mot de passe administrateur de l’appareil. Le mot de passe par défaut est `Password1`.

2.  À l’invite de commandes, tapez :

	`Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
	
    Entrez, entre crochets, les valeurs suivantes de DATA 0 :
											
	- Adresse IPv4
	
	- Passerelle IPv4
	
	- Masque de sous-réseau IPv4
	
	- Adresse IPv4 fixe du contrôleur 0

	- Adresse IPv4 fixe du contrôleur 1

## Étapes suivantes

- Pour configurer les interfaces réseau autres que DATA 0, utilisez la [page Configurer du portail Azure Classic](storsimple-modify-device-config.md). 
- Si vous rencontrez des problèmes lors de la configuration de vos interfaces réseau, consultez la page [Résolution des problèmes de déploiement](storsimple-troubleshoot-deployment.md).

<!---HONumber=AcomDC_1203_2015-->