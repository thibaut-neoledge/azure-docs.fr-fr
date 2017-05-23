Au cours de cette étape, vous allez tester l’écouteur de groupe de disponibilité à l’aide d’une application cliente s’exécutant sur le même réseau.

La connectivité client présente les exigences suivantes :

* Les connexions client à l’écouteur doivent provenir de machines résidant dans un service cloud autre que celui qui héberge les réplicas de disponibilité AlwaysOn.
* Si les réplicas Always On figurent dans des sous-réseaux distincts, les clients doivent spécifier *MultisubnetFailover=True* dans la chaîne de connexion. Cette condition entraîne des tentatives parallèles de connexion aux réplicas dans les différents sous-réseaux. Ce scénario inclut un déploiement de groupe de disponibilité AlwaysOn dans plusieurs régions.

Par exemple, vous pouvez vous connecter à l’écouteur à partir de l’une des machines virtuelles du même réseau virtuel Azure (mais n’hébergeant pas de réplica). Un moyen simple d’effectuer ce test consiste à connecter SQL Server Management Studio à l’écouteur de groupe de disponibilité. Une autre méthode simple consiste à exécuter [SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx), comme suit :

    sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [!NOTE]
> Si la valeur EndpointPort est définie sur *1433*, vous n’êtes pas tenu de la spécifier dans l’appel. L’appel précédent suppose également que la machine cliente est associée au même domaine et que l’appelant a reçu des autorisations pour la base de données à l’aide de l’authentification Windows.
> 
> 

Lorsque vous testez l’écouteur, procédez au basculement du groupe de disponibilité pour vérifier que les clients peuvent se connecter à l’écouteur d’un basculement à un autre.

