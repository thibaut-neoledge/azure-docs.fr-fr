<BR>
## Forum Aux Questions 
### Combien coûtent les enregistrements DNS inversés ?
Ils sont gratuits ! Les enregistrements DNS inversés ou les requêtes n’entraînent aucun frais supplémentaire.
### Mes enregistrements DNS inversés seront-ils résolus à partir d'Internet ?
Oui. Dès que vous avez défini la propriété DNS inversée pour votre adresse IP publique, Azure gère toutes les délégations DNS et les zones DNS requises pour s’assurer que l’enregistrement DNS inversé soit résolu pour tous les utilisateurs d’Internet.
### Un enregistrement DNS inversé par défaut sera-t-il créé pour mes adresses IP publiques ?
Non. Le DNS inversé est une fonctionnalité optionnelle. Aucun enregistrement DNS inversé par défaut n’est créé si vous choisissez de ne pas les configurer.
### Quel est le format du nom de domaine complet (FQDN) ?
Les noms de domaine complets sont spécifiés dans l’ordre chronologique et doivent se terminer par un point (par exemple, « app1.contoso.com. »).
### Que se passe-t-il si les tests de validation pour le DNS inversé que j'ai spécifié échouent ?
Si les tests de validation du DNS inversé échouent, l’opération de gestion du service échoue. Corrigez la valeur DNS inversée et réessayez.
### Puis-je gérer le DNS inversé pour mon site web Azure ?
Le DNS inversé n’est pas pris en charge pour les sites web Azure. Le DNS inversé est pris en charge pour les machines virtuelles Azure.
### Puis-je configurer plusieurs enregistrements DNS inversés pour mon adresse IP publique ?
Non. Azure ne prend en charge qu’un seul enregistrement DNS inversé pour chaque adresse IP publique. Cependant, chaque adresse IP publique peut avoir son propre enregistrement DNS inversé.
### Puis-je configurer un enregistrement DNS inversé pour mon adresse IP publique sans spécifier de propriété DomainNameLabel ?
Non. Pour tirer parti des enregistrements DNS inversés pour les adresses IP publiques, vous devez spécifier la propriété DomainNameLabel.
### Puis-je héberger les zones ARPA pour mes adresses IP attribuées par Azure sur Azure DNS au sein de mon abonnement ou sur mes propres serveurs DNS faisant autorité ?
Non. Azure ne prend pas en charge la délégation des zones ARPA. Azure héberge les zones ARPA pour toutes les adresses IP disponibles et permet aux clients de créer des enregistrements DNS inversés au sein de ces zones ARPA.
### Puis-je héberger les zones ARPA pour mes blocs d’adresses IP attribués par mon fournisseur de services Internet sur Azure DNS ?
Non. Actuellement, Azure DNS ne prend pas en charge les enregistrements DNS inversés dans les zones DNS des clients.
### Puis-je envoyer des courriers électroniques à des domaines externes à partir d’Azure Compute Services ?
Non. Comme [ici](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/), les services Azure Compute Services ne gèrent pas l’envoi de messages à des domaines externes.

<!---HONumber=AcomDC_0907_2016-->