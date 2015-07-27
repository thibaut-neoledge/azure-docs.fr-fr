Le DNS (Domain Name System) permet de localiser des éléments sur Internet. Par exemple, lorsque vous entrez une adresse dans votre navigateur ou cliquez sur un lien d'une page Web, le DNS est utilisé pour traduire le domaine en adresse IP. L'adresse IP joue le même rôle qu'une adresse postale, tout en étant moins significative. Par exemple, il est bien plus facile de se souvenir d'un nom DNS tel que **contoso.com** que d'une adresse IP telle que 192.168.1.88 ou 2001:0:4137:1f67:24a2:3888:9cce:fea3.

Le système DNS est basé sur des *enregistrements*. Les enregistrements associent un *nom* spécifique, tel que **contoso.com**, à une adresse IP ou un autre nom DNS. Lorsqu'une application (par exemple, un navigateur Web) recherche un nom dans le DNS, elle trouve l'enregistrement et utilise sa cible comme adresse. Si la valeur ciblée correspond à une adresse IP, le navigateur l'utilise. Si la cible est un autre nom DNS, l'application doit à nouveau procéder à une résolution. En fin de compte, toute résolution de nom génère une adresse IP.

Lorsque vous créez un site web Azure, un nom DNS lui est automatiquement attribué. Ce nom se présente comme suit : **&lt;nomdevotresite&gt;.azurewebsites.net**. Lorsque vous ajoutez votre site web en tant que point de terminaison Azure Traffic Manager, il est accessible via le domaine **&lt;votreprofiltrafficmanager&gt;.trafficmanager.net**.

> [AZURE.NOTE]Si votre site web est configuré en tant que point de terminaison Traffic Manager, vous utiliserez l’adresse **.trafficmanager.net** lors de la création d’enregistrements DNS.

> Seuls les enregistrements CNAME peuvent être utilisés avec Traffic Manager.

Il existe de nombreux types d'enregistrements, chacun présentant ses propres fonctions et limites, mais pour les sites web configurés en tant que points de terminaison Traffic Manager, seul le type d'enregistrement *CNAME* nous intéresse.

###Enregistrement CNAME ou d'alias

Un enregistrement CNAME mappe un nom DNS *spécifique*, tel que **mail.contoso.com** ou **www.contoso.com**, à un autre nom de domaine (canonique). Dans le cas des sites web Azure utilisant Traffic Manager, le nom de domaine canonique correspond au nom de domaine **&lt;monapp>.trafficmanager.net** de votre profil Traffic Manager. Une fois créé, l’enregistrement CNAME crée un alias pour le nom de domaine **&lt;monapp>.trafficmanager.net**. L’entrée CNAME devient automatiquement l’adresse IP de votre nom de domaine **&lt;monapp>.trafficmanager.net**. Ainsi, même si l’adresse IP du site web change, vous n’avez aucune action à effectuer.

Lorsque le trafic parvient à Traffic Manager, il est acheminé vers votre site web à l'aide de la méthode d'équilibrage de charge pour laquelle il est configuré. Cela se fait de manière totalement transparente pour les visiteurs de votre site web. Ils ne voient que le nom de domaine personnalisé dans votre navigateur.

> [AZURE.NOTE]Certains bureaux d’enregistrement de domaines n’autorisent le mappage de sous-domaines que si un enregistrement CNAME est utilisé (par exemple, **www.contoso.com**) et non un nom racine (tel que **contoso.com**). Pour plus d’informations sur les enregistrements CNAME, consultez la documentation fournie par votre bureau d’enregistrement, la <a href="http://en.wikipedia.org/wiki/CNAME_record">page Wikipédia sur l’enregistrement CNAME</a> ou le document <a href="http://tools.ietf.org/html/rfc1035">Noms de domaine IETF - Implémentation et spécification</a>.

<!---HONumber=July15_HO3-->