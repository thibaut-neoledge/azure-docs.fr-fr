
#### Pour installer Update 1 à partir du portail de gestion

1. Dans la page du service StorSimple, sélectionnez votre appareil. Accédez à **Appareils** > **Maintenance**.

2. En bas de la page, cliquez sur **Rechercher les mises à jour**. La tâche créée recherche les mises à jour disponibles. Un message s’affiche une fois la tâche terminée.

3. Dans la section **Mises à jour logicielles** de cette page, de nouvelles mises à jour logicielles sont disponibles. Nous vous recommandons de consulter les notes de publication avant d’appliquer Update 1.0 sur votre appareil.

    ![Installer les mises à jour logicielles](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates1-include.png)

4. En bas de la page, cliquez sur **Installer les mises à jour**.

5. Vous êtes invité à confirmer l’opération. Cliquez sur **OK**.

6. Une boîte de dialogue **Installer les mises à jour** s’affiche. Vérifiez que votre appareil satisfait aux contrôles indiqués dans cette boîte de dialogue. Sélectionnez **Je comprends l’obligation ci-dessus et je suis prêt à mettre à jour mon appareil**. Cliquez sur l’icône en forme de coche.

    ![Message de confirmation](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates2-include.png)

7. Un message indique que les vérifications préalables à la mise à jour sont en cours.
  
    ![Notification de la vérification préalable](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates3-include.png)

    Voici un exemple dans lequel la vérification préalable à la mise à jour a échoué. Vous devez vérifier que les contrôleurs d’appareil sont en bon état et en ligne. Vous devez également vérifier l’intégrité des composants matériels. Dans cet exemple, les composants Contrôleur 0 et Contrôleur 1 méritent attention. Vous devrez peut-être contacter le support technique Microsoft si vous ne pouvez pas résoudre ces problèmes vous-même.

    ![Échec de la vérification préalable](./media/storsimple-install-update-via-portal/HCS_PreUpgradeChecksFailed-include.png)

8. Une fois les vérifications préalables à la mise à jour terminées, une tâche de mise à jour est créée. Un message s’affiche une fois la mise à jour créée.
 
    ![Création de la tâche de mise à jour](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates4-include.png)

    La mise à jour est appliquée à votre appareil.
 
9. Pour surveiller la progression de la mise à jour, cliquez sur **Afficher le travail**. La page Travaux indique la progression de la mise à jour.

    ![Progression de la tâche de mise à jour](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates5-include.png)

10. La mise à jour prend quelques heures. À tout moment, vous pouvez afficher les détails du travail.

    ![Détails de la tâche de mise à jour](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates6-include.png)

11. Une fois la mise à jour terminée, accédez à la page **Maintenance** et faites défiler l’écran jusqu’à **Mises à jour logicielles**.

12. Vérifiez que votre appareil exécute **StorSimple 8000 Series Update 1.0 (6.3.9600.17491)**. Le paramètre **Dernière date de mise à jour** doit également être modifié.

    ![Page Maintenance](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates7-include.png)

<!---HONumber=August15_HO7-->