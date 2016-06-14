## À propos des enregistrements

Chaque enregistrement DNS a un nom et un type. Les enregistrements sont organisés selon différents types, en fonction des données qu’ils contiennent. Le type le plus courant est un enregistrement « A » qui associe un nom à une adresse IPv4. Un autre type est un enregistrement « MX », qui associe un nom à un serveur de messagerie.

Azure DNS prend en charge tous les types d’enregistrement DNS courants, notamment A, AAAA, CNAME, MX, NS, SOA, SRV et TXT. Les jeux d’enregistrements SOA sont créés automatiquement avec chaque zone. Ils ne peuvent pas être créés séparément. Notez que les enregistrements SPF doivent être créés à l’aide du type d’enregistrement TXT. Pour plus d’informations, voir [cette page](http://tools.ietf.org/html/rfc7208#section-3.1).

Dans Azure DNS, les enregistrements sont spécifiés à l’aide de noms relatifs. Le nom de domaine complet inclut le nom de la zone, contrairement au nom relatif. Par exemple, le nom d’enregistrement relatif « www » dans la zone « contoso.com » crée le nom d’enregistrement complet www.contoso.com.

## À propos des jeux d’enregistrements

Vous devez parfois créer plusieurs enregistrements DNS avec un nom et un type donnés. Par exemple, supposons que le site web « www.contoso.com » est hébergé sur deux adresses IP différentes. Ce site web requiert deux enregistrements A différents, à savoir un pour chaque adresse IP. Voici un exemple de jeu d’enregistrements :

	www.contoso.com.		3600	IN	A	134.170.185.46
	www.contoso.com.		3600	IN	A	134.170.188.221

Azure DNS gère les enregistrements DNS à l’aide de jeux d’enregistrements. Un jeu d’enregistrements est une collection d’enregistrements DNS dans une zone ayant le même nom et le même type. La plupart des jeux d’enregistrements contiennent un enregistrement unique, mais les exemples comme celui-ci, dans lequel un jeu d’enregistrements contient plusieurs enregistrements, sont relativement courants.

Les jeux d’enregistrements SOA et CNAME sont des exceptions. Les normes DNS n’autorisent pas plusieurs enregistrements avec le même nom pour ces types.

La durée de vie (TTL) spécifie la durée pendant laquelle chaque enregistrement est mis en cache par les clients avant d’être réinterrogé. Dans cet exemple, la durée de vie est de 3600 secondes ou 1 heure. La durée de vie est spécifiée pour le jeu d’enregistrements, pas pour chaque enregistrement. La même valeur est donc utilisée pour tous les enregistrements au sein de ce jeu d’enregistrements.

#### Jeux d’enregistrements génériques

Azure DNS prend en charge les [enregistrements génériques](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Ces derniers sont retournés pour toute requête avec un nom correspondant (à moins qu’une correspondance plus proche provienne d'un jeu d'enregistrements non génériques). Les jeux d'enregistrements génériques sont pris en charge pour tous les types d'enregistrements, hormis NS et SOA.

Pour créer un jeu d’enregistrements génériques, utilisez le nom de jeu d’enregistrements « * ». Vous pouvez également utiliser un nom avec le caractère « * », par exemple, « *.foo ».

#### Jeux d’enregistrements CNAME

Les jeux d’enregistrements CNAME ne peuvent pas coexister avec d’autres jeux d’enregistrements portant le même nom. Par exemple, vous ne pouvez pas créer un jeu d’enregistrements CNAME avec le nom relatif « www » et un enregistrement A avec le nom relatif « www » en même temps. Étant donné que l’extrémité de la zone (nom = « @ ») contient toujours les jeux d’enregistrements NS et SOA créés lors de la création de la zone, vous ne pouvez pas créer un jeu d’enregistrements CNAME au niveau de l’extrémité de la zone. Ces contraintes sont dues aux normes DNS. Il ne s’agit pas de limites d’Azure DNS.

<!---HONumber=AcomDC_0601_2016-->