
# Configuration d'un nom de domaine personnalisé pour un service cloud Azure

Lorsque vous créez une application dans Azure, Azure fournit un sous-domaine sur le domaine cloudapp.net pour permettre à vos utilisateurs d'accéder à votre application à partir d'une URL telle que http://<*monapp*>.cloudapp.net. Toutefois, vous pouvez également proposer votre application sur votre propre nom de domaine, par exemple contoso.com.

> [WACOM.NOTE] Les procédures décrites dans cette tâche s'appliquent aux services cloud Azure. Pour les comptes de stockage, consultez la rubrique [Configuration d'un nom de domaine personnalisé pour un compte de stockage Azure](../storage-custom-domain-name/). Pour les sites Web, consultez la rubrique [Configuration d'un nom de domaine personnalisé pour un site Web Azure](../web-sites-custom-domain-name/).

Dans cet article :

* [Présentation des enregistrements CNAME et A](#access-app)
* [Ajout d'un enregistrement CNAME pour votre domaine
  personnalisé](#add-cname)
* [Ajout d'un enregistrement A pour votre nom de domaine
  personnalisé](#add-aname)

<h2><a name="access-app"></a>Présentation des enregistrements CNAME et A</h2>


Bien que fonctionnant différemment, les enregistrements CNAME (ou enregistrements d'alias) et A permettent d'associer un nom de domaine avec un serveur spécifique (ou un service dans ce cas). Avant de faire votre choix, certains aspects spécifiques sont également à prendre en considération lors de l'utilisation d'enregistrements A avec les services cloud Azure.

### Enregistrement CNAME ou d'alias

Un enregistrement CNAME associe un domaine *spécifique*, tel que **contoso.com** ou **www.contoso.com**, à un nom de domaine canonique. Dans ce cas, le nom de domaine canonique est le nom de domaine **<monapp>.cloudapp.net** de votre application hébergée Azure. Une fois créé, l'enregistrement CNAME émet un alias pour **<monapp>.cloudapp.net**. L'entrée CNAME devient automatiquement l'adresse IP de votre service **<monapp>.cloudapp.net**. Ainsi, même si l'adresse IP du service cloud change, vous n'avez aucune action à effectuer.

> [WACOM.NOTE] 
Certains bureaux d'enregistrement autorisent le mappage de sous-domaines uniquement lorsqu'un enregistrement CNAME est utilisé (par exemple, www.contoso.com) et non un nom racine, (tel que contoso.com). Pour plus d'informations sur les enregistrements CNAME, consultez la documentation remise par votre bureau d'enregistrement, [la page Wikipedia sur l'enregistrement CNAME][1] ou le document [IETF Domain Names - Implementation and Specification][2].

### Enregistrement A

Un enregistrement A associe un domaine, tel que **contoso.com** ou **www.contoso.com**, *ou un nom de domaine générique* (par exemple : **\*.contoso.com**) à une adresse IP. Dans le cas d'un service cloud Azure, il s'agit de l'adresse IP virtuelle du service. Le principal avantage d'un enregistrement A par rapport à un enregistrement CNAME est que vous pouvez disposer d'une entrée utilisant un caractère générique (par exemple, **\*.contoso.com**), ce qui permet de gérer les demandes pour plusieurs sous-domaines, tels que **mail.contoso.com**, **login.contoso.com** ou **www.contso.com**.

> [WACOM.NOTE] 
L'enregistrement A étant associé à une adresse IP statique, les changements d'adresse IP de votre service cloud ne sont donc pas pris en compte automatiquement. L'adresse IP utilisée par votre service cloud est allouée la première fois que vous effectuez un déploiement vers un emplacement vide (de production ou intermédiaire). Si vous supprimez le déploiement de l'emplacement, l'adresse IP est publiée par Azure et tout déploiement futur dans l'emplacement peut recevoir une nouvelle adresse IP.

> De façon assez pratique, l'adresse IP d'un emplacement de déploiement donné (de production ou intermédiaire) est conservée lors du basculement entre les déploiements intermédiaires et de production ou lors de l'exécution de la mise à niveau sur place d'un déploiement existant. Pour plus d'informations sur ces actions, consultez la rubrique [Gestion des services cloud](../cloud-services-how-to-manage/).

<h2><a name="add-cname"></a>Ajout d'un enregistrement CNAME pour votre domaine personnalisé</h2>


Pour créer un enregistrement CNAME, vous devez ajouter une nouvelle entrée dans la table DNS de votre domaine personnalisé en utilisant les outils fournis par votre bureau d'enregistrement. Chaque bureau d'enregistrement possède sa propre méthode de spécification des enregistrements CNAME, même si le fonctionnement général reste souvent similaire.

1.  Employez une des méthodes suivantes pour connaître le nom de domaine **.cloudapp.net** attribué à votre service cloud.

	* Connectez-vous au [portail de gestion Azure][3], sélectionnez votre service cloud, sélectionnez **Dashboard**, puis cherchez l'entrée **Site URL** dans la section **quick glance**.

 			![section aperçu rapide indiquant l’URL du site](./media/custom-dns/csurl.png) 
	* Installez et configurez [Azure Powershell](../install-configure-powershell/), puis utilisez la commande suivante :
	
		Get-AzureDeployment -ServiceName votre\_nom\_service \| Select Url

	Enregistrez le nom de domaine utilisé dans l'URL renvoyée par l'une des méthodes, car vous en aurez besoin lors de la création d'un enregistrement CNAME.

1.  Connectez-vous au site Web du bureau d'enregistrement de votre DNS et accédez à la section de gestion DNS. Recherchez la mention **Domain Name**, **DNS** ou **Name Server Management**.

2.  Maintenant, cherchez où vous pouvez sélectionner ou saisir vos enregistrements CNAME. Il se peut que vous deviez sélectionner le type d'enregistrement dans une liste déroulante ou accéder à une page de paramètres avancés. La section recherchée doit normalement comporter les mots **CNAME**, **Alias** ou **Subdomains**.

3.  Vous devez également fournir l'alias de domaine ou de sous-domaine pour l'enregistrement CNAME, tel que **www** si vous voulez créer un alias pour **www.domainepersonnalisé.com**. Si vous voulez créer un alias pour le domaine racine, l'entrée correspondante devrait être répertoriée avec le symbole << **@** >> dans les outils DNS de votre bureau d'enregistrement.

4.  Vous devez ensuite fournir un nom d'hôte canonique, qui correspond au domaine **cloudapp.net** de votre application dans le cas présent.

Par exemple, l'enregistrement CNAME suivant renvoie tout le trafic de **www.contoso.com** vers **contoso.cloudapp.net**, le nom de domaine personnalisé de votre application déployée :

<table  border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Nom d’hôte/Sous-domaine</strong>
</td>

<td><strong>Domaine canonique</strong>
</td>

</tr>

<tr>
<td>www</td>

<td>contoso.cloudapp.net</td>

</tr>

</table>

Un utilisateur consultant le site **www.contoso.com** ne verra jamais l'adresse de l'hôte réel (contoso.cloudapp.net). Le processus de transfert est donc invisible pour l'utilisateur final.

> [WACOM.NOTE] 
L'exemple ci-dessus s'applique uniquement au trafic sur le sous-domaine **www**. Puisqu'il n'est pas
possible d'utiliser des caractères génériques pour les enregistrements CNAME, vous devez créer un enregistrement CNAME pour chaque domaine/sous-domaine. Pour rediriger le trafic de sous-domaines tels que \*.contoso.com vers votre adresse cloudapp.net, vous pouvez configurer une entrée de **redirection d'URL**ou de **transfert d'URL** dans vos paramètres DNS. Vous pouvez également créer un enregistrement A.

<h2><a name="add-aname"></a>Ajout d'un enregistrement A pour votre domaine personnalisé</h2>


Pour créer un enregistrement A, vous devez tout d'abord connaître l'adresse IP virtuelle de votre service cloud. Ajoutez ensuite une entrée dans la table DNS de votre domaine personnalisé à l'aide des outils fournis par votre bureau d'enregistrement. Chaque bureau d'enregistrement possède sa propre méthode de spécification des enregistrements A, même si le fonctionnement général reste souvent similaire.

1.  Utilisez l'une des méthodes suivantes pour obtenir l'adresse IP de votre service cloud.

	* Connectez-vous au [portail de gestion Azure][3], sélectionnez votre service cloud, sélectionnez **Dashboard**, puis cherchez l'entrée **Public Virtual IP (VIP) address** dans la section **quick glance**.

 		![section aperçu rapide illustrant l’adresse IP virtuelle publique](./media/custom-dns/csvip.png) 
	* Installez et configurez [Azure Powershell](../install-configure-powershell/), puis utilisez la commande suivante :
  
  		get-azurevm -servicename votre\_nom\_service \| get-azureendpoint -VM \{$\_.VM} \| select Vip
  
  		Si plusieurs points de terminaison sont associés à votre service cloud, vous recevrez plusieurs lignes contenant l'adresse IP, mais toutes doivent comporter la même adresse.

	Enregistrez l'adresse IP. Vous en aurez besoin lors de la création d'un enregistrement A.

1.  Connectez-vous au site Web du bureau d'enregistrement de votre DNS et accédez à la section de gestion DNS. Recherchez la mention **Domain Name**, **DNS** ou **Name Server Management**.

2.  Maintenant, cherchez où vous pouvez sélectionner ou saisir vos enregistrements A. Il se peut que vous deviez sélectionner le type d'enregistrement dans une liste déroulante ou accéder à une page de paramètres avancés.

3.  Sélectionnez ou entrez le domaine ou sous-domaine qui utilisera cet enregistrement A. Par exemple, sélectionnez **www** si vous souhaitez créer un alias pour **www.domainepersonnalisé.com**. Pour créer une entrée avec des caractères génériques pour l'ensemble des sous-domaines, entrez << **\*** >>. Ceci permet de couvrir tous les sous-domaines tels que **mail.customdomain.com**, **login.customdomain.com** ou **www.domainepersonnalisé.com**.

	Si vous voulez créer un enregistrement A pour le domaine racine, l'entrée correspondante devrait être répertoriée avec le symbole
<< **@** >> dans les outils DNS de votre bureau d'enregistrement.

1.  Entrez l'adresse IP de votre service cloud dans le champ prévu à cet effet. Cette opération permet d'associer le domaine de l'enregistrement A avec l'adresse IP de votre déploiement de service cloud.

Par exemple, l'enregistrement A suivant transfère tout le trafic de **contoso.com** vers **137.135.70.239**, l'adresse IP de votre application déployée :

<table  border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Nom d’hôte/Sous domaine</strong>
</td>

<td><strong>Adresse IP</strong>
</td>

</tr>

<tr>
<td>@</td>

<td>137.135.70.239</td>

</tr>

</table>

Cet exemple montre comment créer un enregistrement A pour le domaine racine. Pour créer une entrée avec des caractères génériques qui couvre l'ensemble des sous-domaines, entrez << **\*** >> comme sous-domaine.

## Étapes suivantes

* [Gestion des services cloud](../cloud-services-how-to-manage/)
* [Mappage du contenu CDN à un domaine personnalisé][4]



[1]: http://en.wikipedia.org/wiki/CNAME_record
[2]: http://tools.ietf.org/html/rfc1035
[3]: https://manage.windowsazure.com
[4]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg680307.aspx