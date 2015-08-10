<properties 
   pageTitle="Changement des modes de votre appareil StorSimple"
   description="Découvrez les différents modes d’appareil StorSimple et comment modifier l’appareil."
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
   ms.date="07/30/2015"
   ms.author="alkohli" />

# Modes de l’appareil StorSimple

Cet article fournit une brève description des modes avec lesquels votre appareil StorSimple peut fonctionner. Votre appareil StorSimple peut fonctionner en trois modes : Normal, Maintenance et Récupération.

À la fin de cet article, vous :

- connaîtrez les modes de l’appareil StorSimple,
- saurez déterminer le mode dans lequel se trouve l’appareil StorSimple,
- saurez passer du mode Normal au mode Maintenance, et inversement.


Les tâches de gestion ci-dessus peuvent uniquement être effectuées via l’interface Windows PowerShell de votre appareil StorSimple.

## À propos des modes de l’appareil StorSimple

Votre appareil StorSimple peut fonctionner en mode Normal, Maintenance ou Récupération. Chacun de ces modes est brièvement décrit ci-dessous.

### Mode Normal

Il s’agit du mode de fonctionnement normal pour un appareil StorSimple entièrement configuré. Par défaut, votre appareil doit être en mode Normal.

### Mode Maintenance

L’appareil StorSimple devra parfois être placé en mode Maintenance. Ce mode permet d’effectuer la maintenance sur l’appareil et d’installer des mises à jour sans interruption, telles que celles liées au microprogramme de disque et le microprogramme USM (et qui peuvent toutes deux être récurrentes et causer des perturbations).

Vous pouvez placer le système en mode Maintenance uniquement via Windows PowerShell pour StorSimple. Dans ce mode, toutes les demandes d’E/S sont suspendues. Les services tels que la mémoire vive non volatile (NVRAM) ou le service de cluster sont également arrêtés. Les deux contrôleurs sont redémarrés lorsque vous entrez dans ce mode ou que vous le quittez. Lorsque vous quittez le mode Maintenance, tous les services reprennent et doivent être sains. Cela peut prendre quelques minutes.

>[AZURE.NOTE]**Le mode Maintenance est uniquement pris en charge sur un appareil en état de fonctionnement. Il n’est pas pris en charge sur un appareil dont l’un ou les deux contrôleurs ne fonctionnent pas.** </br>

### Mode Récupération

Le mode Récupération peut être décrit comme un « mode sans échec avec prise en charge du réseau Windows ». Le mode Récupération engage l’équipe de support technique Microsoft et leur permet d’effectuer des diagnostics sur le système. Le principal objectif du mode Récupération consiste à récupérer les journaux système.

Si votre système passe en mode Récupération, vous devez contacter le support technique Microsoft pour les étapes suivantes. Pour plus d’informations, accédez à [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md).

>[AZURE.NOTE]**Vous ne pouvez pas définir le mode Récupération sur l’appareil. Si l’appareil est en mauvais état, le mode Récupération tente de définir l’état de l’appareil de telle sorte que le personnel du support technique Microsoft puisse l’examiner.**

## Identification des modes de l’appareil StorSimple

Pour déterminer le mode de l’appareil, procédez comme suit :

1. Ouvrez une session sur la console série de l’appareil en suivant les étapes dans [Utilisation de PuTTY pour se connecter à la console série de l’appareil](https://msdn.microsoft.com/library/azure/dn757808.aspx).
2. Examinez le message de bannière situé dans le menu de la console série de l’appareil. Ce message indique explicitement si l’appareil est en mode Maintenance ou Récupération. Si le message ne contient pas d’informations spécifiques concernant le mode du système, cela signifie que l’appareil est en mode Normal.

## Changement du mode de votre appareil StorSimple 

Vous pouvez placer l’appareil StorSimple en mode Maintenance (à partir du mode Normal) pour effectuer une maintenance ou installer des mises à jour du mode Maintenance. Effectuez les procédures suivantes pour entrer dans le mode Maintenance ou le quitter.

> [AZURE.IMPORTANT]Avant de passer en mode Maintenance, vérifiez que les deux contrôleurs d’appareil sont sains en évaluant l’État du matériel sur la page Maintenance du portail de gestion. Si le contrôleur n’est pas sain, contactez le support technique Microsoft pour connaître les étapes suivantes. Pour plus d’informations, accédez à [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md).

#### Pour passer en mode Maintenance

1. Ouvrez une session sur la console série de l’appareil en suivant les étapes dans [Utilisation de PuTTY pour se connecter à la console série de l’appareil](https://msdn.microsoft.com/library/azure/dn757808.aspx).

1. Dans le menu de la console série, sélectionnez l’option 1, **Ouvrir une session avec un accès total**. Lorsque vous y êtes invité, fournissez le **mot de passe administrateur de l’appareil**. Le mot de passe par défaut est : `Password1`.

1. À l'invite de commandes, tapez

	`Enter-HcsMaintenanceMode`

1. Un message d’avertissement s’affiche pour vous indiquer que le mode Maintenance va perturber toutes les demandes d’E/S et annuler la connexion au portail de gestion. Vous êtes invité à confirmer l’opération. Saisissez **O** pour passer en mode Maintenance.

1. Les deux contrôleurs redémarrent. Une fois le redémarrage terminé, un autre message s’affiche pour indiquer que l’appareil est en mode Maintenance.


#### Pour quitter le mode Maintenance

1. Connectez-vous à la console série de l’appareil. Vérifiez à partir du message de bannière que votre appareil est en mode Maintenance.

2.  À l’invite de commandes, tapez :

	`Exit-HcsMaintenanceMode`

1. Un message d’avertissement et un message de confirmation s’affichent. Saisissez **O** pour quitter le mode Maintenance.

1. Les deux contrôleurs redémarrent. Une fois le redémarrage terminé, un autre message indique que le périphérique est en mode normal.


## Étapes suivantes

Découvrez comment [appliquer les mises à jour des modes Normal et Maintenance](storsimple-update-device.md) sur votre appareil StorSimple.

<!---HONumber=July15_HO5-->