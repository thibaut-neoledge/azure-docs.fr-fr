<properties
   pageTitle="Passage en mode Maintenance"
   description="Explique comment placer l’appareil StorSimple en mode Maintenance."
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

### Pour passer en mode Maintenance

1. Dans le menu de la console série, sélectionnez l’option 1, **Ouvrir une session avec un accès total**.

2. Saisissez le mot de passe. Le mot de passe par défaut est **Password1**.

3. À l'invite de commandes, tapez

    **Enter-HcsMaintenanceMode**

4. Un message d’avertissement s’affiche pour vous indiquer que le mode Maintenance va perturber toutes les demandes d’E/S et annuler la connexion au portail de gestion. Vous êtes invité à confirmer l’opération. Saisissez **O** pour passer en mode Maintenance.

    Les deux contrôleurs redémarrent. Une fois le redémarrage terminé, un autre message s’affiche pour indiquer que l’appareil est en mode Maintenance.

<!--HONumber=52-->
