<!--author=alkohli last changed: 03/17/16-->

## Résolution des échecs de mise à jour

**Que faire en cas d’affichage d’une notification d’échec des vérifications préalables à la mise à niveau ?**

Si une vérification préalable est mise en échec, consultez de nouveau la barre des notifications détaillées de la partie inférieure de la page. Elle fournit des indications relatives aux vérifications préalables mises en échec. L’illustration suivante représente un exemple d’apparition de ce type de notification. Dans ce cas, les contrôles d’intégrité du contrôleur et des composants ont échoué. Dans la section **État du matériel**, vous constatez que les composants **Contrôleur 0** et **Contrôleur 1** requièrent votre attention.

  ![Échec de la vérification préalable](./media/storsimple-install-troubleshooting/HCS_PreUpdateCheckFailed-include.png)

Vous devez vous assurer que les deux contrôleurs sont intègres et en ligne. Il est également nécessaire de vérifier que l’intégrité des composants matériels de l’appareil StorSimple est vérifiée sur la page Maintenance. Vous pouvez ensuite essayer d’installer les mises à jour. Si vous n’êtes pas en mesure de corriger les problèmes de composants matériels, sollicitez le Support Microsoft, qui vous indiquera la procédure à suivre.

**Que faire si vous recevez un message d’erreur « Impossible d’installer les mises à jour » et que l’on vous recommande de consulter le guide de résolution des mises à jour pour déterminer l’origine du problème ?**

Cela peut être dû au fait que vous ne disposez d’aucune connectivité aux serveurs Microsoft Update. Il s’agit d’une vérification manuelle obligatoire. Si vous perdez la connectivité au serveur de mise à jour, votre tâche de mise à jour est mise en échec. Pour vérifier la connectivité, exécutez l’applet de commande suivante à partir de l’interface Windows PowerShell de votre appareil StorSimple :

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Exécutez l’applet de commande sur les deux contrôleurs.

Si vous avez vérifié l’existence de la connectivité et que le problème persiste, sollicitez le Support Microsoft pour connaître la procédure à suivre.

<!---HONumber=AcomDC_0323_2016-->