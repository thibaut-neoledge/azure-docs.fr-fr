<BR>

## Forum Aux Questions
### Combien coûtent les enregistrements DNS inversés ?
Ils sont gratuits ! Les enregistrements DNS inversés ou les requêtes n’entraînent aucun frais supplémentaire.

### Mes enregistrements DNS inversés seront-ils résolus à partir d'Internet ?
Oui. Dès que vous avez défini la propriété DNS inversée pour votre service cloud, Azure gère toutes les délégations DNS et les zones DNS requises pour s’assurer que l’enregistrement DNS inversé soit résolu pour tous les utilisateurs d’Internet.

### Un enregistrement DNS inversé par défaut sera-t-il créé pour mes services cloud ?
Non. Le DNS inversé est une fonctionnalité optionnelle. Aucun enregistrement DNS inversé par défaut n’est créé si vous choisissez de ne pas les configurer.

### Quel est le format du nom de domaine complet (FQDN) ?
Les noms de domaine complets sont spécifiés dans l’ordre chronologique et doivent se terminer par un point (par exemple, « app1.contoso.com. »).

### Que se passe-t-il si les tests de validation pour le DNS inversé que j'ai spécifié échouent ?
Si les tests de validation du DNS inversé échouent, l’opération de gestion du service échoue. Corrigez la valeur DNS inversée et réessayez.

### Puis-je gérer le DNS inversé pour mon site web Azure ?
Le DNS inversé n’est pas pris en charge pour les sites web Azure. Le DNS inversé est pris en charge pour les rôles PaaS Azure et les machines virtuelles IaaS.

### Puis-je configurer plusieurs enregistrements DNS inversés pour mon service cloud ?
Non. Azure ne prend en charge qu’un seul enregistrement DNS inversé pour chaque service cloud Azure. Cependant, chaque service cloud Azure peut avoir son propre enregistrement DNS inversé.

### Puis-je envoyer des courriers électroniques à des domaines externes à partir d’Azure Compute Services ?
Non. Comme [ici](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/), les services Azure Compute Services ne gèrent pas l’envoi de messages à des domaines externes.

<!---HONumber=AcomDC_0907_2016-->