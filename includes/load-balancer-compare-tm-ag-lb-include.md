## Différences d’équilibrage de charge

Il existe différentes options pour répartir le trafic réseau à l’aide de Microsoft Azure. Ces options fonctionnent différemment, car elle disposent d’un ensemble de fonctionnalités différent et prennent en charge différents scénarios. Elles peuvent être utilisées de manière isolée ou en les combinant.

- L’Équilibrage de charge Azure fonctionne au niveau de la couche réseau (niveau 4 dans la pile de référence de réseau OSI). Il fournit une distribution du trafic au niveau du réseau entre les instances d’une application s’exécutant dans le même centre de données Azure.

- Application Gateway fonctionne au niveau de la couche d’application (niveau 7 dans la pile de référence de réseau OSI). Il agit comme un service de proxy inversé, qui met fin à la connexion du client et transfère les requêtes vers les points de terminaison principaux.

- 	Traffic Manager fonctionne au niveau du DNS. Il utilise les réponses DNS pour diriger le trafic de l’utilisateur final vers les points de terminaison globalement distribués. Les clients se connectent ensuite à ces points de terminaison directement. Le tableau suivant récapitule les fonctionnalités offertes par chaque service :

|Équilibrage de charge Azure |	Application Gateway | Traffic Manager |
|---|---|---|
|Technology| Niveau Réseau (niveau 4) | Niveau Application (niveau 7) |	Niveau DNS |
| Protocoles d’applications pris en charge |	Quelconque | HTTP et HTTPS | 	N’importe quelle option (un point de terminaison HTTP/S est requis pour l’analyse du point de terminaison) |
| Points de terminaison | Instances de rôle de services cloud et de machines virtuelles Azure | Toute adresse IP interne Azure ou adresse IP Internet publique | Machines virtuelles Azure, Cloud Services, Azure Web Apps et points de terminaison externes |
| Prise en charge de réseau virtuel | Peut être utilisé pour les applications internes (réseau virtuel) et les applications accessibles via Internet | Peut être utilisé pour les applications internes (réseau virtuel) et les applications accessibles via Internet |	Prend uniquement en charge les applications accessibles via Internet |
Surveillance de point de terminaison | prise en charge via les sondes | prise en charge via les sondes | prise en charge via HTTP/HTTPS GET | 
<BR> L’Équilibrage de charge Azure et Application Gateway dirigent le trafic réseau vers des points de terminaison, mais ils ont des scénarios d'utilisation différents concernant le trafic à gérer. Le tableau ci-dessous vous aide à comprendre la différence entre les deux équilibreurs de charge :


| Type | Équilibrage de charge Azure | Application Gateway |
|---|---|---|
| Protocoles | UDP/TCP | HTTP / HTTPS |
| Réservation IP | Pris en charge | Non pris en charge | 
| Mode d’équilibrage de charge | 5 tuples (IP source, port source, adresse IP de destination, port de destination, type de protocole) | CookieBasedAffinity = false,rules = basic (Round-Robin) | 
| Mode d’équilibrage de charge (adresse IP source / sessions rémanentes) | 2 tuples (adresse IP source et adresse IP de destination), 3 tuples (adresse IP source, adresse IP de destination et port). Possibilité de mise à l'échelle vers le haut ou vers le bas en fonction du nombre de machines virtuelles | CookieBasedAffinity = true,rules = basic (Roud-Robin) pour les nouvelles connexions. |
| sondes d’intégrité | Par défaut : intervalle de sondage - 15 secondes. Mise hors service : 2 échecs continus. Prise en charge des sondes définies par l’utilisateur | Intervalle de sondage inactif - 30 secondes. Mise hors service après 5 échecs consécutifs trafic sur le trafic en direct ou un seul échec de sonde en mode inactif. Prise en charge des sondes définies par l’utilisateur | 
| Déchargement SSL | non pris en charge | pris en charge | 


  

<!---HONumber=AcomDC_0323_2016-->