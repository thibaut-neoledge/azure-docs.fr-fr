Le DNS (Domain Name System) permet de localiser des ressources sur Internet. Par exemple, lorsque vous entrez une adresse de site web dans votre navigateur ou cliquez sur un lien d'une page web, le DNS est utilisé pour traduire le domaine en adresse IP. L'adresse IP joue le même rôle qu'une adresse postale, tout en étant moins significative. Par exemple, il est bien plus facile de se souvenir d'un nom DNS tel que **contoso.com** que d'une adresse IP telle que 192.168.1.88 ou 2001:0:4137:1f67:24a2:3888:9cce:fea3.

Le système DNS est basé sur des *enregistrements*. Les enregistrements associent un *nom* spécifique, tel que **contoso.com**, à une adresse IP ou un autre nom DNS. Lorsqu'une application (par exemple, un navigateur Web) recherche un nom dans le DNS, elle trouve l'enregistrement et utilise sa cible comme adresse. Si la valeur ciblée correspond à une adresse IP, le navigateur l'utilise. Si la cible est un autre nom DNS, l'application doit à nouveau procéder à une résolution. En fin de compte, toute résolution de nom génère une adresse IP.

Lorsque vous créez un site web Azure, un nom DNS lui est automatiquement attribué. Ce nom se présente comme suit : **\<nomdevotresite\>.azurewebsites.net**. Il existe également une adresse IP virtuelle à utiliser lors de la création des enregistrements DNS. Vous pouvez donc créer des enregistrements pointant vers **.azurewebsites.net** ou pointer vers l'adresse IP.

> [WACOM.NOTE] L'adresse IP de votre site web est modifiée si vous supprimez et recréez votre site web ou changez le mode de votre site web et le définissez sur Gratuit, alors qu'il était en mode De base, Partagé ou Standard.

Il existe également plusieurs types d'enregistrements, chacun avec ses fonctions et limites, mais les sites web n'en utilisent que deux : les enregistrements *A* et *CNAME*.

### Enregistrement d'adresse (enregistrement A)

Un enregistrement A associe un domaine, tel que **contoso.com** ou **www.contoso.com**, *ou un nom de domaine générique* (par exemple : **\*.contoso.com**) à une adresse IP. Dans le cas d'un site web Azure, il s'agit de l'adresse IP virtuelle du service ou d'une adresse IP spécifique achetée pour votre site web.

Les principaux avantages d'un enregistrement A par rapport à un enregistrement CNAME sont les suivants :

-   Vous pouvez mapper un domaine racine tel que **contoso.com** vers une adresse IP. De nombreux bureaux d'enregistrement autorisent cette action uniquement avec des enregistrements A.

-   Vous pouvez avoir une entrée avec un caractère générique **\*.contoso.com**, par exemple) qui gère les demandes pour plusieurs sous-domaines, tels que **mail.contoso.com**, **blogs.contoso.com** ou **www.contoso.com**.

> [WACOM.NOTE] L'enregistrement A étant associé à une adresse IP statique, les changements d'adresse IP de votre site web ne sont donc pas pris en compte automatiquement. Une adresse IP à utiliser avec les enregistrements A est fournie lorsque vous configurez les paramètres de nom de domaine personnalisé pour votre site web. Cette valeur est toutefois susceptible d'être modifiée si vous supprimez et recréez votre site web ou que vous faites de nouveau passer votre site web en mode Gratuit.

### Enregistrement d'alias (enregistrement CNAME)

Un enregistrement CNAME mappe un nom DNS *spécifique*, tel que **mail.contoso.com** ou **www.contoso.com**, à un autre nom de domaine (canonique). Dans le cas des sites web Azure, le nom de domaine canonique est le nom de domaine **\<nomdevotresite\>.azurewebsites.net** de votre site web. Une fois créé, l'enregistrement CNAME crée un alias pour le nom de domaine **\<nomdevotresite\>.azurewebsites.net**. L'entrée CNAME devient automatiquement l'adresse IP de votre nom de domaine **\<nomdevotresite\>.azurewebsites.net**. Ainsi, même si l'adresse IP du site web change, vous n'avez aucune action à effectuer.

> [WACOM.NOTE] Certains bureaux d'enregistrement de domaines autorisent le mappage de sous-domaines uniquement lorsqu'un enregistrement CNAME est utilisé par exemple, **www.contoso.com** et non un nom racine tel que **contoso.com**. Pour plus d'informations sur les enregistrements CNAME, consultez la documentation fournie par votre bureau d'enregistrement, [la page Wikipedia sur l'enregistrement CNAME][] ou le document [Noms de domaine IETF - Implémentation et spécification][].

### Caractéristiques DNS des sites web Azure

Pour utiliser un enregistrement A avec Sites Web Azure, vous devez d'abord créer l'un des enregistrements CNAME suivants :

-   **Pour le domaine racine ou des sous-domaines avec caractères génériques** : un nom DNS de **awverify** vers **awverify.\<nomdevotresiteweb\>.azurewebsites.net**.

-   **Pour un sous-domaine spécifique** : un nom DNS de **awverify.\<sous-domaine\>** vers **awverify.\<nomdevotresiteweb\>.azurewebsites.net**. Par exemple : **awverify.blogs** si l'enregistrement A est destiné à **blogs.contoso.com**.

Cet enregistrement CNAME permet de vérifier que vous détenez bien le domaine que vous essayez d'utiliser. Cette opération s'ajoute à la création d'un enregistrement A pointant vers l'adresse IP virtuelle de votre site web.

Pour connaître l'adresse IP, ainsi que les noms **awverify** et **.azurewebsites.net** pour votre site web, procédez comme suit :

1.  Dans votre navigateur, ouvrez le [portail de gestion Azure][].

2.  Sous l'onglet **Sites Web**, cliquez sur le nom de votre site, sélectionnez **Tableau de bord**, puis **Gérer les domaines** en bas de la page.

    ![][]

    > [WACOM.NOTE] Si **Gérer les domaines** n'est pas activé, vous utilisez un site web gratuit. Les noms de domaine personnalisés ne peuvent pas être utilisés avec les sites web gratuits. Vous devez effectuer une mise à niveau vers le mode Partagé, De base ou Standard. Pour plus d'informations sur les modes de sites web, notamment sur la modification du mode de votre site, consultez la page [Mise à l'échelle de sites Web][].

3.  Dans la boîte de dialogue **MANAGE CUSTOM DOMAINS** sont affichés le nom de domaine **.azurewebsites.net**, les informations **awverify** et l'adresse IP virtuelle. Enregistrez ces informations, car elles serviront lors de la création d'enregistrements DNS.

    ![][1]

  [la page Wikipedia sur l'enregistrement CNAME]: http://en.wikipedia.org/wiki/CNAME_record
  [Noms de domaine IETF - Implémentation et spécification]: http://tools.ietf.org/html/rfc1035
  [portail de gestion Azure]: https://manage.windowsazure.com
  []: ./media/custom-dns-web-site/dncmntask-cname-6.png
  [Mise à l'échelle de sites Web]: http://www.windowsazure.com/en-us/documentation/articles/web-sites-scale/
  [1]: ./media/custom-dns-web-site/managecustomdomains.png
