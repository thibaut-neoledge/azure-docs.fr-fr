### Création d'un point de terminaison TCP pour la machine virtuelle

Pour accéder à SQL Server depuis Internet, la machine virtuelle doit avoir un point de terminaison pour écouter les communications TCP entrantes. Dans cette étape de configuration Azure, le trafic du port TCP entrant est dirigé vers un port TCP accessible à la machine virtuelle.

>[AZURE.NOTE]Si vous vous connectez dans le même service cloud ou réseau virtuel, vous n’avez pas besoin de créer un point de terminaison accessible publiquement. Dans ce cas, vous pouvez passer à l’étape suivante. Pour plus d'informations, consultez [Scénarios de connexion](../articles/virtual-machines/virtual-machines-sql-server-connectivity.md#connection-scenarios).

1. Dans le portail de gestion Azure, cliquez sur **VIRTUAL MACHINES**.
	
2. Cliquez sur la machine virtuelle que vous venez de créer. Les informations relatives à la machine virtuelle sont affichées.
	
3. En haut de la page, sélectionnez la page **POINTS DE TERMINAISON**, puis en bas de la page, cliquez sur **AJOUTER**.
	
4. Dans la page **Ajouter un point de terminaison à la machine virtuelle**, cliquez sur **Ajouter un point de terminaison autonome**, puis sur la flèche Suivant.
	
5. Sur la page **Specify the details of the endpoint**, entrez les informations suivantes.

	- Dans la zone **NAME**, entrez un nom pour le point de terminaison.
	- Dans la zone **PROTOCOL**, sélectionnez **TCP**. Vous pouvez taper la valeur **57500** dans la zone **PORT PUBLIC**. De la même façon, vous pouvez indiquer le port d’écoute par défaut de SQL Server **1433** dans la zone **Port privé**. Notez que plusieurs organisations sélectionnent différents numéros de ports pour éviter les attaques de sécurité. 

6. Cliquez sur la coche pour continuer. Le point de terminaison est créé.

<!---HONumber=AcomDC_0107_2016-->