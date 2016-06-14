Pour ouvrir un port ou créer un point de terminaison dans Azure, vous créez un filtre réseau qui autorise le trafic vers le port choisi sur un sous-réseau ou une interface réseau de machine virtuelle. Ces filtres, qui contrôlent le trafic entrant et sortant, sont placés dans un groupe de sécurité réseau et associés à la ressource qui reçoit le trafic.

Nous allons utiliser un exemple courant de trafic web sur le port 80. Une fois que vous disposez d’une machine virtuelle configurée pour traiter les requêtes web sur le port TCP 80 standard (n’oubliez pas de démarrer les services appropriés et d’ouvrir toutes les règles de pare-feu du système d’exploitation sur la machine virtuelle), vous allez :

1. créer un groupe de sécurité réseau ;
2. créer une règle de trafic entrant autorisant le trafic avec :
  - la plage de ports de destination « 80 » ;
  - la plage de ports source « * » (autorisant n’importe quel port source) ;
  - une valeur de priorité inférieure à 65 500 (de sorte que la priorité soit supérieure à celle de la règle fourre-tout de refus de trafic entrant par défaut) ;
3. associer le groupe de sécurité réseau à l’interface réseau de machine virtuelle ou au sous-réseau.
    
Vous pouvez créer des configurations de réseau très complexes pour sécuriser votre environnement en utilisant des règles et groupes de sécurité réseau, plutôt qu’une ou deux règles autorisant le trafic HTTP ou la gestion à distance. Pour plus d’informations, reportez-vous à la section [Informations complémentaires](#more-information-on-network-security-groups) plus bas ou à l’article [Présentation du groupe de sécurité réseau](../articles/virtual-network/virtual-networks-nsg.md).

<!---HONumber=AcomDC_0601_2016-->