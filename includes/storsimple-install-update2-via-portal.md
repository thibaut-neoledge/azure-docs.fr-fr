<!--author=alkohli last changed: 03/17/16-->

#### Pour installer une mise à jour à partir du portail Azure

1. Dans la page du service StorSimple, sélectionnez votre appareil. Accédez à **Appareils** > **Maintenance**.

2. En bas de la page, cliquez sur **Rechercher les mises à jour**. La tâche créée recherche les mises à jour disponibles. Un message s’affiche une fois la tâche terminée.

3. Dans la section **Mises à jour logicielles** de cette page, de nouvelles mises à jour logicielles sont disponibles. Nous vous recommandons de consulter les notes de publication avant d’appliquer une mise à jour sur votre appareil.

4. En bas de la page, cliquez sur **Installer les mises à jour**, puis sur **OK**.

5. Dans la boîte de dialogue **Installer les mises à jour**, vérifiez que vous avez suivi les recommandations, puis sélectionnez **Je comprends la condition requise ci-dessus et je suis prêt à mettre à niveau mon appareil** et cliquez sur le bouton de confirmation.

    ![Message de confirmation](./media/storsimple-install-update2-via-portal/InstallUpdate12_2M.png)

7. Un ensemble de vérifications préalables démarre. Il s’agit des actions suivantes :

	- **Contrôles d’intégrité des contrôleurs**, destinés à vérifier que les contrôleurs d’appareil sont en bon état et en ligne.

	- **Contrôles d’intégrité des composants matériels**, utilisés pour vérifier l’état des composants matériels de votre appareil StorSimple.

	- **Contrôles DATA 0**, afin de s’assurer de l’activation de DATA 0 sur votre appareil. Si cette interface n’est pas activée, activez-la, puis réessayez.

	- **Contrôles DATA 2 et DATA 3** pour vérifier que les interfaces réseau DATA 2 et DATA 3 ne sont pas activées. Si ces interfaces sont activées, désactivez-les, puis essayez de mettre à jour votre appareil. Cette vérification est effectuée uniquement si vous mettez à jour un appareil exécutant un logiciel de disponibilité générale. Les appareils exécutant les versions 0.1, 0.2 ou 0.3 ne sont pas soumis à cette vérification.

	- **Contrôle de passerelle** sur tout appareil exécutant une version antérieure à Update 1. Cette vérification est effectuée sur tous les appareils exécutant le logiciel antérieur à Update 1, mais elle échoue sur les appareils présentant une passerelle configurée pour une interface réseau différente de DATA 0.

	La mise à jour est installée si l’ensemble des vérifications préalables sont correctement effectuées. Un message indique que les vérifications préalables sont en cours.

    ![Notification de la vérification préalable](./media/storsimple-install-update2-via-portal/InstallUpdate12_3M.png)

    Voici un exemple dans lequel la vérification préalable a échoué. Vous devez vérifier que les contrôleurs d’appareil sont en bon état et en ligne. Vous devez également vérifier l’intégrité des composants matériels. Dans cet exemple, les composants Contrôleur 0 et Contrôleur 1 méritent attention. Vous devrez peut-être contacter le support technique Microsoft si vous ne pouvez pas résoudre ces problèmes vous-même.

   	 ![Les vérifications ont échoué](./media/storsimple-install-update2-via-portal/HCS_PreUpgradeChecksFailed-include.png)

8. Une fois les vérifications préalables terminées, une tâche de mise à jour est créée. Un message s’affiche une fois la mise à jour créée.

    ![Création de la tâche de mise à jour](./media/storsimple-install-update2-via-portal/InstallUpdate12_44M.png)

    La mise à jour est appliquée à votre appareil.

9. Pour surveiller la progression de la mise à jour, cliquez sur **Afficher le travail**. La page **Travaux** indique la progression de la mise à jour.

10. La mise à jour prend quelques heures. Sélectionnez la tâche de mise à jour et cliquez sur **Détails** pour afficher les détails de la tâche à tout moment.

11. Une fois la mise à jour terminée, accédez à la page **Maintenance** et faites défiler l’écran jusqu’à **Mises à jour logicielles**.

<!---HONumber=AcomDC_0323_2016-->