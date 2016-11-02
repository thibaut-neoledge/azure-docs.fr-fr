### Détermination du nom DNS de la machine virtuelle

Pour vous connecter au moteur de base de données SQL Server à partir d'un autre ordinateur, vous devez connaître le nom DNS de la machine virtuelle. Il s'agit du nom utilisé par Internet pour identifier une machine virtuelle. Vous pouvez utiliser l'adresse IP, mais celle-ci peut être modifiée lorsqu'Azure déplace des ressources pour des raisons de redondance ou de maintenance. Le nom DNS reste stable, car il peut être redirigé vers une nouvelle adresse IP.

1. Dans le portail Azure (ou à partir de l’étape précédente), sélectionnez **Machines virtuelles (classique)**.

2. Sélectionnez votre machine virtuelle SQL.

2. Dans le panneau **Machine virtuelle**, copiez le **Nom DNS** pour la machine virtuelle.

	![Nom DNS](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)


### Connexion au moteur de base de données à partir d'un autre ordinateur

1. Sur un ordinateur connecté à Internet, ouvrez SQL Server Management Studio.

2. Dans la boîte de dialogue **Se connecter au serveur** ou **Se connecter au moteur de base de données**, dans la zone **Nom du serveur**, entrez le nom DNS de la machine virtuelle (déterminé durant la tâche précédente), ainsi qu’un numéro de port de point de terminaison public au format *NomDNS,numéro\_port* tel que **mysqlvm.cloudapp.net,57500**.

	![Connexion en utilisant SSMS](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)

	Si vous ne connaissez pas le numéro de port du point de terminaison public créé précédemment, vous le trouverez dans la zone **Points de terminaison** du panneau **Machine virtuelle**.

	![Port public](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)

3. Dans la zone **Authentification**, sélectionnez **Authentification SQL Server**.

5. Dans la zone **Connexion**, entrez le nom d'une connexion créée lors d'une tâche précédente.

6. Dans la zone **Mot de passe**, entrez le mot de passe de connexion que vous avez créé lors d'une tâche précédente.

7. Cliquez sur **Connecter**.

<!---HONumber=AcomDC_0629_2016-->