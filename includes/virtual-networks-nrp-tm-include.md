## Profil Traffic Manager
Traffic Manager et ses ressources de point de terminaison enfant activent la répartition de votre trafic vers les points de terminaison dans Azure et en dehors d'Azure. Cette répartition du trafic est régie par des stratégies. Traffic Manager permet également de surveiller l'intégrité des points de terminaison, ainsi que le trafic dévié de manière appropriée en fonction de l'intégrité d'un point de terminaison.

Les propriétés clés d'un profil Traffic Manager sont les suivantes :

- **Méthode de routage du trafic** : les valeurs possibles sont *Performances*, *Pondéré* et *Priorité*.
- **Configuration DNS** : nom de domaine complet du profil.
- **Protocole** : protocole de surveillance. Les valeurs possibles sont *HTTP* et *HTTPS*.
- **Port** : port de surveillance. 
- **Chemin d'accès** : chemin d'accès de surveillance.
- **Points de terminaison** : conteneur pour les ressources des points de terminaison.

### Point de terminaison 
Un point de terminaison est une ressource enfant d'un profil Traffic Manager. Il représente un service ou un point de terminaison web vers lequel le trafic est réparti en fonction de la stratégie configurée dans la ressource de profil Traffic Manager.

Les propriétés clé d'un point de terminaison sont les suivantes :
 
- **Type** : type du point de terminaison. Les valeurs possibles sont *Point de terminaison Azure*, *oint de terminaison externe* et *Point de terminaison imbriqué*. 
- **ID de ressource cible** : adresse IP publique d'un service ou d'un point de terminaison web. Ce peut être un point de terminaison Azure ou un point de terminaison externe.
- **Poids** : poids du point de terminaison utilisé dans la gestion du trafic. 
- **Priorité** : priorité du point de terminaison, utilisée pour définir une action de basculement. 

<!---HONumber=Oct15_HO3-->