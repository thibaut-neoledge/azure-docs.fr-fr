Le DNS (Domain Name System) permet de localiser des ressources sur Internet. Par exemple, lorsque vous entrez une adresse d’application web dans votre navigateur ou cliquez sur un lien dans une page web, le DNS est utilisé pour traduire le domaine en adresse IP. L'adresse IP joue le même rôle qu'une adresse postale, tout en étant moins significative. Par exemple, il est bien plus facile de se souvenir d'un nom DNS tel que **contoso.com** que d'une adresse IP telle que 192.168.1.88 ou 2001:0:4137:1f67:24a2:3888:9cce:fea3.

Le système DNS est basé sur des *enregistrements*. Les enregistrements associent un *nom* spécifique, tel que **contoso.com**, à une adresse IP ou un autre nom DNS. Lorsqu'une application (par exemple, un navigateur Web) recherche un nom dans le DNS, elle trouve l'enregistrement et utilise sa cible comme adresse. Si la valeur ciblée correspond à une adresse IP, le navigateur l'utilise. Si la cible est un autre nom DNS, l'application doit à nouveau procéder à une résolution. En fin de compte, toute résolution de nom génère une adresse IP.

Lorsque vous créez une application web dans App Service, un nom DNS lui est automatiquement attribué. Ce nom a la forme suivante : **&lt;nomdevotreapplicationweb&gt;.azurewebsites.net**. Il existe également une adresse IP virtuelle à utiliser lors de la création des enregistrements DNS. Vous pouvez donc créer des enregistrements pointant vers **.azurewebsites.net** ou pointer vers l'adresse IP.

> [AZURE.NOTE]L’adresse IP de votre application web est modifiée si vous supprimez et recréez votre application web. Elle l’est également si vous définissez le mode de plan App Service sur **Gratuit** après l’avoir défini sur **De base**, **Partagé** ou **Standard**.

Il existe également plusieurs types d’enregistrements, chacun avec ses fonctions et limites, mais les applications web n’en utilisent que deux : les enregistrements *A* et *CNAME*.

###Enregistrement d'adresse (enregistrement A)

Un enregistrement A associe un domaine, tel que **contoso.com** ou **www.contoso.com**, *ou un nom de domaine générique* (par exemple : ***.contoso.com**) à une adresse IP. Dans le cas d’une application web dans App Service, il s’agit de l’adresse IP virtuelle du service ou d’une adresse IP spécifique que vous avez achetée pour votre application web.

Les principaux avantages d'un enregistrement A par rapport à un enregistrement CNAME sont les suivants :

* Vous pouvez mapper un domaine racine tel que **contoso.com** vers une adresse IP. De nombreux bureaux d'enregistrement autorisent cette action uniquement avec des enregistrements A.

* Vous pouvez avoir une entrée avec un caractère générique (***.contoso.com**, par exemple) qui gère les demandes pour plusieurs sous-domaines, tels que **mail.contoso.com**, **blogs.contoso.com** ou **www.contso.com**.

> [AZURE.NOTE]L’enregistrement A étant mappé à une adresse IP statique, les changements d’adresse IP de votre application web ne sont donc pas pris en compte automatiquement. Une adresse IP à utiliser avec les enregistrements A est fournie lorsque vous configurez les paramètres de nom de domaine personnalisé pour votre application web. Cette valeur est toutefois susceptible d’être modifiée si vous supprimez et recréez votre application web ou que vous faites de nouveau passer le mode de plan App Service en mode **Gratuit**.

###Enregistrement d'alias (enregistrement CNAME)

Un enregistrement CNAME mappe un nom DNS *spécifique*, tel que **mail.contoso.com** ou **www.contoso.com**, à un autre nom de domaine (canonique). Dans le cas d’App Service Web Apps, le nom de domaine canonique est le nom de domaine **&lt;nomdevotreapplicationweb>.azurewebsites.net** de votre application web. Une fois créé, l’enregistrement CNAME crée un alias pour le nom de domaine **&lt;nomdevotreapplicationweb>.azurewebsites.net**. L’entrée CNAME devient automatiquement l’adresse IP de votre nom de domaine **&lt;nomdevotreapplicationweb>.azurewebsites.net**. Ainsi, même si l’adresse IP de l’application web change, vous n’avez aucune action à effectuer.

> [AZURE.NOTE]Certains bureaux d’enregistrement de domaines n’autorisent le mappage de sous-domaines que si un enregistrement CNAME est utilisé (par exemple, **www.contoso.com**) et non un nom racine (tel que **contoso.com**). Pour plus d’informations sur les enregistrements CNAME, consultez la documentation fournie par votre bureau d’enregistrement, la <a href="http://en.wikipedia.org/wiki/CNAME_record">page Wikipédia sur l’enregistrement CNAME</a> ou le document <a href="http://tools.ietf.org/html/rfc1035">Noms de domaine IETF - Implémentation et spécification</a>.

###Spécifications DNS des applications web

Pour utiliser un enregistrement A avec Web Apps, vous devez d’abord créer l’un des enregistrements CNAME suivants :

* **Pour le domaine racine ou les sous-domaines génériques** : nom DNS d’**awverify** vers **awverify.&lt;nomdevotreapplicationweb&gt;.azurewebsites.net**.

* **Pour un sous-domaine spécifique** : nom DNS d’**awverify.&lt;sous-domaine>** vers **awverify.&lt;nomdevotreapplicationweb&gt;.azurewebsites.net**. Par exemple : **awverify.blogs** si l'enregistrement A est destiné à **blogs.contoso.com**.

Cet enregistrement CNAME permet de vérifier que vous détenez bien le domaine que vous essayez d'utiliser. Cette opération s’ajoute à la création d’un enregistrement A pointant vers l’adresse IP virtuelle de votre application web.

Pour connaître l’adresse IP ainsi que les noms **awverify** et **.azurewebsites.net** de votre application web, procédez comme suit :

1. Dans votre navigateur, ouvrez le [portail Azure](https://portal.azure.com).

2. Dans le panneau **Web Apps**, cliquez sur le nom de votre application web et sélectionnez **Tous les paramètres**, puis **Domaines personnalisés et SSL** au bas de la page.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

	> [AZURE.NOTE]Si **Gérer les domaines** n’est pas activé, vous utilisez une application web **Gratuite**. Vous ne pouvez pas utiliser de noms de domaines personnalisés avec une application web **Gratuite**. Vous devez donc mettre à niveau le plan App Service vers le mode **Partagé**, **De base** ou **Standard**. Pour plus d’informations sur les modes de plan App Service, notamment sur la modification du mode de votre application web, voir [Mise à l’échelle des applications web](../articles/web-sites-scale.md).

6. Dans la boîte de dialogue **MANAGE CUSTOM DOMAINS** sont affichés le nom de domaine **.azurewebsites.net**, les informations **awverify** et l'adresse IP virtuelle. Enregistrez ces informations, car elles serviront lors de la création d'enregistrements DNS.

	![](./media/custom-dns-web-site/managecustomdomains.png)


<!--HONumber=54-->