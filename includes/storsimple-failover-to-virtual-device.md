#### Pour restaurer votre appareil physique en fonction de l’appareil virtuel StorSimple
1. Vérifiez que le conteneur de volumes que vous souhaitez basculer est associé à des instantanés cloud.
2. Dans la page **Appareils**, cliquez sur l’onglet **Conteneurs de volumes**.
3. Sélectionnez un conteneur de volumes que vous souhaitez basculer vers l’appareil virtuel. Cliquez sur le conteneur de volumes pour afficher la liste des volumes qui y sont inclus. Sélectionnez un volume et cliquez sur **Déconnecter** pour mettre le volume hors connexion. Répétez ce processus pour tous les volumes dans le conteneur de volume.
4. Répétez l’étape précédente pour tous les conteneurs de volumes que vous souhaitez basculer vers l’appareil virtuel.
5. Dans la page **Appareils**, sélectionnez l’appareil que vous souhaitez basculer, puis cliquez sur **Basculement** pour ouvrir l’Assistant **Basculement d’appareil**.
6. Dans **Sélectionner le conteneur de volumes à basculer**, sélectionnez les conteneurs de volumes que vous souhaitez basculer. Pour figurer dans cette liste, le conteneur de volumes doit contenir un instantané cloud et être hors connexion. Si un conteneur de volumes que vous souhaitez voir n’est pas présent, annulez l’Assistant et vérifiez s’il est hors connexion.
7. Dans la page suivante, sous **Choisir un appareil cible pour les volumes** dans les conteneurs de volumes, sélectionnez l’appareil virtuel dans la liste déroulante des appareils disponibles. Seuls les appareils possédant la capacité disponible sont affichés dans la liste.
8. Passez en revue tous les paramètres de basculement dans la page **Confirmer le basculement**. S’ils sont corrects, cliquez sur l’icône en forme de coche.

Le processus de basculement commence. Lorsque le basculement est terminé, accédez à la page Appareils et sélectionnez l’appareil virtuel qui a été utilisé comme cible pour le processus de basculement. Accédez à la page Conteneurs de volumes. Tous les conteneurs de volumes, ainsi que les volumes de l’ancien appareil, doivent être répertoriés.

<!---HONumber=AcomDC_1217_2015-->