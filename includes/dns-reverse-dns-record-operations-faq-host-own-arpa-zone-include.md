
## <a name="faq---hosting-your-arpa-zone-in-azure-dns"></a>Forum Aux Questions - Hébergement de votre zone ARPA dans DNS Azure

### <a name="can-i-host-arpa-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Puis-je héberger les zones ARPA pour mes blocs d’adresses IP attribués par mon fournisseur de services Internet sur Azure DNS ?

Oui. L’hébergement des zones ARPA dans vos propres plages d’adresses IP dans DNS Azure est entièrement pris en charge.

Il vous suffit de [créer la zone dans DNS Azure ](../articles/dns/dns-getstarted-create-dnszone.md), puis de demander à votre fournisseur de services Internet de [déléguer la zone](../articles/dns/dns-domain-delegation.md).  Vous pouvez ensuite gérer les enregistrements PTR pour chaque recherche inversée de la même façon que d’autres types d’enregistrement.

Vous pouvez également [importer une zone de recherche inversée existante à l’aide de l’interface de ligne de commande Azure](../articles/dns/dns-import-export.md).

### <a name="how-much-does-hosting-my-arpa-zone-cost"></a>Combien coûte l’hébergement de ma zone ARPA ?

L’hébergement de la zone ARPA pour votre bloc d’adresses IP affecté au fournisseur de services Internet dans DNS Azure est facturé au [tarif DNS Azure standard](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-arpa-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Puis-je héberger des zones ARPA pour les adresses IPv4 et IPv6 dans DNS Azure ?

Oui.
