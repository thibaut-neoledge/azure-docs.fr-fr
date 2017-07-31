#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>Pour créer des points de terminaison publics sur l’appliance cloud

1. Connectez-vous au portail Azure.
2. Accédez à **Machines virtuelles**, puis sélectionnez la machine virtuelle utilisée comme appliance cloud et cliquez dessus.
    
3. Vous devez créer une règle de groupe de sécurité réseau pour contrôler le flux du trafic entrant et sortant de votre machine virtuelle. Procédez comme suit pour créer une règle de groupe de sécurité réseau.
    1. Sélectionnez **Groupe de sécurité réseau**.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. Cliquez sur le groupe de sécurité réseau par défaut qui s’affiche.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. Sélectionnez **Règles de sécurité de trafic entrant**.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. Cliquez sur **+ Ajouter** pour créer une règle de sécurité de trafic entrant.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        Dans le panneau Ajouter une règle de sécurité de trafic entrant :

        1. Pour **Nom**, tapez le nom de point de terminaison suivant : WinRMHttps.
        
        2. Pour **Priorité**, sélectionnez un nombre inférieur à 1 000 (qui est la priorité de la règle par défaut). Plus la valeur est élevée, plus la priorité est faible.

        3. Définissez **Source** sur **Any** (Quelconque).

        4. Pour **Service**, sélectionnez **WinRM**. Le **protocole** est automatiquement défini sur **TCP** et **l’étendue du port** est définie sur **5986**.

        5. Cliquez sur **OK** pour créer la règle.

            ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. L’étape finale consiste à associer un sous-réseau ou une interface réseau spécifique à votre groupe de sécurité réseau. Procédez comme suit pour associer un sous-réseau à votre groupe de sécurité réseau.
    1. Accédez à **Sous-réseaux**.
    2. Cliquez sur **+ Associer**.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. Sélectionnez votre réseau virtuel, puis sélectionnez le sous-réseau approprié.
    4. Cliquez sur **OK** pour créer la règle.

        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

Une fois la règle créée, vous pouvez afficher les détails correspondants pour déterminer l’adresse IP virtuelle publique. Enregistrez cette adresse.


