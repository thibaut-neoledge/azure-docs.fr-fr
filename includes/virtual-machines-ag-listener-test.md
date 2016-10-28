Au cours de cette étape, vous allez tester l'écouteur du groupe de disponibilité à l'aide d'une application cliente s'exécutant sur le même réseau.

Pour la connectivité client, veuillez noter les exigences suivantes :

- Les connexions client à l'écouteur doivent provenir de machines qui résident dans un service cloud différent de celui qui héberge les réplicas de disponibilité AlwaysOn.

- Si les réplicas AlwaysOn se situent dans des sous-réseaux différents, les clients doivent spécifier « MultisubnetFailover = True » dans la chaîne de connexion. Cela entraîne des tentatives parallèles de connexion aux réplicas dans les différents sous-réseaux. Notez que ce scénario inclut un déploiement de groupe de disponibilité AlwaysOn sur plusieurs régions.

Par exemple, vous pouvez vous connecter à l'écouteur à partir de l'une des machines virtuelles qui se situent dans le même réseau virtuel Azure (mais n'hébergeant pas de réplica). Un moyen simple d'effectuer ce test consiste à connecter SSMS à l'écouteur du groupe de disponibilité. Une autre méthode simple consiste à exécuter [SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx) comme suit :

	sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [AZURE.NOTE]Si la valeur EndpointPort est 1433, il n'est pas nécessaire de la spécifier dans l'appel. L'appel précédent suppose également que la machine cliente est associée au même domaine et que l'appelant a reçu des autorisations pour la base de données à l'aide de l'authentification Windows.

Lorsque vous testez l'écouteur, procédez au basculement du groupe de disponibilité pour vérifier que les clients peuvent se connecter à l'écouteur d'un basculement à un autre.

<!---HONumber=Oct15_HO3-->