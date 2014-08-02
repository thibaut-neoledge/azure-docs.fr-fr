

Le DNS (Domain Name System) permet de localiser des éléments sur Internet. Par exemple, lorsque vous entrez une adresse dans votre navigateur ou cliquez sur un lien d'une page Web, le DNS est utilisé pour traduire le domaine en adresse IP. L'adresse IP joue le même rôle qu'une adresse postale, tout en étant moins significative. Par exemple, il est bien plus facile de se souvenir d'un nom DNS tel que **contoso.com** que d'une adresse IP telle que 192.168.1.88 ou 2001:0:4137:1f67:24a2:3888:9cce:fea3.

Le système DNS est basé sur des *enregistrements*. Les enregistrements associent un *nom* spécifique, tel que **contoso.com**, à une adresse IP ou un autre nom DNS. Lorsqu'une application (par exemple, un navigateur Web) recherche un nom dans le DNS, elle trouve l'enregistrement et utilise sa cible comme adresse. Si la valeur ciblée correspond à une adresse IP, le navigateur l'utilise. Si la cible est un autre nom DNS, l'application doit à nouveau procéder à une résolution. En fin de compte, toute résolution de nom génère une adresse IP.

Lorsque vous créez un site Web Azure, un nom DNS lui est automatiquement attribué. Ce nom se présente comme suit : **&lt;nomdevotresite\>.azurewebsites.net**. Il existe également une adresse IP virtuelle à utiliser lors de la création des enregistrements DNS. Vous pouvez donc créer un enregistrement pointant vers **.azurewebsites.net** ou vers l'adresse IP.

> [WACOM.NOTE] L'adresse IP de votre site Web est modifiée si vous supprimez votre site Web et que vous le recréez par la suite, ou que vous changez le mode de votre site Web et le définissez sur Gratuit, alors qu'il était en mode Basique, Partagé ou Standard.

Il existe plusieurs types d'enregistrements, chacun avec ses fonctions et limites, mais les sites Web n'en utilisent que deux : les enregistrements *CNAME* et *A*.

### Enregistrement CNAME ou d'alias

Un enregistrement CNAME mappe un nom DNS *spécifique*, tel que **mail.contoso.com** ou **www.contoso.com**, à un autre nom de domaine (canonique). Dans le cas des sites Web Azure, le nom canonique est le nom de domaine **&lt;myapp\>.azurewebsites.net** de votre site Web. Une fois créé, l'enregistrement CNAME crée un alias pour le nom de domaine **&lt;monapp\>.azurewebsites.net**. L'entrée CNAME devient automatiquement l'adresse IP de votre nom de domaine **&lt;monapp\>.azurewebsites.net**. Ainsi, même si l'adresse IP de votre site Web change, vous n'avez aucune action à effectuer.

> [WACOM.NOTE] Certains bureaux d'enregistrement de domaines autorisent le mappage de sous-domaines uniquement lorsqu'un enregistrement CNAME est utilisé (par exemple, **www.contoso.com**) et non un nom racine (tel que **contoso.com**). Pour plus d'informations sur les enregistrements CNAME, consultez la documentation fournie par votre bureau d'enregistrement, [la page Wikipedia sur l'enregistrement CNAME](http://en.wikipedia.org/wiki/CNAME_record) ou le document [Noms de domaine IETF - Implémentation et spécification](http://tools.ietf.org/html/rfc1035).

### Enregistrement A

Un enregistrement A associe un domaine, tel que **contoso.com** ou **www.contoso.com**, *ou un nom de domaine générique* (par exemple : **\*.contoso.com**) à une adresse IP. Dans le cas d'un site Web Azure, il s'agit de l'adresse IP virtuelle du service ou d'une adresse IP spécifique achetée pour votre site Web.

Les principaux avantages d'un enregistrement A par rapport à un enregistrement CNAME sont les suivants :

-   Vous pouvez mapper un domaine racine tel que **contoso.com** vers une adresse IP. De nombreux bureaux d'enregistrement autorisent cette action uniquement avec des enregistrements A.

-   Vous pouvez avoir une entrée avec un caractère générique (**\*.contoso.com**, par exemple) qui gère les requêtes pour plusieurs sous-domaines, tels que **mail.contoso.com**, **login.contoso.com** ou **www.contso.com**.

> [WACOM.NOTE] L'enregistrement A étant associé à une adresse IP statique, les changements d'adresse IP de votre site Web ne sont donc pas pris en compte automatiquement. Une adresse IP à utiliser avec les enregistrements A est fournie lorsque vous configurez les paramètres de nom de domaine personnalisé pour votre site Web. Cette valeur est toutefois susceptible d'être modifiée si vous supprimez et recréez votre site Web ou que vous faites de nouveau passer votre site Web en mode Gratuit.

### Caractéristiques DNS des sites Web Azure

Pour utiliser un enregistrement A avec le service Sites Web Azure, vous devez d'abord créer un enregistrement CNAME qui mappe un des deux noms suivants :

-   Un nom DNS de **www** vers **&lt;nomdusite\>.azurewebsites.net**. OU
-   Un nom DNS de **awverify.www** vers **awverify.&lt;nomdusite\>.azurewebsites.net**.

Cet enregistrement CNAME permet de vérifier que vous détenez bien le domaine que vous essayez d'utiliser. Cette opération s'ajoute à la création d'un enregistrement A pointant vers l'adresse IP virtuelle de votre site Web.

Pour connaître l'adresse IP, ainsi que les noms **awverify.www** et **.azurewebsites.net** pour votre site Web, procédez comme suit :

1.  Dans votre navigateur, ouvrez le [portail de gestion Azure](https://manage.windowsazure.com).

2.  Dans l'onglet **Web Sites**, cliquez sur le nom de votre site, sélectionnez **Dashboard**, puis **Manage Domains** en bas de la page.

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3.  Dans la boîte de dialogue **MANAGE CUSTOM DOMAINS** sont affichés le nom de domaine **.azurewebsites.net**, les informations **awverify** et l'adresse IP virtuelle.


