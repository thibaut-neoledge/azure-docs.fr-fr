<!--author=SharS last changed: 9/17/15-->

#### Pour installer Update 1.2 à partir du portail de gestion

1. Dans la page du service StorSimple, sélectionnez votre appareil. Accédez à **Appareils** > **Maintenance**.

2. En bas de la page, cliquez sur **Rechercher les mises à jour**. La tâche créée recherche les mises à jour disponibles. Un message s’affiche une fois la tâche terminée.

3. Dans la section **Mises à jour logicielles** de cette page, de nouvelles mises à jour logicielles sont disponibles. Nous vous recommandons de consulter les notes de publication avant d’appliquer Update 1.2 sur votre appareil.

    ![Installer les mises à jour logicielles](./media/storsimple-install-update-via-portal/InstallUpdate12_11M.png)

4. En bas de la page, cliquez sur **Installer les mises à jour**.

5. Vous êtes invité à confirmer l’opération. Cliquez sur **OK**.

6. Une boîte de dialogue **Installer les mises à jour** s’affiche. Vérifiez que votre appareil satisfait aux contrôles indiqués dans cette boîte de dialogue. Ces étapes ont été effectuées avant la mise à jour. Sélectionnez **Je comprends l’obligation ci-dessus et je suis prêt à mettre à jour mon appareil**. Cliquez sur l’icône en forme de coche.

    ![Message de confirmation](./media/storsimple-install-update-via-portal/InstallUpdate12_2M.png)

7. Un ensemble de vérifications automatiques préalables va démarrer. Il s’agit des actions suivantes :

	- **Contrôles d’intégrité des contrôleurs**, destinés à vérifier que les contrôleurs d’appareil sont en bon état et en ligne.
	
	- **Contrôles d’intégrité des composants matériels**, utilisés pour vérifier l’état des composants matériels de votre appareil StorSimple.
	
	- **Contrôles DATA 0**, afin de s’assurer de l’activation de DATA 0 sur votre appareil. Si cette interface n’est pas activée, activez-la, puis réessayez.
	
	- **Contrôles DATA 2 et DATA 3** pour vérifier que les interfaces réseau DATA 2 et DATA 3 ne sont pas activées. Si ces interfaces sont activées, désactivez-les, puis essayez de mettre à jour votre appareil. Cette vérification est effectuée uniquement si vous mettez à jour un appareil exécutant un logiciel de disponibilité générale. Les appareils exécutant les versions 0.1, 0.2 ou 0.3 ne sont pas soumis à cette vérification.
	
	- **Contrôle de passerelle** sur tout appareil exécutant une version antérieure à Update 1. Cette vérification est effectuée sur tous les appareils exécutant le logiciel antérieur à Update 1, mais elle échoue sur les appareils présentant une passerelle configurée pour une interface réseau différente de DATA 0.
 
	La solution Update 1.2 est installée uniquement si l'ensemble ci-dessus des vérifications préalables à la mise à jour sont correctement effectuées. Un message indique que les vérifications préalables à la mise à jour sont en cours.
  
    ![Notification de la vérification préalable](./media/storsimple-install-update-via-portal/InstallUpdate12_3M.png)

    Voici un exemple dans lequel la vérification préalable à la mise à jour a échoué. Vous devez vérifier que les contrôleurs d’appareil sont en bon état et en ligne. Vous devez également vérifier l’intégrité des composants matériels. Dans cet exemple, les composants Contrôleur 0 et Contrôleur 1 méritent attention. Vous devrez peut-être contacter le support technique Microsoft si vous ne pouvez pas résoudre ces problèmes vous-même.

   	 ![Échec de la vérification préalable](./media/storsimple-install-update-via-portal/HCS_PreUpgradeChecksFailed-include.png)

	> [AZURE.NOTE]Toute mise à jour ultérieure à l'installation d'Update 1.2 sur votre appareil StorSimple n'est pas soumise aux contrôles DATA 2, DATA 3 et de passerelle. Les autres vérifications préalables seront toujours requises.


8. Une fois les vérifications préalables à la mise à jour terminées, une tâche de mise à jour est créée. Un message s’affiche une fois la mise à jour créée.
 
    ![Création de la tâche de mise à jour](./media/storsimple-install-update-via-portal/InstallUpdate12_44M.png)

    La mise à jour est appliquée à votre appareil.
 
9. Pour surveiller la progression de la mise à jour, cliquez sur **Afficher le travail**. La page **Travaux** indique la progression de la mise à jour.

    ![Progression de la tâche de mise à jour](./media/storsimple-install-update-via-portal/InstallUpdate12_5M.png)

10. La mise à jour prend quelques heures. À tout moment, vous pouvez afficher les détails du travail.

    ![Détails de la tâche de mise à jour](./media/storsimple-install-update-via-portal/InstallUpdate12_6M.png)

11. Une fois la mise à jour terminée, accédez à la page **Maintenance** et faites défiler l’écran jusqu’à **Mises à jour logicielles**.

12. Vérifiez que votre appareil exécute **StorSimple 8000 Series Update 1.2 (6.3.9600.17584)**. Le paramètre **Dernière date de mise à jour** doit également être modifié.

    ![Page Maintenance](./media/storsimple-install-update-via-portal/InstallUpdate12_10M.png)

13. Vous voyez maintenant que les mises à jour du mode Maintenance sont disponibles. Ces mises à jour entraînent des temps d'arrêt de l'appareil et ne peuvent être appliquées que par le biais de l'interface Windows PowerShell de votre appareil. Suivez les instructions sous [Installation des mises à jour en mode Maintenance](storsimple-update-device.md#install-maintenance-mode-updates-via-windows-powershell-for-storsimple) pour installer ces mises à jour via Windows PowerShell pour StorSimple.

> [AZURE.NOTE]Dans certains cas, le message indiquant que les mises à jour du mode Maintenance sont disponibles peut s'afficher 24 heures après que les mises à jour du mode Maintenance aient été correctement appliquées sur l'appareil.

<!---HONumber=Sept15_HO4-->