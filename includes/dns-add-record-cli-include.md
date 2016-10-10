#### Créer un jeu d’enregistrements avec un seul enregistrement

Pour créer un jeu d’enregistrements, utilisez `azure network dns record-set create`. Spécifiez le groupe de la ressource, le nom de la zone, le nom relatif du jeu d’enregistrements, le type d’enregistrement et la durée de vie (TTL).

	azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

Après avoir créé le jeu d’enregistrements A, ajoutez l’adresse IPv4 au jeu d’enregistrements avec `azure network dns record-set add-record` :

	azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1

#### Créer un jeu d’enregistrements AAAA avec un seul enregistrement

	azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

#### Créer un jeu d’enregistrements CNAME avec un seul enregistrement

Les enregistrements CNAME n’admettent qu’une seule valeur de chaîne.


	azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300

	azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"


#### Créer un jeu d’enregistrements MX avec un seul enregistrement

Dans cet exemple, nous utilisons le nom de jeu d’enregistrements « @ » pour créer l’enregistrement MX à l’apex de la zone (dans ce cas, « contoso.com »). Cela est courant pour les enregistrements MX.

	azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

	azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


#### Créer un jeu d’enregistrements NS avec un seul enregistrement

	azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300

	azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com"

#### Créer un jeu d’enregistrements PTR avec un seul enregistrement  
Dans ce cas, « my-arpa-zone.com » indique la zone ARPA représentant votre plage d’adresses IP. Chaque enregistrement PTR défini dans cette zone correspond à une adresse IP figurant dans cette plage d’adresses IP.

	azure network dns record-set add-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"   

#### Créer un jeu d’enregistrements SRV avec un seul enregistrement

Si vous créez un enregistrement SRV à la racine de la zone, vous pouvez spécifier « \_service » et « \_protocol » dans le nom de l’enregistrement. Il est inutile d’inclure « @ » dans le nom de l’enregistrement.


	azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com"

#### Créer un jeu d’enregistrements TXT avec un seul enregistrement

	azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record"

<!---HONumber=AcomDC_0928_2016-->