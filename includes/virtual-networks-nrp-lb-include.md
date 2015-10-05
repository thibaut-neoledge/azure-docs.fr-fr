## Équilibrage de charge
Un équilibrage de charge est utilisé lorsque vous voulez étendre vos applications. Les scénarios de déploiement classiques impliquent des applications s'exécutant sur plusieurs instances de machine virtuelle. Les instances de machine virtuelle sont pilotées par un équilibrage de charge qui permet de répartir le trafic réseau entre les différentes instances.

![Cartes d'interface réseau sur une seule machine virtuelle](./media/resource-groups-networking/Figure5.png)

Les équilibrages de charge contiennent les ressources enfants suivantes :

- **Configuration d'adresses IP frontales** : un équilibrage de charge peut inclure une ou plusieurs adresses IP frontales, également appelées « adresses IP virtuelles ». Ces adresses IP servent d'entrée pour le trafic. 
- **Pool d'adresses principal** : il s'agit des adresses IP associées aux cartes réseau des machines virtuelles vers lesquelles la charge sera distribuée.
- **Règles d'équilibrage de charge** : une propriété de règle mappe une combinaison d'adresse IP et de port frontaux donnée à un ensemble de combinaisons d'adresse IP et de port principaux. Avec une seule définition d'une ressource d'équilibrage de charge, vous pouvez définir plusieurs règles d'équilibrage de charge, chaque règle reflétant une combinaison d'une adresse IP et d'un port frontaux d'une part, et d'une adresse IP et d'un port principaux d'autre part, associés à des machines virtuelles. 
- **Sondes** : les sondes vous permettent d'effectuer le suivi de l'intégrité des instances de machine virtuelle. En cas d'échec d'une sonde d'intégrité, l'instance de machine virtuelle est automatiquement mise hors service.
- **Règles NAT de trafic entrant** : règles NAT définissant le trafic entrant transitant via l'adresse IP frontale et distribué à l'adresse IP principale.

<!---HONumber=Sept15_HO4-->