Le DNS (Domain Name System) permet de localiser des ressources sur Internet. Par exemple, lorsque vous entrez une adresse d’application web dans votre navigateur ou cliquez sur un lien dans une page web, le DNS est utilisé pour traduire le domaine en adresse IP. L'adresse IP joue le même rôle qu'une adresse postale, tout en étant moins significative. Par exemple, il est bien plus facile de se souvenir d'un nom DNS tel que **contoso.com** que d'une adresse IP telle que 192.168.1.88 ou 2001:0:4137:1f67:24a2:3888:9cce:fea3.

Le système DNS est basé sur des *enregistrements*. Les enregistrements associent un *nom*spécifique, tel que **contoso.com**, à une adresse IP ou un autre nom DNS. Lorsqu'une application (par exemple, un navigateur Web) recherche un nom dans le DNS, elle trouve l'enregistrement et utilise sa cible comme adresse. Si la valeur ciblée correspond à une adresse IP, le navigateur l'utilise. Si la cible est un autre nom DNS, l'application doit à nouveau procéder à une résolution. En fin de compte, toute résolution de nom génère une adresse IP.

Lorsque vous créez une application web dans App Service, un nom DNS lui est automatiquement attribué. Ce nom se présente comme suit : **&lt;nomdevotreapplicationweb&gt;.azurewebsites.net**. Il existe également une adresse IP virtuelle utilisable lors de la création des enregistrements DNS. Vous pouvez donc soit créer des enregistrements pointant vers **.azurewebsites.net**, soit pointer vers l’adresse IP.

> [!NOTE]
> L’adresse IP de votre application web est modifiée si vous supprimez et recréez votre application web. Elle l’est aussi si vous redéfinissez le mode de plan App Service sur **Gratuit** après l’avoir défini sur **De base**, **Partagé** ou **Standard**.
> 
> 

Il existe également plusieurs types d’enregistrements, présentant chacun ses propres fonctions et limites, mais les applications web n’en utilisent que deux : les enregistrements *A* et *CNAME*.

### <a name="address-record-a-record"></a>Enregistrement d'adresse (enregistrement A)
Un enregistrement A mappe un domaine, tel que **contoso.com** ou **www.contoso.com**, *ou un nom de domaine générique* comme **\*.contoso.com**, sur une adresse IP. Dans le cas d’une application web dans App Service, il s’agit de l’adresse IP virtuelle du service ou d’une adresse IP spécifique que vous avez achetée pour votre application web.

Les principaux avantages d'un enregistrement A par rapport à un enregistrement CNAME sont les suivants :

* Vous pouvez mapper un domaine racine tel que **contoso.com** sur une adresse IP. De nombreux bureaux d’enregistrement n’autorisent cette action qu’avec des enregistrements A.
* L’une de vos entrées peut utiliser un caractère générique (**\*.contoso.com**, par exemple) afin de gérer les demandes pour plusieurs sous-domaines, tels que **mail.contoso.com**, **blogs.contoso.com** ou **www.contoso.com**.

> [!NOTE]
> L’enregistrement A étant mappé à une adresse IP statique, les changements d’adresse IP de votre application web ne sont donc pas pris en compte automatiquement. Une adresse IP à utiliser avec les enregistrements A est fournie lorsque vous configurez les paramètres de nom de domaine personnalisé pour votre application web. Toutefois, cette valeur est susceptible d’être modifiée si vous supprimez et recréez votre application web ou que vous repassez en mode de plan App Service **Gratuit**.
> 
> 

### <a name="alias-record-cname-record"></a>Enregistrement d'alias (enregistrement CNAME)
Un enregistrement CNAME mappe un nom DNS *spécifique*, tel que **mail.contoso.com** ou **www.contoso.com**, sur un autre nom de domaine (canonique). Dans le cas d’App Service Web Apps, le nom de domaine canonique est le nom de domaine **&lt;nomdevotreapplicationweb>.azurewebsites.net** de votre application web. Une fois créé, l’enregistrement CNAME crée un alias pour le nom de domaine **&lt;nomdevotreapplicationweb>.azurewebsites.net**. L’entrée CNAME devient automatiquement l’adresse IP de votre nom de domaine **&lt;nomdevotreapplicationweb>.azurewebsites.net**. Ainsi, même si l’adresse IP de l’application web change, vous n’avez aucune action à effectuer.

> [!NOTE]
> Certains bureaux d’enregistrement de domaines n’autorisent le mappage de sous-domaines qu’en cas d’utilisation d’un enregistrement CNAME (par exemple, **www.contoso.com**), et non d’un nom racine (tel que **contoso.com**). Pour plus d'informations sur les enregistrements CNAME, consultez la documentation fournie par votre bureau d'enregistrement, la <a href="http://en.wikipedia.org/wiki/CNAME_record">page Wikipédia sur l'enregistrement CNAME</a> ou le document <a href="http://tools.ietf.org/html/rfc1035">Noms de domaine IETF - Implémentation et spécification</a>.
> 
> 

### <a name="web-app-dns-specifics"></a>Spécifications DNS des applications web
L’utilisation d’un enregistrement A avec Web Apps exige que vous créiez d’abord l’un des enregistrements TXT suivants :

* **Pour le domaine racine** : enregistrement TXT DNS A de **@** défini sur **&lt;nomdevotreapplicationweb&gt;.azurewebsites.net**.
* **Pour un sous-domaine spécifique** : nom DNS A de **&lt;sous-domaine>** défini sur **&lt;nomdevotreapplicationweb&gt;.azurewebsites.net**. Par exemple, **blogs** si l’enregistrement A est destiné à **blogs.contoso.com**.
* **Pour les sous-domaines génériques** : enregistrement TXT DNS A de ***** défini sur **&lt;nomdevotreapplicationweb&gt;.azurewebsites.net**.

Cet enregistrement TXT permet de vérifier que vous détenez bien le domaine que vous tentez d’utiliser. Cette opération s’ajoute à la création d’un enregistrement A pointant vers l’adresse IP virtuelle de votre application web.

Pour connaître l’adresse IP ainsi que les noms **.azurewebsites.net** de votre application web, procédez comme suit :

1. Dans votre navigateur, ouvrez le [portail Azure](https://portal.azure.com).
2. Dans le panneau **Web Apps**, cliquez sur le nom de votre application web, puis sélectionnez **Domaines personnalisés** au bas de la page.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. Dans le panneau **Domaines personnalisés** , vous voyez l’adresse IP virtuelle. Enregistrez ces informations, car elles serviront lors de la création d’enregistrements DNS.
   
    ![](./media/custom-dns-web-site/virtual-ip-address.png)
   
   > [!NOTE]
   > Vous ne pouvez pas utiliser de noms de domaines personnalisés avec une application web en mode **Gratuit**. Vous devez donc mettre à niveau le plan App Service vers le niveau **Partagé**, **De base**, **Standard** ou **Premium**. Pour plus d’informations sur les niveaux tarifaires du plan App Service, notamment sur la modification du niveau tarifaire de votre application web, consultez l’article [Mise à l’échelle des applications web](../articles/app-service-web/web-sites-scale.md).
   > 
   > 



<!--HONumber=Nov16_HO3-->


