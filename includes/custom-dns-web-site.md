#Configuration d'un nom de domaine personnalisé pour un site web Azure

Quand vous créez un site web, Azure fournit un sous-domaine convivial au sein du domaine azurewebsites.net. Vos utilisateurs peuvent donc accéder à votre site web via une URL de type http://&lt;mysite\>.azurewebsites.net. Toutefois, si vous configurez le mode partagé ou le mode standard pour vos sites web, vous pourrez mapper votre site web à votre propre nom de domaine.

Vous pouvez éventuellement utiliser Azure Traffic Manager pour équilibrer le trafic entrant vers votre site web. Pour plus d'informations sur le fonctionnement de Traffic Manager avec les sites web, consultez [Contrôle du trafic des sites web Azure avec Azure Traffic Manager][trafficmanager].

> [AZURE.NOTE]Les procédures décrites ici s'appliquent aux sites web Azure. Pour les services cloud, consultez <a href="/develop/net/common-tasks/custom-dns/">Configuration d'un nom de domaine personnalisé dans Azure</a>.

> [AZURE.NOTE]La procédure suivante implique que vos sites web soient configurés en mode Partagé ou Standard, ce qui peut influer sur vos frais d'abonnement. Pour plus d'informations, consultez <a href="/pricing/details/web-sites/">Détails de la tarification des sites web</a>.

Dans cet article :

-   [Présentation des enregistrements CNAME et A](#understanding-records)
-   [Configuration de vos sites web en mode Partagé ou Standard](#bkmk_configsharedmode)
-   [Ajout de vos sites web à Traffic Manager](#trafficmanager)
-   [Ajout d’un enregistrement CNAME pour votre domaine personnalisé](#bkmk_configurecname)
-   [Ajout d'un enregistrement A pour votre nom de domaine personnalisé](#bkmk_configurearecord)

<h2><a name="understanding-records"></a>Présentation des enregistrements CNAME et&#160;A</h2>

Bien que fonctionnant différemment, les enregistrements CNAME \(ou enregistrements d'alias\) et A permettent d'associer un nom de domaine à un site web.

###Enregistrement CNAME ou d'alias

Un enregistrement CNAME associe un domaine *spécifique*, tel que **contoso.com** ou **www.contoso.com**, à un nom de domaine canonique. Dans ce cas, le nom de domaine canonique est le nom de domaine **&lt;myapp\>.azurewebsites.net** de votre site web Azure ou le nom de domaine **&lt;myapp\>.trafficmgr.com** de votre profil Traffic Manager. Une fois créé, l'enregistrement CNAME émet un alias pour le nom de domaine **&lt;myapp\>.azurewebsites.net** ou **&lt;myapp\>.trafficmgr.com**. L'entrée CNAME devient automatiquement l'adresse IP de votre nom de domaine **&lt;myapp\>.azurewebsites.net** ou **&lt;myapp\>.trafficmgr.com**. Ainsi, même si l'adresse IP de votre site web change, vous n'avez aucune action à effectuer.

> [AZURE.NOTE]Certains bureaux d’enregistrement de domaines autorisent le mappage de sous-domaines uniquement lorsqu’un enregistrement CNAME est utilisé \(par exemple, www.contoso.com\) et non un nom racine \(tel que contoso.com\). Pour plus d'informations sur les enregistrements CNAME, consultez la documentation fournie par votre bureau d'enregistrement, la <a href="http://en.wikipedia.org/wiki/CNAME_record">page Wikipédia sur l'enregistrement CNAME</a> ou le document <a href="http://tools.ietf.org/html/rfc1035">Noms de domaine IETF - Implémentation et spécification</a>.

###Enregistrement A

Un enregistrement A associe un domaine, tel que **contoso.com** ou **www.contoso.com**, *ou un nom de domaine générique* \(par exemple : **\*.contoso.com**\) à une adresse IP. Dans le cas d'un site web Azure, il s'agit de l'adresse IP virtuelle du service ou d'une adresse IP spécifique achetée pour votre site web. Le principal avantage d'un enregistrement A par rapport à un enregistrement CNAME est que vous pouvez disposer d'une entrée utilisant un caractère générique \(par exemple, ***.contoso.com**\), ce qui permet de gérer les demandes pour plusieurs sous-domaines, tels que **mail.contoso.com**, **login.contoso.com** ou **www.contso.com**.

> [AZURE.NOTE]L'enregistrement A étant associé à une adresse IP statique, les changements d'adresse IP de votre site web ne sont donc pas pris en compte automatiquement. Une adresse IP à utiliser avec les enregistrements A est fournie lorsque vous configurez les paramètres de nom de domaine personnalisé pour votre site web. Cette valeur est toutefois susceptible d'être modifiée si vous supprimez et recréez votre site web ou que vous faites de nouveau passer votre site web en mode Gratuit.

> [AZURE.NOTE]Les enregistrements A ne peuvent pas être utilisés pour équilibrer la charge avec Traffic Manager. Pour plus d’informations, consultez [Contrôle du trafic des sites Web Azure avec Azure Traffic Manager][trafficmanager].
 
<a name="bkmk_configsharedmode"></a><h2>Configuration des sites web pour le mode Partagé ou Standard</h2>

Seuls les modes Partagé et Standard autorisent la définition d'un nom de domaine personnalisé pour les sites web Azure. Avant de faire passer un site web du mode Gratuit au mode Partagé ou Standard, vous devez d'abord supprimer les limites de dépense mises en place pour l'abonnement de votre site web. Pour plus d'informations sur la tarification des modes standard et partagé, consultez la page [Tarification][PricingDetails].

1. Dans votre navigateur, ouvrez le [portail de gestion][portal].
2. Sous l'onglet **Sites web**, cliquez sur le nom de votre site.

	![][standardmode1]

3. Cliquez sur l'onglet **SCALE**.

	![][standardmode2]

	
4. Dans la section **Général**, définissez le mode du site web en cliquant sur **PARTAGÉ**.

	![][standardmode3]

	> [AZURE.NOTE]Si vous comptez utiliser Traffic Manager avec ce site web, vous devez sélectionner le mode Standard au lieu de Partagé.

5. Cliquez sur **Save**.
6. Un message vous informe de l'augmentation des coûts en mode Partagé \(ou en mode Standard si vous avez sélectionné cette option\). Cliquez sur **Yes** si vous acceptez.

	<!--![][standardmode4]-->

	**Remarque**<br /> Si un message d'erreur indiquant l'échec de la mise à l'échelle du site web choisi s'affiche, vous pouvez cliquer sur le bouton des détails pour obtenir plus d'informations.

<a name="trafficmanager"></a><h2>\(Facultatif\) Ajouter vos sites web à Traffic Manager</h2>

Si vous voulez utiliser votre site web avec Traffic Manager, procédez comme suit.

1. Si vous n'avez pas encore de profil Traffic Manager, utilisez les informations de [Création d'un profil Traffic Manager à l'aide de Création rapide][createprofile] pour en créer un. Notez le domaine **.trafficmgr.com** associé à votre profil Traffic Manager. Il sera utilisé dans une étape ultérieure.

2. Utilisez les informations de la rubrique [Ajout ou suppression de points de terminaison][addendpoint] pour ajouter votre site web comme point de terminaison dans votre profil Traffic Manager.

	> [AZURE.NOTE]Si votre site web n'est pas répertorié lors de l'ajout d'un point de terminaison, vérifiez qu'il est configuré pour le mode Standard. Vous devez utiliser le mode Standard pour votre site web si vous voulez utiliser Traffic Manager.

3. Connectez-vous au site web du bureau d'enregistrement de votre DNS et accédez à la page de gestion DNS. Recherchez la mention **Domain Name**, **DNS** ou **Name Server Management**.

4. Maintenant, cherchez où vous pouvez sélectionner ou saisir vos enregistrements CNAME. Il se peut que vous deviez sélectionner le type d’enregistrement dans une liste déroulante ou accéder à une page de paramètres avancés. La section recherchée doit normalement comporter les mots **CNAME**, **Alias** ou **Subdomains**.

5. Vous devez également fournir l’alias de domaine ou de sous-domaine pour l’enregistrement CNAME. Par exemple, **www** si vous souhaitez créer un alias pour **www.domainepersonnalisé.com**.

5. Vous devez également fournir un nom d'hôte correspondant au nom de domaine canonique pour cet alias CNAME. Il s'agit du nom **.trafficmgr.com** pour votre site Web.

Par exemple, l'enregistrement CNAME suivant renvoie tout le trafic de **www.contoso.com** vers **contoso.trafficmgr.com**, le nom de domaine d'un site web :

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Nom d'hôte/Sous-domaine</strong></td>
<td><strong>Domaine canonique</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.trafficmgr.com</td>
</tr>
</table>

Un utilisateur consultant le site **www.contoso.com** ne verra jamais l’adresse de l’hôte réel \(contoso.azurewebsite.net\). Le processus de transfert est donc invisible pour l’utilisateur final.

> [AZURE.NOTE]Si vous utilisez Traffic Manager avec un site web, vous ne devez pas suivre les étapes des sections suivantes, « Ajout d'un enregistrement CNAME pour votre nom de domaine personnalisé » et « Ajout d'un enregistrement A pour votre domaine personnalisé ». L'enregistrement CNAME créé dans les étapes précédentes acheminera le trafic entrant vers Traffic Manager, qui l'acheminera vers le ou les points de terminaison du site web.

<a name="bkmk_configurecname"></a><h2>Ajout d'un enregistrement CNAME pour votre nom de domaine personnalisé</h2>

Pour créer un enregistrement CNAME, vous devez ajouter une nouvelle entrée dans la table DNS de votre domaine personnalisé en utilisant les outils fournis par votre bureau d’enregistrement. Chaque bureau d'enregistrement possède sa propre méthode de spécification des enregistrements CNAME, même si le fonctionnement général reste souvent similaire.

1. Utilisez une des méthodes suivantes pour rechercher le nom de domaine **.azurewebsite.net** attribué à votre site web.

	* Connectez-vous au [portail de gestion Azure][portal], sélectionnez votre site web, sélectionnez **Tableau de bord**, puis cherchez l'entrée **URL du site** dans la section **aperçu rapide**.

	* Installez et configurez [Azure Powershell](/manage/install-and-configure-windows-powershell/), puis utilisez la commande suivante :

			get-azurewebsite yoursitename | select hostnames

	* Installez et configurez l'[interface de ligne de commande interplateforme Azure](/manage/install-and-configure-cli/), puis utilisez la commande suivante :

			azure site domain list yoursitename

	Conservez ce nom **.azurewebsite.net** ; il sera utilisé aux étapes suivantes.

3. Connectez-vous au site web du bureau d'enregistrement de votre DNS et accédez à la page de gestion DNS. Recherchez la mention **Domain Name**, **DNS** ou **Name Server Management**.

4. Maintenant, cherchez où vous pouvez sélectionner ou saisir vos enregistrements CNAME. Il se peut que vous deviez sélectionner le type d’enregistrement dans une liste déroulante ou accéder à une page de paramètres avancés. La section recherchée doit normalement comporter les mots **CNAME**, **Alias** ou **Subdomains**.

5. Vous devez également fournir l’alias de domaine ou de sous-domaine pour l’enregistrement CNAME. Par exemple, **www** si vous souhaitez créer un alias pour **www.domainepersonnalisé.com**. Si vous voulez créer un alias pour le domaine racine, l'entrée correspondante peut être répertoriée avec le symbole '\*\*@\*\* dans les outils DNS de votre bureau d'enregistrement.

5. Vous devez également fournir un nom d'hôte correspondant au nom de domaine canonique pour cet alias CNAME. Il s'agit du nom **.azurewebsite.net** pour votre site web.

Par exemple, l'enregistrement CNAME suivant renvoie tout le trafic de **www.contoso.com** vers **contoso.azurewebsite.net**, le nom de domaine d'un site web :

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Nom d'hôte/Sous-domaine</strong></td>
<td><strong>Domaine canonique</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.azurewebsite.net</td>
</tr>
</table>

Un utilisateur consultant le site **www.contoso.com** ne verra jamais l’adresse de l’hôte réel \(contoso.azurewebsite.net\). Le processus de transfert est donc invisible pour l’utilisateur final.

> [AZURE.NOTE]L'exemple ci-dessus s'applique uniquement au trafic du sous-domaine __www__. Puisqu'il n'est pas possible d'utiliser des caractères génériques pour les enregistrements CNAME, vous devez créer un enregistrement CNAME pour chaque domaine/sous-domaine. Pour rediriger le trafic de sous-domaines tels que *.contoso.com vers votre adresse azurewebsite.net, vous pouvez configurer une entrée de __redirection d'URL__ ou de __transfert d'URL__ dans vos paramètres DNS. Vous pouvez également créer un enregistrement A.

> [AZURE.NOTE]La propagation de l'enregistrement CNAME dans le système DNS peut prendre du temps. Vous ne pouvez pas définir l'enregistrement CNAME pour le site web avant la propagation de l'enregistrement CNAME. Vous pouvez utiliser un service tel que <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> pour vérifier que l'enregistrement CNAME est disponible.

###Ajout du nom de domaine à votre site web

Une fois la propagation de l'enregistrement CNAME comme nom de domaine terminée, vous devez associer cet enregistrement à votre site web. Vous pouvez ajouter le nom de domaine personnalisé défini par l'enregistrement CNAME à votre site web en utilisant l'interface de ligne de commande interplateforme Azure ou le portail de gestion Azure.

**Ajout d'un nom de domaine via les outils en ligne de commande**

Installez et configurez l'[interface de ligne de commande interplateforme Azure](/manage/install-and-configure-cli/), puis utilisez la commande suivante :

	azure site domain add customdomain yoursitename

Par exemple, la ligne suivante ajoute le nom de domaine personnalisé **www.contoso.com** au site web **contoso.azurewebsite.net** :

	azure site domain add www.contoso.com contoso

Vous pouvez vérifier que le nom de domaine personnalisé a été ajouté au site web en utilisant la commande suivante :

	azure site domain list yoursitename

La liste renvoyée par cette commande doit contenir le nom de domaine personnalisé, ainsi que l'entrée par défaut **.azurewebsite.net**.

**Ajout d'un nom de domaine via le portail de gestion Azure**

1. Dans votre navigateur, ouvrez le [portail de gestion Azure][portal].

2. Sous l'onglet **Sites Web**, cliquez sur le nom de votre site, sélectionnez **Tableau de bord**, puis **Gérer les domaines** en bas de la page.

	![][setcname2]

6. Dans la zone de texte **DOMAIN NAMES**, entrez le nom de domaine que vous avez configuré.

	![][setcname3]

6. Cliquez sur la coche pour accepter le nom de domaine.

Une fois la configuration terminée, le nom de domaine personnalisé est répertorié dans la section **noms de domaines** de la page **Configurer** de votre site web.

<a name="bkmk_configurearecord"></a><h2>Ajout d'un enregistrement A pour votre nom de domaine personnalisé</h2>

Pour créer un enregistrement A, vous devez tout d'abord connaître l'adresse IP de votre site web. Ajoutez ensuite une entrée dans la table DNS de votre domaine personnalisé à l'aide des outils fournis par votre bureau d'enregistrement. Chaque bureau d’enregistrement possède sa propre méthode de spécification des enregistrements A, même si le fonctionnement général reste souvent similaire. Lors de la création d’un enregistrement A, vous devez également créer un enregistrement CNAME qui sera utilisé par Azure pour vérifier l’enregistrement A.

1. Dans votre navigateur, ouvrez le [portail de gestion Azure][portal].

2. Sous l'onglet **Sites Web**, cliquez sur le nom de votre site, sélectionnez **Tableau de bord**, puis **Gérer les domaines** en bas de l'écran.

	![][setcname2]

5. Dans la boîte de dialogue **Manage custom domains**, recherchez la mention **The IP Address to use when configuring A records**. Copiez l’adresse IP. Elle sera utilisée lors de la création de l’enregistrement A.

5. Dans la boîte de dialogue **Manage custom domains**, notez le nom de domaine awverify situé à la fin du texte en haut de la boîte de dialogue. Cette adresse doit normalement être **awverify.monsite.azurewebsites.net** \(remplacez **monsite** par le nom de votre site web\). Copiez cette adresse, car il s’agit du nom de domaine qui sera utilisé lors de la création de l’enregistrement CNAME de vérification.

6. Connectez-vous au site web du bureau d'enregistrement de votre DNS et accédez à la page de gestion DNS. Recherchez la mention **Domain Name**, **DNS** ou **Name Server Management**.

6. Cherchez à présent où vous pouvez sélectionner ou saisir vos enregistrements A et CNAME. Il se peut que vous deviez sélectionner le type d’enregistrement dans une liste déroulante ou accéder à une page de paramètres avancés.

7. Procédez comme suit pour créer l’enregistrement A :

	1. Sélectionnez ou entrez le domaine ou sous-domaine qui utilisera l’enregistrement A. Par exemple, sélectionnez **www** si vous souhaitez créer un alias pour **www.domainepersonnalisé.com**. Pour créer une entrée avec des caractères génériques pour l'ensemble des sous-domaines, entrez « \_\_\*\_\_ ». Ceci permet de couvrir tous les sous-domaines tels que **mail.customdomain.com**, **login.customdomain.com** ou **www.domainepersonnalisé.com**.

		If you want to create an A record for the root domain, it may be listed as the '**@**' symbol in your registrar's DNS tools.

	2. Entrez l'adresse IP de votre service cloud dans le champ prévu à cet effet. Cette opération permet d’associer le domaine de l’enregistrement A avec l’adresse IP de votre déploiement de service cloud.

		For example, the following A record forwards all traffic from **contoso.com** to **137.135.70.239**, the IP address of our deployed application:

		<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
		<tr>
		<td><strong>Host name/Subdomain</strong></td>
		<td><strong>IP address</strong></td>
		</tr>
		<tr>
		<td>@</td>
		<td>137.135.70.239</td>
		</tr>
		</table>

		This example demonstrates creating an A record for the root domain. If you wish to create a wildcard entry to cover all subdomains, you would enter '__*__' as the subdomain.

7. Créez ensuite un enregistrement CNAME, avec **awverify** comme alias et **awverify.monsite.azurewebsites.net** comme domaine canonique \(obtenu plus tôt\).

	> [AZURE.NOTE]L'alias awverify peut fonctionner pour certains bureaux d'enregistrement ; d'autres peuvent demander le nom de domaine d'alias complet \(awverify.www.nomdedomainepersonnalisé.com ou awverify.nomdedomainepersonnalisé.com\).

	Par exemple, les informations suivantes permettent de créer un enregistrement CNAME utilisable par Azure pour vérifier la configuration de l’enregistrement A.

	<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Nom d'hôte/Sous-domaine</strong></td>
<td><strong>Domaine canonique</strong></td>
</tr>
<tr>
<td>awverify</td>
<td>awverify.contoso.azurewebsites.net</td>
</tr>
</table>

> [AZURE.NOTE]La propagation de l'enregistrement CNAME awverify dans le système DNS peut prendre du temps. Vous ne pouvez pas associer le nom de domaine personnalisé défini dans l'enregistrement A avec le site web avant la propagation de l'enregistrement CNAME awverify. Vous pouvez utiliser un service tel que <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> pour vérifier que l'enregistrement CNAME est disponible.

###Ajout du nom de domaine à votre site web

Une fois la propagation de l'enregistrement CNAME **awverify** pour le nom de domaine terminée, vous pouvez associer le domaine personnalisé défini par l'enregistrement A à votre site web. Vous pouvez ajouter le nom de domaine personnalisé défini par l'enregistrement A à votre site web en utilisant l'interface de ligne de commande interplateforme Azure ou le portail de gestion Azure.

**Ajout d'un nom de domaine via les outils en ligne de commande**

Installez et configurez l'[interface de ligne de commande interplateforme Azure](/manage/install-and-configure-cli/), puis utilisez la commande suivante :

	azure site domain add customdomain yoursitename

Par exemple, la ligne suivante ajoute le nom de domaine personnalisé **contoso.com** au site web **contoso.azurewebsite.net** :

	azure site domain add contoso.com contoso

Vous pouvez vérifier que le nom de domaine personnalisé a été ajouté au site web en utilisant la commande suivante :

	azure site domain list yoursitename

La liste renvoyée par cette commande doit contenir le nom de domaine personnalisé, ainsi que l’entrée par défaut **.azurewebsite.net**.

**Ajout d'un nom de domaine via le portail de gestion Azure**

1. Dans votre navigateur, ouvrez le [portail de gestion Azure][portal].

2. Sous l'onglet **Sites Web**, cliquez sur le nom de votre site, sélectionnez **Tableau de bord**, puis **Gérer les domaines** en bas de la page.

	![][setcname2]

6. Dans la zone de texte **DOMAIN NAMES**, entrez le nom de domaine que vous avez configuré.

	![][setcname3]

6. Cliquez sur la coche pour accepter le nom de domaine.

Une fois la configuration terminée, le nom de domaine personnalisé est répertorié dans la section **noms de domaines** de la page **Configurer** de votre site web.

> [AZURE.NOTE]Après avoir ajouté le nom de domaine personnalisé défini par l'enregistrement A à votre site web, vous pouvez supprimer l'enregistrement CNAME awverify à l'aide des outils fournis par votre bureau d'enregistrement. Toutefois, si vous voulez ajouter un autre enregistrement A ultérieurement, vous devrez créer un enregistrement awverify avant de pouvoir associer le nouveau nom de domaine défini par le nouvel enregistrement A au site web.

## Étapes suivantes

-   [Gestion des sites web](/manage/services/web-sites/how-to-manage-websites/)

-   [Configuration d'un certificat SSL pour les sites web](/develop/net/common-tasks/enable-ssl-web-site/)


<!-- Bookmarks -->

[Configure your web sites for shared mode]: #bkmk_configsharedmode
[Configure the CNAME on your domain registrar]: #bkmk_configurecname
[Configure a CNAME verification record on your domain registrar]: #bkmk_configurecname
[Configure an A record for the domain name]: #bkmk_configurearecord
[Set the domain name in management portal]: #bkmk_setcname

<!-- Links -->

[PricingDetails]: /pricing/details/
[portal]: http://manage.windowsazure.com
[digweb]: http://www.digwebinterface.com/
[cloudservicedns]: ../articles/custom-dns.md
[trafficmanager]: ../articles/web-sites-traffic-manager.md
[addendpoint]: http://msdn.microsoft.com/library/windowsazure/hh744839.aspx
[createprofile]: http://msdn.microsoft.com/library/windowsazure/dn339012.aspx

<!-- images -->

[setcname1]: ../media/dncmntask-cname-5.png


<!-- images -->
[standardmode1]: ./media/custom-dns-web-site/dncmntask-cname-1.png
[standardmode2]: ./media/custom-dns-web-site/dncmntask-cname-2.png
[standardmode3]: ./media/custom-dns-web-site/dncmntask-cname-3.png
[standardmode4]: ./media/custom-dns-web-site/dncmntask-cname-4.png


[setcname2]: ./media/custom-dns-web-site/dncmntask-cname-6.png
[setcname3]: ./media/custom-dns-web-site/dncmntask-cname-7.png

<!--HONumber=52-->
