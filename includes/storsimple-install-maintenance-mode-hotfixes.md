<properties
   pageTitle="Installation des correctifs logiciels en mode Maintenance"
   description="Explique comment installer les correctifs logiciels en mode Maintenance via Windows PowerShell pour StorSimple."
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
   ms.date="04/23/2015"
   ms.author="v-sharos" />

### Pour installer les correctifs logiciels en mode Maintenance via Windows PowerShell pour StorSimple

> [AZURE.IMPORTANT]En mode Maintenance, vous devez appliquer le correctif logiciel d’abord à un contrôleur, puis à l’autre.

1. Mettez l’appareil en mode Maintenance. Consultez la rubrique Changer le mode de fonctionnement de l’appareil pour obtenir des instructions sur le passage en mode Maintenance.

2. Pour appliquer le correctif logiciel, tapez :

     **Start-HcsHotfix**

3. Lorsque vous y êtes invité, indiquez le chemin d’accès au dossier partagé sur le réseau qui contient les fichiers du correctif logiciel.

4. Vous êtes invité à confirmer l’opération. Saisissez **O** pour poursuivre l’installation du correctif logiciel.

5. Après avoir appliqué le correctif logiciel à un contrôleur, ouvrez une session sur l’autre contrôleur. Appliquez le correctif logiciel comme vous l’avez fait pour le contrôleur précédent.

6. Une fois les correctifs logiciels appliqués, quittez le mode Maintenance. Consultez la rubrique [Pour quitter le mode Maintenance](#to-exit-maintenance-mode) pour obtenir des instructions.

<!--HONumber=52-->
