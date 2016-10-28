<!--author=SharS last changed: 9/17/15-->

#### Pour installer les mises à jour en mode Maintenance via Windows PowerShell pour StorSimple

1. Si vous ne l’avez pas déjà fait, accédez à la console série de l’appareil et sélectionnez l’option 1, **Ouvrir une session avec un accès total**. 

2. Saisissez le mot de passe. Le mot de passe par défaut est **Password1**.

3.  À l’invite de commandes, tapez :

     `Get-HcsUpdateAvailability`
    
4. Le système vous indique si des mises à jour sont disponibles et si elles risquent ou non de provoquer une interruption de service. Pour appliquer les mises à jour sans interruption, vous devez mettre l’appareil en mode Maintenance. Pour obtenir des instructions, consultez l’[Étape 2 : passage en mode Maintenance](storsimple-update-device.md#step2).

5. Lorsque votre périphérique est en mode Maintenance, à l’invite de commandes, tapez : `Start-HcsUpdate`

6. Vous êtes invité à confirmer l’opération. Une fois que vous les avez confirmées, les mises à jour sont installées sur le contrôleur auquel vous êtes connecté. Après l’installation des mises à jour, le contrôleur redémarre.

7. Surveillez l’état des mises à jour. Tapez :

	`Get-HcsUpdateStatus`
	
	Si le `RunInProgress` est `True`, la mise à jour est toujours en cours. Si le `RunInProgress` est `False`, cela veut dire que la mise à jour est terminée.

7. Lorsque la mise à jour est installée sur le contrôleur actuel et que celui-ci a redémarré, connectez-vous à l’autre contrôleur et menez les étapes 1 à 6.

8. Après la mise à jour des deux contrôleurs, quittez le mode Maintenance. Pour obtenir des instructions, consultez l’[Étape 4 : quitter le mode Maintenance](storsimple-update-device.md#step4).

<!---HONumber=Oct15_HO3-->