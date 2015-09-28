<properties 
   pageTitle="Changer vos mots de passe StorSimple | Microsoft Azure" 
   description="Explique comment utiliser le service StorSimple Manager pour modifier vos mots de passe du Gestionnaire d'instantanés StorSimple et d’administrateur de l’appareil." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="09/15/2015"
   ms.author="v-sharos@microsoft.com"/>

# Utilisation du service StorSimple Manager pour modifier vos mots de passe StorSimple

## Vue d'ensemble 

La page **Configurer** du portail de gestion contient tous les paramètres reconfigurables d’un appareil StorSimple géré par un service StorSimple Manager. Ce didacticiel explique comment utiliser la page **Configurer** pour modifier votre mot de passe du Gestionnaire d'instantanés StorSimple ou d’administrateur de l’appareil.

## Modification du mot de passe du Gestionnaire d’instantanés StorSimple

Le Gestionnaire d’instantanés StorSimple réside sur l’ordinateur hôte Windows et permet aux administrateurs de gérer les sauvegardes de votre appareil StorSimple sous la forme d’instantanés cloud ou locaux.

Au moment de configurer un appareil dans le Gestionnaire d’instantanés StorSimple, vous êtes invité à fournir l’adresse IP et le mot de passe de votre appareil de stockage afin de l’authentifier. Ce mot de passe est d’abord configuré via l’interface Windows PowerShell. Pour plus d'informations, consultez l’[Étape 3 : configuration et inscription de l'appareil via Windows PowerShell pour StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) dans la page [Déploiement de votre appareil StorSimple local](storsimple-deployment-walkthrough.md).

Le mot de passe qui a été défini initialement dans l'interface Windows PowerShell au moment de l’inscription peut ensuite être modifié via le portail de gestion. Pour modifier le mot de passe du Gestionnaire d’instantanés StorSimple, procédez comme suit.

#### Pour modifier le mot de passe du Gestionnaire d’instantanés StorSimple

1. Dans le portail, cliquez sur **Appareils** > **Configurer** pour votre appareil.

2. Faites défiler l’écran vers le bas jusqu’à la section **Gestionnaire d’instantanés StorSimple**. Entrez un mot de passe 14 ou 15 caractères. Assurez-vous que le mot de passe contient une combinaison de caractères en majuscules, en minuscules, numériques et spéciaux.

3. Confirmez le mot de passe.

4. Cliquez sur **Enregistrer** au bas de la page.

Le mot de passe du Gestionnaire d’instantanés StorSimple doit maintenant être mis à jour.
 
## Modification du mot de passe d’administrateur de l’appareil

Quand vous utilisez l’interface Windows PowerShell pour accéder à l’appareil StorSimple, vous devez entrer un mot de passe d’administrateur de l’appareil. À l’inscription du premier appareil StorSimple auprès d’un service, le mot de passe par défaut de cette interface est *Password1*. Pour assurer la sécurité de vos données, il vous est demandé de modifier ce mot de passe à la fin de l’inscription. Vous ne pouvez pas quitter le processus d'inscription sans modifier ce mot de passe. Pour plus d'informations, consultez l’[Étape 3 : configuration et inscription de l'appareil via Windows PowerShell pour StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) dans la page [Déploiement de votre appareil StorSimple local](storsimple-deployment-walkthrough.md).

Le mot de passe qui a été défini initialement dans l'interface Windows PowerShell au moment de l’inscription peut ensuite être modifié via le portail de gestion. Pour modifier le mot de passe d’administrateur de l’appareil, procédez comme suit.

#### Pour modifier le mot de passe d’administrateur de l’appareil

1. Dans le portail, cliquez sur **Appareils** > **Configurer** pour votre appareil.

2. Faites défiler l’écran vers le bas jusqu’à la section **Mot de passe de l’administrateur de l’appareil**. Indiquez un mot de passe Administrateur contenant entre 8 et 15 caractères. Le mot de passe doit contenir une combinaison de caractères en majuscules, en minuscules, numériques et spéciaux.

3. Confirmez le mot de passe.

4. Cliquez sur **Enregistrer** au bas de la page.

Le mot de passe Administrateur d’appareil doit maintenant être à jour. Vous pouvez utiliser ce mot de passe modifié pour accéder à l’interface Windows PowerShell.

## Étapes suivantes

- En savoir plus sur la [sécurité StorSimple](storsimple-security.md).

- En savoir plus sur la [modification de la configuration de votre appareil](storsimple-modify-device-config.md).

- En savoir plus sur [l’utilisation du service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=Sept15_HO3-->