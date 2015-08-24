
#### Attacher les câbles SAS

1. Identifiez le boîtier principal et le boîtier EBOD Les deux boîtiers peuvent être identifiés en examinant leurs fonds de panier respectifs. Reportez-vous à la figure suivante. 

    ![Fond de panier du boîtier principal et du boîtier EBOD](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)

    **Vue arrière du boîtier principal et du boîtier EBOD**

    |Étiquette|Description|
    |:----|:----------|
    |1|Boîtier principal|
    |2|Boîtier EBOD|

2. Recherchez les numéros de série sur le boîtier principal et le boîtier EBOD. L'étiquette du numéro de série est apposée sur la languette arrière de chaque boîtier. Les numéros de série doivent être identiques sur les deux boîtiers. Contactez immédiatement le [support technique Microsoft](storsimple-contact-microsoft-support.md) si les numéros de série ne correspondent pas. Reportez-vous à l'illustration suivante pour rechercher les numéros de série.

    ![Vue arrière du boîtier avec emplacement du numéro de série](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)

    **Emplacement de l'étiquette de numéro de série**

    |Étiquette|Description|
    |:----|:----------|
    |1|Languette du boîtier|

3. Utilisez les câbles SAS fournis pour connecter le boîtier EBOD au boîtier principal en procédant comme suit :

    1. Identifiez les quatre ports SAS sur le boîtier principal et le boîtier EBOD. Les ports SAS sont indiqués par la mention EBOD sur le boîtier principal et par la mention CTRL sur le boîtier EBOD, comme indiqué dans l'illustration du branchement des câbles SAS ci-dessous.

    2. Utilisez les câbles SAS fournis pour connecter les ports EBOD aux ports CTRL.

    3. Le port EBOD sur le contrôleur 0 doit être raccordé au port CTRL du contrôleur 0 du boîtier EBOD. Le port EBOD sur le contrôleur 1 doit être raccordé au port CTRL du contrôleur 1 du boîtier EBOD. Consultez l'illustration suivante pour plus d'informations.
																	
     ![Branchement des câbles SAS de votre appareil](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)

     **Branchement des câbles SAS**

    |Étiquette|Description|
    |:----|:----------|
    |Une|Boîtier principal|
    |B|Boîtier EBOD|
    |1|Contrôleur 0|
    |2|Contrôleur 1|
    |3|Contrôleur 0 du boîtier EBOD|
    |4|Contrôleur 1 du boîtier EBOD|
    |5, 6|Ports SAS sur le boîtier principal (mention EBOD)|
    |7, 8|Ports SAS sur le boîtier EBOD (mention CTRL)|

<!---HONumber=August15_HO7-->