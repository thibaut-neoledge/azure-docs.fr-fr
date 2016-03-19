<!--author=alkohli last changed: 9/16/15-->


#### Raccorder votre appareil à l'alimentation électrique

>[AZURE.NOTE]Les deux boîtiers sur votre appareil StorSimple incluent des PCM redondants. Pour chaque boîtier, les PCM doivent être installés et connectés à des sources d'alimentation différentes pour garantir une haute disponibilité.

1. Assurez-vous que les commutateurs d’alimentation sont en position d’arrêt sur tous les PCM.

2. Dans le boîtier principal, branchez les câbles d'alimentation aux deux PCM. Les câbles d'alimentation sont représentés en rouge dans le schéma de branchement des câbles d'alimentation suivant.

3. Vérifiez que les deux PCM du boîtier principal utilisent des sources d'alimentation distinctes.

4. Fixez les câbles d'alimentation aux unités de distribution de l'alimentation du rack, comme illustré sur le schéma de branchement des câbles d'alimentation suivant.

5. Répétez les étapes 2 à 4 pour le boîtier EBOD.

6. Activez le boîtier EBOD en positionnant les commutateurs d'alimentation de chaque PCM sur ON.

7. Assurez-vous que le boîtier EBOD est activé en vérifiant que les LED vertes à l'arrière du contrôleur EBOD sont allumées.

8. Activez le boîtier principal en positionnant chaque commutateur d'alimentation de PCM sur ON.

9. Assurez-vous que le système fonctionne en vérifiant que les LED du contrôleur sont allumés.

10. Assurez-vous que la connexion entre le contrôleur EBOD et le contrôleur de l'appareil est active en vérifiant que les quatre LED à côté du port SAS sur le contrôleur du boîtier EBOD sont vertes.

    >[AZURE.IMPORTANT]Pour garantir une haute disponibilité de votre système, nous vous recommandons de vous conformer strictement au schéma de branchement des câbles d'alimentation illustré dans le diagramme suivant.

    ![Raccorder votre appareil à l’alimentation électrique](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)

    **Branchement des câbles d’alimentation**

    |Étiquette|Description|
    |:----|:----------|
    |1|Boîtier principal|
    |2|PCM 0|
    |3|PCM 1|
    |4|Contrôleur 0|
    |5|Contrôleur 1|
    |6|Contrôleur 0 du boîtier EBOD|
    |7|Contrôleur 1 du boîtier EBOD|
    |8|Boîtier EBOD|
    |9|PDU|

<!---HONumber=Oct15_HO3-->