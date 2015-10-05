## Groupe de sécurité réseau
Une ressource de groupe de sécurité réseau permet de créer des limites de sécurité pour les charges de travail, en implémentant des règles d'autorisation et de refus. Ces règles peuvent être appliquées au niveau de la carte d'interface réseau (niveau des instances de machine virtuelle) ou au niveau du sous-réseau (groupe de machines virtuelles).

Les propriétés clés d'une ressource de groupe de sécurité réseau sont les suivantes :

- **Règle de sécurité** : plusieurs règles de sécurité peuvent être définies pour un groupe de sécurité réseau. Chaque règle peut autoriser ou refuser différents types de trafic.

### Règle de sécurité
Une règle de sécurité est une ressource enfant d'un groupe de sécurité réseau.

Les propriétés clés d'une règle de sécurité sont les suivantes :

- **Protocole** : protocole réseau auquel cette règle s'applique.
- **Plage du port source** : port source, ou plage comprise entre 0 et 65535. Un caractère générique peut être utilisé pour faire correspondre tous les ports. 
- **Plage du port de destination** : port de destination, ou plage comprise entre 0 et 65535. Un caractère générique peut être utilisé pour faire correspondre tous les ports.
- **Préfixe d'adresse source** : plage d'adresses IP sources. 
- **Préfixe d'adresse de destination** : plage d'adresses IP de destination.
- **Accès** : *Autoriser* ou *Refuser* le trafic.
- **Priorité** : valeur comprise entre 100 et 4096. Le numéro de priorité doit être unique pour chaque règle de la collection de règles de sécurité. Plus le numéro de priorité est faible, plus la priorité de la règle est élevée.
- **Sens** : spécifie si la règle sera appliquée au trafic dans le sens *entrant* ou *sortant*. 

<!---HONumber=Sept15_HO4-->