
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>Forum Aux Questions - DNS inversé pour votre adresse IP attribuée à Azure

### <a name="how-much-do-reverse-dns-records-cost"></a>Combien coûtent les enregistrements DNS inversés ?

Ils sont gratuits.  Les enregistrements DNS inversés ou les requêtes n’entraînent aucun frais supplémentaire.

### <a name="will-the-reverse-dns-records-for-my-azure-assigned-public-ip-address-resolve-from-the-internet"></a>Les enregistrements DNS inversés pour mon adresse IP publique affectée à Azure vont-ils se résoudre à partir d’Internet ?

Oui. Dès que vous avez défini la propriété DNS inversée pour votre adresse IP publique, Azure gère toutes les délégations DNS et les zones DNS requises pour s’assurer que l’enregistrement DNS inversé soit résolu pour tous les utilisateurs d’Internet.

### <a name="will-a-default-reverse-dns-record-be-created-for-my-public-ip-addresses"></a>Un enregistrement DNS inversé par défaut sera-t-il créé pour mes adresses IP publiques ?

Non. Le DNS inversé est une fonctionnalité optionnelle. Aucun enregistrement DNS inversé par défaut n’est créé si vous choisissez de ne pas les configurer.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Quel est le format du nom de domaine complet (FQDN) ?

Les noms de domaine complets sont spécifiés dans l’ordre chronologique et doivent se terminer par un point (par exemple, « app1.contoso.com. »).

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>Que se passe-t-il si les tests de validation pour le DNS inversé que j’ai spécifié échouent ?

Si les tests de validation du DNS inversé échouent, l’opération de gestion du service échoue. Corrigez la valeur DNS inversée et réessayez.

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>Puis-je gérer le DNS inversé pour mon site web Azure ?

Le DNS inversé n’est pas pris en charge pour les sites web Azure. Le DNS inversé est pris en charge pour les machines virtuelles Azure.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-public-ip-address"></a>Puis-je configurer plusieurs enregistrements DNS inversés pour mon adresse IP publique ?

Non. Azure ne prend en charge qu’un seul enregistrement DNS inversé pour chaque adresse IP publique. Cependant, chaque adresse IP publique peut avoir son propre enregistrement DNS inversé.

### <a name="can-i-configure-reverse-dns-records-for-an-ipv6-public-ip-address"></a>Puis-je configurer des enregistrements DNS inversés pour une adresse IP publique IPv6 ?

Non.  Actuellement, les enregistrements DNS inversés sont uniquement pris en charge pour les adresses IP publiques IPv4.

### <a name="can-i-configure-a-reverse-dns-record-for-my-public-ip-address-without-having-a-domainnamelabel-specified"></a>Puis-je configurer un enregistrement DNS inversé pour mon adresse IP publique sans spécifier de propriété DomainNameLabel ?

Non. Pour tirer parti des enregistrements DNS inversés pour les adresses IP publiques, vous devez spécifier la propriété DomainNameLabel.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Puis-je envoyer des courriers électroniques à des domaines externes à partir d’Azure Compute Services ?

Non. [Les services Azure Compute Services ne prennent pas en charge l’envoi de messages à des domaines externes](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).


<!--HONumber=Nov16_HO3-->


