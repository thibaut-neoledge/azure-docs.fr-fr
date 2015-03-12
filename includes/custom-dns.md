# Configuration d'un nom de domaine personnalisé pour un service cloud Azure

> [AZURE.NOTE]
> Avancez plus rapidement, utilisez la NOUVELLE [procédure pas à pas d'Azure](http://support.microsoft.com/kb/2990804) !  L'association d'un nom de domaine personnalisé ET la sécurisation des communications (SSL) avec Azure Cloud Services ou Sites Web Azure deviennent un jeu d'enfant.

Lorsque vous créez une application dans Azure, Azure fournit un sous-domaine sur le domaine cloudapp.net pour que vos utilisateurs puissent accéder à votre application sur une URL telle que http://&lt;*myapp*>. cloudapp.net. Toutefois, vous pouvez également exposer votre application sur votre propre nom de domaine, par exemple, contoso.com.

> [AZURE.NOTE] 
> Les procédures décrites dans cette tâche s'appliquent aux services cloud Azure. Pour les comptes de stockage, consultez la rubrique [Configuration d'un nom de domaine personnalisé pour un compte de stockage Azure](../storage-custom-domain-name/). Pour les sites web, consultez la page [Configuration d'un nom de domaine personnalisé pour un site web Azure](../web-sites-custom-domain-name/).

Dans cet article :

-   [Présentation des enregistrements CNAME et A](#access-app)
-   [Ajout d'un enregistrement CNAME pour votre domaine personnalisé](#add-cname)
-   [Ajout d'un enregistrement A pour votre nom de domaine personnalisé](#add-aname)

<h2><a name="access-app"></a>Présentation des enregistrements CNAME et A</h2>

Bien que fonctionnant différemment, les enregistrements CNAME (ou enregistrements d'alias) et A permettent d'associer un nom de domaine avec un serveur spécifique (ou un service dans ce cas). Avant de faire votre choix, certains aspects spécifiques sont également à prendre en considération lors de l'utilisation d'enregistrements A avec les services cloud Azure.

###Enregistrement CNAME ou d'alias

Un enregistrement CNAME associe un domaine *specific*, tel que **contoso.com** ou **www.contoso.com**, à un nom de domaine canonique. Dans ce cas, le nom de domaine canonique est le nom de domaine **&lt;myapp>.cloudapp.net** de votre application hébergée Azure. Une fois créé, l'enregistrement CNAME crée un alias pour **&lt;myapp>.cloudapp.net**. L'entrée CNAME résout l'adresse IP de votre service **&lt;myapp>.cloudapp.net** automatiquement ; si l'adresse IP du service cloud change, vous n'avez donc pas à intervenir.

> [AZURE.NOTE] 
> Certains bureaux d'enregistrement de domaines autorisent le mappage de sous-domaines uniquement lorsqu'un enregistrement CNAME est utilisé (par exemple, www.contoso.com) et non un nom racine (tel que contoso.com). Pour plus d'informations sur les enregistrements CNAME, consultez la documentation fournie par votre bureau d'enregistrement, <a href="http://en.wikipedia.org/wiki/CNAME_record">la page Wikipedia sur l'enregistrement CNAME</a>ou le document <a href="http://tools.ietf.org/html/rfc1035">Noms de domaine IETF - Implémentation et spécification</a> .

###Enregistrement A

Un enregistrement A mappe un domaine, tel que **contoso.com** ou **www.contoso.com**, *or a wildcard domain* comme **\*.contoso.com**, à une adresse IP. Dans le cas d'un service cloud Azure, il s'agit de l'adresse IP virtuelle du service. Par conséquent, l'avantage principal d'un enregistrement A sur un enregistrement CNAME est que vous pouvez avoir une entrée utilisant un caractère générique, par exemple, ***.contoso.com**, qui permet de traiter les requêtes pour plusieurs sous-domaines tels que **mail.contoso.com**, **login.contoso.com** ou **www.contso.com**.

> [AZURE.NOTE]
> L'enregistrement A étant associé à une adresse IP statique, les changements d'adresse IP de votre service cloud ne sont donc pas pris en compte automatiquement. L'adresse IP utilisée par votre service cloud est allouée la première fois que vous effectuez un déploiement vers un emplacement vide (de production ou intermédiaire). Si vous supprimez le déploiement de l'emplacement, l'adresse IP est publiée par Azure et tout déploiement futur dans l'emplacement peut recevoir une nouvelle adresse IP.
> 
> De façon assez pratique, l'adresse IP d'un emplacement de déploiement donné (de production ou intermédiaire) est conservée lors du basculement entre les déploiements intermédiaires et de production ou lors de l'exécution de la mise à niveau sur place d'un déploiement existant. Pour plus d'informations sur l'exécution de ces actions, consultez la page [Gestion des services cloud](../cloud-services-how-to-manage/).


<h2><a name="add-cname"></a>Ajout d'un enregistrement CNAME pour votre domaine personnalisé</h2>

Pour créer un enregistrement CNAME, vous devez ajouter une nouvelle entrée dans la table DNS de votre domaine personnalisé en utilisant les outils fournis par votre bureau d'enregistrement. Chaque bureau d'enregistrement possède sa propre méthode de spécification des enregistrements CNAME, même si le fonctionnement général reste souvent similaire.

1. Employez une des méthodes suivantes pour connaître le nom de domaine **.cloudapp.net** attribué à votre service cloud.

  * Connectez-vous au [portail de gestion Azure], sélectionnez votre service cloud, sélectionnez **Tableau de bord**, puis recherchez l'entrée **URL du site** dans la section **Aperçu rapide**.

  		  ![quick glance section showing the site URL][csurl]

  * Installez et configurez [Azure Powershell](../install-configure-powershell/), puis utilisez la commande suivante :

    Get-AzureDeployment -ServiceName yourservicename | Select Url

  Enregistrez le nom de domaine utilisé dans l'URL renvoyée par l'une des méthodes, car vous en aurez besoin lors de la création d'un enregistrement CNAME.

1.  Connectez-vous au site web du bureau d'enregistrement de votre DNS et accédez à la page de gestion DNS. Recherchez des liens ou des zones indiquant **Nom de domaine**, **DNS** ou **Name Server Management**.

2.  Maintenant, cherchez où vous pouvez sélectionner ou saisir vos enregistrements CNAME. Il se peut que vous deviez sélectionner le type d'enregistrement dans une liste déroulante ou accéder à une page de paramètres avancés. La section recherchée doit normalement comporter les mots **CNAME**, **Alias** ou **sous-domaines**.

3.  Vous devez également fournir l'alias de domaine ou de sous-domaine pour l'enregistrement CNAME, tel que **www** si vous souhaitez créer un alias pour **www.domainepersonnalisé.com**. Si vous souhaitez créer un alias pour le domaine racine, l'entrée correspondante devrait être répertoriée avec le symbole " **@** " dans les outils DNS de votre bureau d'enregistrement.

4. Vous devez ensuite fournir un nom d'hôte canonique, qui correspond au domaine **cloudapp.net** de votre application dans le cas présent.

Par exemple, l'enregistrement CNAME suivant renvoie tout le trafic de **www.contoso.com** vers **contoso.cloudapp.net**, le nom de domaine personnalisé de votre application déployée :

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Nom d'hôte/Sous-domaine</strong></td>
<td><strong>Domaine canonique</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.cloudapp.net</td>
</tr>
</table>

Un visiteur accédant à **www.contoso.com** ne verra jamais l'hôte réel
(contoso.cloudapp.net), le processus de transfert est donc transparent pour
l'utilisateur final.

> [AZURE.NOTE]
> L'exemple ci-dessus s'applique uniquement au trafic sur le sous-domaine <strong>www</strong> . Puisqu'il n'est pas possible d'utiliser des caractères génériques pour les enregistrements CNAME, vous devez créer un enregistrement CNAME pour chaque domaine/sous-domaine. Si vous souhaitez diriger  le trafic des sous-domaines, tels que *.contoso.com, vers votre adresse cloudapp.net, vous pouvez configurer une entrée <strong>Redirection d'URL</strong> ou <strong>Transfert d'URL</strong> dans vos paramètres DNS, ou créer un enregistrement A.


<h2><a name="add-aname"></a>Ajout d'un enregistrement A pour votre domaine personnalisé</h2>

Pour créer un enregistrement A, vous devez tout d'abord connaître l'adresse IP virtuelle de votre service cloud. Ajoutez ensuite une entrée dans la table DNS de votre domaine personnalisé à l'aide des outils fournis par votre bureau d'enregistrement. Chaque bureau d'enregistrement possède sa propre méthode de spécification des enregistrements A, même si le fonctionnement général reste souvent similaire.

1. Utilisez l'une des méthodes suivantes pour obtenir l'adresse IP de votre service cloud.

  * Connectez-vous au [portail de gestion Azure], sélectionnez votre service cloud, puis sélectionnez **Tableau de bord** et recherchez l'entrée ** 	Adresse IP virtuelle (VIP) publique** dans la section **Aperçu rapide**.

   		 ![quick glance section showing the VIP][vip]

  * Installez et configurez [Azure Powershell](../install-configure-powershell/), puis utilisez la commande suivante :

      get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip

    Si plusieurs points de terminaison sont associés à votre service cloud, vous recevrez plusieurs lignes contenant l'adresse IP, mais toutes doivent comporter la même adresse.

  Enregistrez l'adresse IP. Vous en aurez besoin lors de la création d'un enregistrement A.

1.  Connectez-vous au site web du bureau d'enregistrement de votre DNS et accédez à la page de gestion DNS. Recherchez des liens ou des zones indiquant **Nom de domaine**, **DNS** ou **Name Server Management**.

2.  Maintenant, cherchez où vous pouvez sélectionner ou saisir vos enregistrements A. Il se peut que vous deviez sélectionner le type d'enregistrement dans une liste déroulante ou accéder à une page de paramètres avancés.

3. Sélectionnez ou entrez le domaine ou sous-domaine qui utilisera cet enregistrement A. Par exemple, sélectionnez **www** si vous souhaitez créer un alias pour **www.domainepersonnalisé.com**. Pour créer une entrée avec des caractères génériques pour l'ensemble des sous-domaines, entrez " __*__ ". Ceci permet de couvrir tous les sous-domaines tels que **mail.customdomain.com**, **login.customdomain.com** et **www.customdomain.com**.

  Si vous souhaitez créer un enregistrement A pour le domaine racine, l'entrée correspondante devrait être répertoriée avec le symbole " **@** " dans les outils DNS de votre bureau d'enregistrement.

4. Entrez l'adresse IP de votre service cloud dans le champ prévu à cet effet. Cette opération permet d'associer le domaine de l'enregistrement A avec l'adresse IP de votre déploiement de service cloud.

Par exemple, l'enregistrement A suivant transfère tout le trafic de **contoso.com** vers **137.135.70.239**, l'adresse IP de votre application déployée :

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Nom d'hôte/Sous domaine</strong></td>
<td><strong>Adresse IP</strong></td>
</tr>
<tr>
<td>@</td>
<td>137.135.70.239</td>
</tr>
</table>

Cet exemple montre comment créer un enregistrement A pour le domaine racine. Pour créer une entrée avec des caractères génériques qui couvre l'ensemble des sous-domaines, entrez " __*__ " comme sous-domaine.

## Étapes suivantes

-   [Gestion des services cloud](../cloud-services-how-to-manage/)
-   [Mappage du contenu CDN à un domaine personnalisé][]

  [Exposition de votre application sur un domaine personnalisé]: #access-app
  [Ajout d'un enregistrement CNAME pour votre domaine personnalisé]: #add-cname
  [Exposition de vos données sur un domaine personnalisé]: #access-data
  [Échange de VIP]: http://msdn.microsoft.com/library/ee517253.aspx
  [Création d'un enregistrement CNAME qui associe le sous-domaine au compte de stockage]: #create-cname
  [Portail de gestion Azure]: https://manage.windowsazure.com
  [Validation de la boîte de dialogue de domaine personnalisé]: http://i.msdn.microsoft.com/dynimg/IC544437.jpg
  [Mappage du contenu CDN à un domaine personnalisé]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx
  [vip]: ./media/custom-dns/csvip.png
  [csurl]: ./media/custom-dns/csurl.png
<!--HONumber=45--> 
