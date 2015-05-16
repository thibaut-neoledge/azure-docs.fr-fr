<properties
   pageTitle="Installation des mises à jour en mode Maintenance"
   description="Explique comment installer les mises à jour en mode Maintenance via Windows PowerShell pour StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" /> <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/21/2015"
   ms.author="v-sharos" />

### Pour installer les mises à jour en mode Maintenance via Windows PowerShell pour StorSimple

1. Si vous ne l’avez pas déjà fait, accédez à la console série de l’appareil et sélectionnez l’option 1, **Ouvrir une session avec un accès total**. 

2. Saisissez le mot de passe. Le mot de passe par défaut est **Password1**.

3.  À l’invite de commandes, tapez :

   **Get-HcsUpdateAvailability**
    
    Le système vous indique si des mises à jour sont disponibles et si elles risquent ou non de provoquer une interruption de service.

4. Pour appliquer les mises à jour sans interruption, vous devez mettre l’appareil en mode Maintenance. Consultez la rubrique [Pour passer en mode Maintenance](#to-enter-maintenance-mode) afin d’obtenir des instructions.

5. Une fois l’appareil en mode Maintenance, à l’invite de commandes, tapez :

    **Start-HcsUpdate**

6. Vous êtes invité à confirmer l’opération. Une fois que vous les avez confirmées, les mises à jour sont installées sur le contrôleur auquel vous êtes connecté. Après l’installation des mises à jour, le contrôleur redémarre.

7. Une fois le redémarrage du premier contrôleur terminé, connectez-vous à l’autre contrôleur et suivez les étapes 1 à 6.

8. Après la mise à jour des deux contrôleurs, quittez le mode Maintenance. Consultez la rubrique [Pour quitter le mode Maintenance]\]\(\#to-exit-maintenance-mode\) afin d’obtenir des instructions.

<!--HONumber=52-->
