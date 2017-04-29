## <a name="what-is-reverse-dns"></a>Qu’est-ce que le DNS inversé ?

Les enregistrements DNS traditionnels permettent d’effectuer des mappages à partir d’un nom de DNS (par exemple, « www.contoso.com ») vers une adresse IP (par exemple, 64.4.6.100).  Le DNS inversé permet la traduction d’une adresse IP (64.4.6.100) en nom (« www.contoso.com »).

Les enregistrements DNS inversés sont utilisés dans de nombreuses situations. Par exemple, les enregistrements DNS inversés sont largement utilisés dans la lutte contre les messages indésirables en vérifiant l’expéditeur d’un message électronique.  Le serveur de messagerie de réception récupère l’enregistrement DNS inversé de l’adresse IP du serveur d’envoi et vérifie si cet hôte est autorisé à envoyer un message électronique à partir du domaine d’origine. (Notez toutefois que [les services Azure Compute Services ne prennent pas en charge l’envoi de messages à des domaines externes](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).)

## <a name="how-reverse-dns-works"></a>Fonctionnement du DNS inversé

Les enregistrements DNS inversés sont hébergés dans des zones DNS spéciales, appelées zones « ARPA ».  Ces zones forment une hiérarchie DNS distincte en parallèle avec la hiérarchie classique hébergeant des domaines tels que « contoso.com ».

Par exemple, l’enregistrement DNS « www.contoso.com » est implémenté à l’aide d’un enregistrement DNS « A » avec le nom « www » dans la zone « contoso.com ».  Cet enregistrement A pointe vers l’adresse IP correspondante, dans ce cas 64.4.6.100.  La recherche inversée est implémentée séparément, à l’aide d’un enregistrement « PTR » nommé « 100 » dans la zone « 6.4.64.in-addr.arpa » (notez que les adresses IP sont inversées dans les zones ARPA).  Cet enregistrement PTR, s’il a été configuré correctement, pointe vers le nom « www.contoso.com ».

Lorsqu’une organisation est affectée à un bloc d’adresses IP, elle acquiert également le droit de gestion de la zone ARPA correspondante. Les zones ARPA qui correspondent aux blocs d’adresses IP utilisés par Azure sont hébergées et gérées par Microsoft. Votre fournisseur de services Internet peut héberger la zone ARPA pour vos propres adresses IP, ou peut vous autoriser à héberger la zone ARPA dans le service DNS de votre choix, par exemple DNS Azure.

> [!NOTE]
> Les recherches DNS directes et inversées sont implémentées dans des hiérarchies DNS distinctes, en parallèle. La recherche inversée pour « www.contoso.com » n’est **pas** hébergée dans la zone « contoso.com », mais dans la zone ARPA pour le bloc d’adresses IP correspondant.

Pour plus d’informations sur le DNS inversé, consultez [Reverse DNS Lookup](http://en.wikipedia.org/wiki/Reverse_DNS_lookup) (Recherche DNS inversée).

## <a name="azure-support-for-reverse-dns"></a>Prise en charge Azure pour le DNS inversé

Azure prend en charge deux scénarios distincts relatifs au DNS inversé :

1. L’hébergement de la zone ARPA qui correspond à votre bloc d’adresses IP.
2. La configuration de l’enregistrement DNS inversé pour l’adresse IP affectée à votre service Azure.

Pour prendre en charge le premier scénario, DNS Azure peut être utilisé pour héberger vos zones ARPA et gérer les enregistrements PTR pour chaque recherche DNS inversée.  Le processus de création de la zone ARPA, de configuration de la délégation et de configuration des enregistrements PTR est identique à celui des zones DNS standard.  Les seules différences sont que la délégation doit être configurée via votre fournisseur de services Internet plutôt que via votre bureau d’enregistrement de DNS, et seul le type d’enregistrement PTR doit être utilisé.

Pour prendre en charge le second scénario, Azure vous autorise à configurer la recherche inversée pour les adresses IP allouées à votre service.  Cette recherche inversée est configurée par Azure comme un enregistrement PTR dans la zone ARPA correspondante.  Ces zones ARPA, qui correspondent aux blocs d’adresses IP utilisés par Azure, sont hébergées par Microsoft. **Le reste de cet article décrit ce scénario en détail.**
