<properties 
   pageTitle="Contacter le support Microsoft | Microsoft Azure"
   description="Découvrez comment créer une demande de support et démarrer une session de support sur votre appareil StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/24/2015"
   ms.author="alkohli" />

# Contacter le support Microsoft

Si vous rencontrez des problèmes avec votre solution Microsoft Azure StorSimple, vous pouvez créer une demande de service pour le support technique. Lors d’une session en ligne avec votre ingénieur de support, vous devrez également démarrer une session de support sur votre appareil StorSimple. Cet article vous guide tout au long du processus de création d'une demande de support et de démarrage d’une session de prise en charge dans l'interface Windows PowerShell de votre appareil StorSimple.

## Création d’une demande de support

Procédez comme suit pour créer une demande de support.

#### Création d’une demande de support

1. Une demande de support peut être créée via le [Portail de gestion](http://manage.windowsazure.com/). Dans le [Portail de gestion](http://manage.windowsazure.com/), cliquez sur votre **nom de compte**, puis sur **Contact Microsoft Support**.

	![Contacter le support MS via le Portail de gestion](./media/storsimple-contact-microsoft-support/IC777286.png)

1. Dans la boîte de dialogue **Contact Microsoft Support** :


	- Dans la liste déroulante, sélectionnez la cible **Subscription** associée à votre service StorSimple Manager. Définissez **Support Type** sur **Technical**. Vous avez besoin d'un plan de support payant pour bénéficier du support technique.

	1. Cliquez sur l’icône en forme de coche ![Icône en forme de coche](./media/storsimple-contact-microsoft-support/IC740895.png) pour la définir sur **Create Ticket**.

1. Dans la fenêtre **Microsoft Support**, dans la liste déroulante **Product**, choisissez **StorSimple**.

	![Contacter le support Microsoft - Produit](./media/storsimple-contact-microsoft-support/IC777288.png)

1. Suivez les instructions à l’écran pour classer correctement votre demande et fournissez une description claire et précise de votre problème.

Une fois votre demande envoyée, un ingénieur de support vous contactera dès que possible pour traiter votre demande.

## Démarrage d’une session de support dans Windows PowerShell pour StorSimple

Pour résoudre les problèmes que vous pouvez rencontrer avec l’appareil StorSimple, vous devrez contacter l'équipe de Support technique de Microsoft. Le support Microsoft devra peut-être utiliser une session de support pour se connecter à votre appareil.

Procédez comme suit pour démarrer une session de support :

#### Démarrage d’une session de support

1. Accédez directement à l’appareil à l'aide de la console série ou via une session telnet à partir d'un ordinateur distant. Pour cela, suivez les étapes de la section [Utiliser PuTTY pour se connecter à la console série de l’appareil](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

1. Dans la session qui s’ouvre, appuyez sur la touche **Entrée** pour afficher une invite de commande.

1. Dans le menu de la console série, sélectionnez l’option 1, **Ouvrir une session avec un accès total**.

1. À l'invite de commande, entrez le mot de passe suivant :

	`Password1`

1. À l'invite de commande, tapez la commande suivante :

	`Enable-HcsSupportAccess`

1. Une chaîne chiffrée s'affiche. Copiez cette chaîne dans un éditeur de texte comme le Bloc-notes.

1. Enregistrez cette chaîne et envoyez-la par e-mail au support Microsoft.

> [AZURE.IMPORTANT]Vous pouvez désactiver l'accès au support en exécutant `Disable-HcsSupportAccess`. L’appareil StorSimple tentera également de désactiver l'accès au support 8 heures après le début de la session. Il est recommandé de modifier vos informations d'identification de l’appareil StorSimple après le lancement d'une session de support.

<!---HONumber=August15_HO6-->