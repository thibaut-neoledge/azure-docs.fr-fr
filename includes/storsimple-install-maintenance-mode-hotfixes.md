
#### Pour installer les correctifs logiciels en mode Maintenance via Windows PowerShell pour StorSimple

> [AZURE.IMPORTANT]En mode Maintenance, vous devez appliquer le correctif logiciel d’abord à un contrôleur, puis à l’autre.

1. Mettez l’appareil en mode Maintenance. Consultez l’[Étape 2 : Passage en mode Maintenance](storsimple-update-device.md#step2) pour obtenir des instructions sur le passage en mode Maintenance.

2. Pour appliquer le correctif logiciel, tapez :

     `Start-HcsHotfix`

3. Lorsque vous y êtes invité, indiquez le chemin d’accès au dossier partagé sur le réseau qui contient les fichiers du correctif logiciel.

4. Vous êtes invité à confirmer l’opération. Saisissez **O** pour poursuivre l’installation du correctif logiciel.

5. Après avoir appliqué le correctif logiciel à un contrôleur, ouvrez une session sur l’autre contrôleur. Appliquez le correctif logiciel comme vous l’avez fait pour le contrôleur précédent.

6. Une fois les correctifs logiciels appliqués, quittez le mode Maintenance. Pour obtenir des instructions, consultez l’[Étape 4 : Quitter le mode Maintenance](storsimple-update-device.md#step4).

<!---HONumber=August15_HO8-->