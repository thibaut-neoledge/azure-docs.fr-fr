### Détermination du nom DNS de la machine virtuelle

Pour vous connecter au moteur de base de données SQL Server à partir d'un autre ordinateur, vous devez connaître le nom DNS de la machine virtuelle. Il s'agit du nom utilisé par Internet pour identifier une machine virtuelle. Vous pouvez utiliser l'adresse IP, mais celle-ci peut être modifiée lorsqu'Azure déplace des ressources pour des raisons de redondance ou de maintenance. Le nom DNS reste stable, car il peut être redirigé vers une nouvelle adresse IP.

1. Dans le portail de gestion Azure (ou à partir de l'étape précédente), sélectionnez **VIRTUAL MACHINES**. 

2. Sur la page **INSTANCES DE MACHINE VIRTUELLE**, dans la colonne **Aperçu rapide**, recherchez et copiez le nom DNS de la machine virtuelle.

	![Nom DNS](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)
	

### Connexion au moteur de base de données à partir d'un autre ordinateur
 
1. Sur un ordinateur connecté à Internet, ouvrez SQL Server Management Studio.
2. Dans la boîte de dialogue **Se connecter au serveur** ou **Se connecter au moteur de base de données**, dans la zone **Nom du serveur**, entrez le nom DNS de la machine virtuelle (déterminé pendant la tâche précédente) et le numéro d’un port de point de terminaison public au format *NomDNS,NuméroPort*, par exemple **tutorialtestVM.cloudapp.net,57500**. Pour obtenir le numéro de port, connectez-vous au portail de gestion Azure et cherchez la machine virtuelle. Dans le tableau de bord, cliquez sur **POINTS DE TERMINAISON** et utilisez le **PORT PUBLIC** affecté à **MSSQL**. ![Port public](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. Dans la zone **Authentification**, sélectionnez **Authentification SQL Server**.
5. Dans la zone **Connexion**, entrez le nom d'une connexion créée lors d'une tâche précédente.
6. Dans la zone **Mot de passe**, entrez le mot de passe de connexion que vous avez créé lors d'une tâche précédente.
7. Cliquez sur **Connecter**.

	![Connexion en utilisant SSMS](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)

<!---HONumber=AcomDC_0107_2016-->