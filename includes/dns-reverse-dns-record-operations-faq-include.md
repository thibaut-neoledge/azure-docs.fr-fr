<BR>
## Forum Aux Questions 
### Combien coûtent les enregistrements DNS inversés ?
Ils sont gratuits ! Les enregistrements DNS inversés ou les requêtes n’entraînent aucun frais supplémentaire.
### Mes enregistrements DNS inversés seront-ils résolus à partir d'Internet ?
Oui. Dès que vous avez défini la propriété DNS inversée pour votre service cloud, Azure gère toutes les délégations DNS et les zones DNS requises pour s’assurer que l’enregistrement DNS inversé soit résolu pour tous les utilisateurs d’Internet.
### Un enregistrement DNS inversé par défaut sera-t-il créé pour mes services cloud ?
Non. Le DNS inversé est une fonctionnalité optionnelle. Aucun enregistrement DNS inversé par défaut ne sera créé si vous choisissez de ne pas en configurer un. Quel est le format du nom de domaine complet (FQDN) ? Les noms de domaine complets sont spécifiés dans l’ordre chronologique et doivent se terminer par un point (par exemple, « app1.contoso.com. »).
### Que se passe-t-il si les tests de validation pour le DNS inversé que j'ai spécifié échouent ?
Si les tests de validation du DNS inversé échouent, l’opération de gestion du service échoue. Corrigez la valeur DNS inversée et réessayez.
### Puis-je gérer le DNS inversé pour mon site web Azure ?
Le DNS inversé n’est pas pris en charge pour les sites web Azure. Le DNS inversé est pris en charge pour les rôles PaaS Azure et les machines virtuelles IaaS.
### Puis-je configurer plusieurs enregistrements DNS inversés pour mon service cloud ?
Non. Azure ne prend en charge qu’un seul enregistrement DNS inversé pour chaque service cloud Azure. Cependant, chaque service cloud Azure peut avoir son propre enregistrement DNS inversé.
### Puis-je héberger les zones ARPA pour mes adresses IP attribuées par Azure sur Azure DNS au sein de mon abonnement ou sur mes propres serveurs DNS faisant autorité ?
Non. Azure ne prend pas en charge la délégation des zones ARPA. Azure héberge les zones ARPA pour toutes les adresses IP disponibles et permet aux clients de créer des enregistrements DNS inversés au sein de ces zones ARPA.
### Puis-je héberger les zones ARPA pour mes blocs d’adresses IP attribués par mon fournisseur de services Internet sur Azure DNS ?
Non. Actuellement, Azure DNS ne prend pas en charge les enregistrements DNS inversés dans les zones DNS des clients.

<!---HONumber=AcomDC_0309_2016-->