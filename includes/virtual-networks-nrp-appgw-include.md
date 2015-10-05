## Application Gateway

Application Gateway fournit une solution d'équilibrage de la charge HTTP gérée par Azure et basée sur l'équilibrage de la charge de couche 7. L'équilibrage de la charge de l'application permet l'utilisation de règles de routage pour le trafic réseau basé sur HTTP.

Les passerelles Application Gateway contiennent les ressources enfant suivantes :

- **Pool de serveurs principaux** : liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent appartenir au sous-réseau de réseau virtuel ou elles doivent être une adresse IP/VIP publique. 
- **Paramètres du pool de serveurs principaux** : chaque pool comporte des paramètres comme le port, le protocole et une affinité basée sur les cookies. Ces paramètres sont liés à un pool et sont appliqués à tous les serveurs du pool.
- **Port frontal** : il s'agit du port public ouvert sur la passerelle Application Gateway. Le trafic atteint ce port, puis il est redirigé vers l’un des serveurs principaux.
- **Écouteur** : l'écouteur a un port frontal, un protocole (Http ou Https, avec respect de la casse) et le nom du certificat SSL (en cas de configuration du déchargement SSL). 
- **Règle** : la règle lie l'écouteur et le pool de serveurs principaux et définit vers quel pool de serveurs principaux le trafic doit être dirigé quand il atteint un écouteur spécifique. Actuellement, seule la règle de base est prise en charge. La règle de base est la distribution de charge par tourniquet (round robin).

<!---HONumber=Sept15_HO4-->