- Les réseaux virtuels peuvent être situés dans des régions (emplacements) identiques ou différentes.

- Un service cloud ou un point de terminaison d’équilibrage de charge NE PEUT PAS s’étendre sur différents réseaux virtuels, même si ces derniers sont interconnectés.

- L’interconnexion de plusieurs réseaux virtuels Azure ne requiert pas de passerelle VPN locale, sauf si la connectivité entre différents locaux est requise.

- La connexion de réseau virtuel à réseau virtuel prend en charge la connexion de réseaux virtuels. Elle ne prend pas en charge la connexion de machines virtuelles ou de services cloud qui ne sont PAS situés dans un réseau virtuel.

- La connexion de réseau virtuel à réseau virtuel nécessite des passerelles VPN Azure avec VPN de type RouteBased (précédemment Dynamic Routing, routage dynamique).

- La connectivité de réseau virtuel peut être utilisée simultanément avec des VPN multisites, avec un maximum de 10 tunnels VPN (passerelles par défaut/standard) ou 30 tunnels VPN (passerelles à hautes performances) pour une passerelle VPN de réseau virtuel se connectant à d’autres réseaux virtuels ou à des sites locaux.

- Les espaces d’adressage des réseaux virtuels et sur les sites de réseau locaux ne doivent pas se chevaucher. Le chevauchement des espaces d’adressage entraîne l’échec des connexions de réseau virtuel à réseau virtuel.

- Les tunnels redondants entre deux réseaux virtuels ne sont pas pris en charge.

- Tous les tunnels VPN du réseau virtuel partagent la bande passante disponible sur la passerelle VPN Azure, ainsi que le même contrat SLA concernant le temps d’activité des passerelles VPN dans Azure.

- Le trafic de réseau virtuel à réseau virtuel transite sur le réseau Microsoft, et non sur Internet.

- Le trafic de réseau virtuel à réseau virtuel au sein d’une même région est gratuit dans les deux sens. Le trafic de réseau virtuel à réseau virtuel de sortie entre différentes régions est facturé au tarif du transfert de données sortantes entre réseaux virtuels en fonction des régions sources. Pour plus d’informations, consultez la [page sur la tarification](https://azure.microsoft.com/pricing/details/vpn-gateway/).

<!---HONumber=AcomDC_0720_2016-->