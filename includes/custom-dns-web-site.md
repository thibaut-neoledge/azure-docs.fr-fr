
# Configuration d'un nom de domaine personnalisé pour un site Web Azure

Lorsque vous créez un site Web, Azure fournit un nom de sous-domaine convivial au sein du domaine azurewebsites.net. Ainsi, vos utilisateurs peuvent accéder à votre site Web en tapant une URL de type http://<monsite>.azurewebsites.net. Toutefois, si vous configurez vos sites Web en mode Partagé ou Standard, vous pouvez mapper votre site Web à votre nom de domaine.

Vous pouvez éventuellement utiliser Azure Traffic Manager pour équilibrer le trafic entrant vers votre site Web. Pour plus d'informations sur le fonctionnement du Traffic Manager avec les sites Web, consultez [Contrôle du trafic des sites Web Azure avec Azure Traffic Manager](/fr-fr/documentation/articles/web-sites-traffic-manager/).

> [WACOM.NOTE] Les procédures décrites ici s'appliquent aux sites Web Azure. Pour les services cloud, consultez la page [Configuration d'un nom de domaine personnalisé dans Azure][1].

> [WACOM.NOTE] La procédure suivante implique que vos sites Web soient configurés en mode Partagé ou Standard, ce qui peut influer sur vos frais d'abonnement. Pour plus d'informations, consultez la page [Tarification des sites Web][2].

Dans cet article :

* [Présentation des enregistrements CNAME et A](#understanding-records)
* [Configuration des sites Web pour le mode Partagé ou
  Standard](#bkmk_configsharedmode)
* [Ajout de vos sites Web à Traffic Manager](#trafficmanager)
* [Ajout d'un enregistrement CNAME pour votre nom de domaine
  personnalisé](#bkmk_configurecname)
* [Ajout d'un enregistrement A pour votre nom de domaine
  personnalisé](#bkmk_configurearecord)

<h2><a name="understanding-records"></a>Présentation des enregistrements CNAME et A</h2>


Bien que fonctionnant différemment, les enregistrements CNAME (ou enregistrements d'alias) et A permettent d'associer un nom de domaine avec un site Web.

### Enregistrement CNAME ou d'alias

Un enregistrement CNAME associe un domaine *spécifique*, tel que **contoso.com** ou **www.contoso.com**, à un nom de domaine canonique. Dans ce cas, le nom de domaine canonique est le nom de domaine **<monapp>.azurewebsites.net** de votre site Web Azure ou le nom de domaine **<monapp>.trafficmgr.com** de votre profil Traffic Manager. Une fois créé, l'enregistrement CNAME émet un alias pour le nom de domaine **<monapp>.azurewebsites.net** ou **<monapp>.trafficmgr.com**. L'entrée CNAME devient automatiquement l'adresse IP de votre nom de domaine
**<monapp>.azurewebsites.net** ou **<monapp>.trafficmgr.com**. Ainsi, même si l'adresse IP de votre site Web change, vous n'avez aucune action à effectuer.

> [WACOM.NOTE] Certains bureaux d'enregistrement autorisent le mappage de sous-domaines uniquement lorsqu'un enregistrement CNAME est utilisé (par exemple, www.contoso.com) et non un nom racine, (tel que contoso.com). Pour plus d'informations sur les enregistrements CNAME, consultez la documentation remise par votre bureau d'enregistrement, [la page Wikipedia sur l'enregistrement CNAME][3] ou le document [IETF Domain Names -Implementation and Specification][4].

### Enregistrement A

Un enregistrement A associe un domaine, tel que **contoso.com** ou **www.contoso.com**, *ou un nom de domaine générique* (par exemple : **\*.contoso.com**) à une adresse IP. Dans le cas d'un site Web Azure, l'adresse IP virtuelle du service ou une adresse IP spécifique achetée pour votre site Web. Le principal avantage d'un enregistrement A par rapport à un enregistrement CNAME est que vous pouvez disposer d'une entrée utilisant un caractère générique (par exemple, **\*.contoso.com**), ce qui permet de gérer les demandes pour plusieurs
sous-domaines, tels que **mail.contoso.com**, **login.contoso.com** ou **www.contso.com**.

> [WACOM.NOTE] L'enregistrement A étant associé à une adresse IP statique, les changements d'adresse IP de votre site Web ne sont donc pas pris en compte automatiquement. Une adresse IP à utiliser avec les enregistrements A est fournie lors de la configuration des paramètres de nom de domaine personnalisé de votre site Web. Cette valeur est toutefois susceptible d'être modifiée si vous supprimez et recréez votre site Web ou que vous passez à nouveau votre site Web en mode Gratuit.

> [WACOM.NOTE] Les enregistrements A ne peuvent pas être utilisé pour équilibrer la charge avec Traffic Manager. Pour plus d'informations, consultez [Contrôle du trafic des sites Web Azure avec Azure Traffic Manager](/fr-fr/documentation/articles/web-sites-traffic-manager/).

<a name="bkmk_configsharedmode"></a>
<h2>Configuration de vos sites Web en mode Partagé ou Standard</h2>

Seuls le mode Partagé ou Standard autorise la définition d'un nom de domaine personnalisé pour les sites Web Azure. Avant de passer un site Web du mode Gratuit au mode Partagé ou Standard, vous devez d'abord supprimer les limites de dépense mises en place pour l'abonnement de votre site Web. Pour plus d'informations sur la tarification des modes standard et partagé, consultez la page [Tarification][5].

1.  Dans votre navigateur, ouvrez le [portail de gestion][6]. 
2.  Sous l'onglet **Web Sites**, cliquez sur le nom de votre site.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-1.png)

3.  Cliquez sur l'onglet **SCALE**.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-2.png)

4.  Dans la section **general**, définissez le mode du site Web en
    cliquant sur **SHARED**.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-3.png)


   
    > [WACOM.NOTE] Si vous comptez utiliser Traffic Manager avec ce site Web, vous devez sélectionner le mode Standard plutôt que Partagé.

5.  Cliquez sur **Save**. 
6.  Un message vous informe de l'augmentation des coûts en mode Partagé (ou en mode Standard si vous avez sélectionné cette option). Cliquez sur **Yes** si vous acceptez.
    
    <!--![][standardmode4]-->
    
    **Remarque**  
    Si un message indiquant une erreur de mise à l'échelle pour le site Web choisi s'affiche, vous pouvez cliquer sur le bouton de détails pour obtenir plus d'informations.

<a name="trafficmanager"></a>
<h2>(Facultatif) Ajout de vos sites Web à Traffic
Manager</h2>

Si vous voulez utiliser votre site Web avec Traffic Manager, procédez comme suit.

1.  Si vous n'avez pas encore de profil Traffic Manager, utilisez les informations de [Création d'un profil Traffic Manager à l'aide de Quick Create][7] pour en créer un. Notez le domaine **.trafficmgr.com** associé à votre profil Traffic Manager. Il sera utilisé dans une étape ultérieure.

2.  Utilisez les informations de [Ajout ou suppression de points de terminaison][8] pour ajouter votre site Web comme point de terminsaison dans votre profil Traffic Manager.


   
    > [WACOM.NOTE] Si votre site Web n'est pas répertorié lors de l'ajout d'un point de terminaison, vérifiez qu'il est configuré pour le mode Standard. Vous devez utiliser le mode Standard pour votre site Web si vous voulez utiliser Traffic Manager.

3.  Connectez-vous au site Web du bureau d'enregistrement de votre DNS et accédez à la section de gestion DNS. Recherchez la mention **Domain Name**, **DNS** ou **Name Server Management**.

4.  Maintenant, cherchez où vous pouvez sélectionner ou saisir vos enregistrements CNAME. Il se peut que vous deviez sélectionner le type d'enregistrement dans une liste déroulante ou accéder à une page de paramètres avancés. La section recherchée doit normalement comporter les mots **CNAME**, **Alias** ou **Subdomains**.

5.  Vous devez également fournir l'alias de domaine ou de sous-domaine pour l'enregistrement CNAME. Par exemple, **www** si vous souhaitez créer un alias pour **www.domainepersonnalisé.com**.

6.  Vous devez également fournir un nom d'hôte correspondant au nom de domaine canonique pour cet alias CNAME. Il s'agit du nom **.trafficmgr.com** de votre site Web.

Par exemple, l'enregistrement CNAME suivant renvoie tout le trafic de **www.contoso.com** vers **contoso.trafficmgr.com**, le nom de domaine d'un site Web :

<table  border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Nom d’hôte/Sous-domaine</strong>
</td>

<td><strong>Domaine canonique</strong>
</td>

</tr>

<tr>
<td>www</td>

<td>contoso.trafficmgr.com</td>

</tr>

</table>

Un utilisateur consultant le site **www.contoso.com** ne verra jamais l'adresse de l'hôte réel (contoso.azurewebsite.net). Le processus de transfert est donc invisible pour l'utilisateur final.

> [WACOM.NOTE] Si vous utilisez Traffic Manager avec un site Web, vous ne devez pas suivre les étapes des sections suivantes, << **Ajout d'un enregistrement CNAME pour votre nom de domaine personnalisé** >> et << **Ajout d'un enregistrement A pour votre domaine personnalisé** >>. L'enregistrement CNAME créé dans les étapes précédentes acheminera le trafic entrant vers Traffic Manager, qui l'acheminera vers le ou les points de terminaison du site Web.

<a name="bkmk_configurecname"></a><h2>Ajout d'un enregistrement CNAME pour votre
domaine personnalisé</h2>

Pour créer un enregistrement CNAME, vous devez ajouter une nouvelle entrée dans la table DNS de votre domaine personnalisé en utilisant les outils fournis par votre bureau d'enregistrement. Chaque bureau d'enregistrement possède sa propre méthode de spécification des enregistrements CNAME, même si le fonctionnement général reste souvent similaire.

1.  Employez une des méthodes suivantes pour connaître le nom de domaine **.azurewebsite.net** attribué à votre site Web.
    
    * Connectez-vous au [portail de gestion Azure][6], sélectionnez votre site Web, sélectionnez **Dashboard**, puis cherchez l'entrée **Site URL** dans la section **quick glance**.
    
    * Installez et configurez [Azure Powershell][9], puis utilisez la commande suivante :
      
            get-azurewebsite nomdevotresite | select hostnames
    
    * Installez et configurez l'[interface de ligne de commande interplateforme Azure][10], puis utilisez la commande suivante :
      
          azure site domain list nomdevotresite
    
    Conservez ce nom **.azurewebsite.net** ; il sera utilisé aux étapes suivantes.

2.  Connectez-vous au site Web du bureau d'enregistrement de votre DNS et accédez à la section de gestion DNS. Recherchez la mention **Domain Name**, **DNS** ou **Name Server Management**.

3.  Maintenant, cherchez où vous pouvez sélectionner ou saisir vos enregistrements CNAME. Il se peut que vous deviez sélectionner le type d'enregistrement dans une liste déroulante ou accéder à une page de paramètres avancés. La section recherchée doit normalement comporter les mots **CNAME**, **Alias** ou **Subdomains**.

4.  Vous devez également fournir l'alias de domaine ou de sous-domaine pour l'enregistrement CNAME. Par exemple, **www** si vous souhaitez créer un alias pour **www.domainepersonnalisé.com**. Si vous voulez créer un alias pour le domaine racine, l'entrée correspondante devrait être répertoriée avec le symbole << **@** >> dans les outils DNS de votre bureau d'enregistrement.

5.  Vous devez également fournir un nom d'hôte correspondant au nom de domaine canonique pour cet alias CNAME. Il s'agit du nom **.azurewebsite.net** de votre site Web.

Par exemple, l'enregistrement CNAME suivant renvoie tout le trafic de **www.contoso.com** vers **contoso.azurewebsite.net**, le nom de domaine d'un site Web :

<table  border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Nom d’hôte/Sous-domaine</strong>
</td>

<td><strong>Domaine canonique</strong>
</td>

</tr>

<tr>
<td>www</td>

<td>contoso.azurewebsite.net</td>

</tr>

</table>

Un utilisateur consultant le site **www.contoso.com** ne verra jamais l'adresse de l'hôte réel (contoso.azurewebsite.net). Le processus de transfert est donc invisible pour l'utilisateur final.

> [WACOM.NOTE] L'exemple ci-dessus s'applique uniquement au trafic sur le sous-domaine **www**. Puisqu'il n'est pas possible d'utiliser des caractères génériques pour les enregistrements CNAME, vous devez créer un enregistrement CNAME pour chaque domaine/sous-domaine. Pourrediriger le trafic de sous-domaines tels que \*.contoso.com vers votre adresse azurewebsite.net, vous pouvez configurer une entrée de **redirection d'URL** ou de **transfert d'URL** dans vos paramètres DNS. Vous pouvez également créer un enregistrement A.

> [WACOM.NOTE] La propagation de l'enregistrement CNAME dans le système DNS peut prendre du temps. Vous ne pouvez pas définir l'enregistrement CNAME pour le site Web avant la propagation de l'enregistrement CNAME. Vous pouvez utiliser un service tel que [http://www.digwebinterface.com/][11] pour vérifier que l'enregistrement CNAME est disponible.

### Ajout du nom de domaine à votre site Web

Une fois la propagation de l'enregistrement CNAME comme nom de domaine terminée, vous devez associer cet enregistrement à votre site. Vous pouvez ajouter le nom de domaine personnalisé défini par l'enregistrement CNAME à votre site Web en utilisant l'interface de ligne de commande interplateforme Azure ou le portail de gestion Azure.

**Ajout d'un nom de domaine via les outils en ligne de commande**

Installez et configurez l'[interface de ligne de commande interplateforme Azure][10], puis utilisez la commande suivante :

    azure site domain add domainepersonnalisé nomdevotresite

Par exemple, la ligne suivante ajoute le nom de domaine personnalisé **www.contoso.com** au site Web **contoso.azurewebsite.net** :

    azure site domain add www.contoso.com contoso

Vous pouvez confirmer que le nom de domaine personnalisé a été ajouté au site Web en utilisant la commande suivante :

    azure site domain list nomdevotresite

La liste renvoyée par cette commande doit contenir le nom de domaine personnalisé, ainsi que l'entrée par défaut **.azurewebsite.net**.

**Ajout d'un nom de domaine via le portail de gestion Azure**

1.  Dans votre navigateur, ouvrez le [portail de gestion Azure][6].

2.  Dans l'onglet **Web Sites**, cliquez sur le nom de votre site, sélectionnez **Dashboard**, puis **Manage Domains** en bas de la page.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3.  Dans la zone de texte **DOMAIN NAMES**, entrez le nom de domaine que vous avez configuré.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4.  Cliquez sur la coche pour accepter le nom de domaine.

Une fois la configuration terminée, le nom de domaine personnalisé est répertorié dans la section **domain names** de la page **Configure** de votre site Web.

<a name="bkmk_configurearecord"></a><h2>Ajout d'un enregistrement A pour votre domaine personnalisé</h2>

Pour créer un enregistrement A, vous devez tout d'abord connaître l'adresse IP de votre site Web. Ajoutez ensuite une entrée dans la table DNS de votre domaine personnalisé à l'aide des outils fournis par votre bureau d'enregistrement. Chaque bureau d'enregistrement possède sa propre méthode de spécification des enregistrements A, même si le fonctionnement général reste souvent similaire. Lors de la création d'un enregistrement A, vous devez également créer un enregistrement CNAME qui sera utilisé par Azure pour vérifier l'enregistrement A.

1.  Dans votre navigateur, ouvrez le [portail de gestion Azure][6].

2.  Dans l'onglet **Web Sites**, cliquez sur le nom de votre site, sélectionnez **Dashboard**, puis **Manage Domains** en bas de l'écran.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3.  Dans la boîte de dialogue **Manage custom domains**, recherchez la mention **The IP Address to use when configuring A records**. Copiez l'adresse IP. Elle sera utilisée lors de la création de l'enregistrement A.

4.  Dans la boîte de dialogue **Manage custom domains**, notez le nom de domaine awverify situé à la fin du texte en haut de la boîte de dialogue. Cette adresse doit normalement être **awverify.monsite.azurewebsites.net** (remplacez **monsite** par le nom de votre site Web). Copiez cette adresse, car il s'agit du nom de domaine qui sera utilisé lors de la création de l'enregistrement CNAME de vérification.

5.  Connectez-vous au site Web du bureau d'enregistrement de votre DNS et accédez à la section de gestion DNS. Recherchez la mention **Domain Name**, **DNS** ou **Name Server Management**.

6.  Cherchez à présent où vous pouvez sélectionner ou saisir vos enregistrements A et CNAME. Il se peut que vous deviez sélectionner le type d'enregistrement dans une liste déroulante ou accéder à une page de paramètres avancés.

7.  Procédez comme suit pour créer l'enregistrement A :
    
    1.  Sélectionnez ou entrez le domaine ou sous-domaine qui utilisera l'enregistrement A. Par exemple, sélectionnez **www** si vous souhaitez créer un alias pour **www.domainepersonnalisé.com**. Pour créer une entrée avec des caractères génériques pour l'ensemble des sous-domaines, entrez << **\*** >>. Ceci permet de couvrir tous les sous-domaines tels que **mail.customdomain.com**, **login.customdomain.com** ou **www.domainepersonnalisé.com**.
        
        Si vous voulez créer un enregistrement A pour le domaine racine, l'entrée correspondante devrait être répertoriée avec le symbole << **@** >> dans les outils DNS de votre bureau d'enregistrement.
    
    2.  Entrez l'adresse IP de votre service cloud dans le champ prévu à cet effet. Cette opération permet d'associer le domaine de l'enregistrement A avec l'adresse IP de votre déploiement de service cloud.
        
        Par exemple, l'enregistrement A suivant transfère tout le trafic de **contoso.com** vers **137.135.70.239**, l'adresse IP de notre application déployée :
        
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

8.  Créez ensuite un enregistrement CNAME, avec **awverify** comme alias et **awverify.monsite.azurewebsites.net** comme domaine canonique (obtenu plus tôt).


   
    > [WACOM.NOTE] L'alias awverify peut fonctionner pour certains bureaux d'enregistrement ; d'autres peuvent demander le nom de domaine d'alias complet (awverify.www.nomdedomainepersonnalisé.com ou awverify.nomdedomainepersonnalisé.com).
    
    Par exemple, les informations suivantes permettent de créer un enregistrement CNAME utilisable par Azure pour vérifier la configuration de l'enregistrement A.
    
    <table  border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
     <tr>
     <td><strong>Alias/Nom d’hôte/Sous-domaine</strong>
    </td>
    
     <td><strong>Domaine canonique</strong>
    </td>
    
     </tr>
    
     <tr>
     <td>awverify</td>
    
     <td>awverify.contoso.azurewebsites.net</td>
    
     </tr>
    
     </table>

> [WACOM.NOTE] La propagation de votre enregistrement CNAME dans le système DNS peut prendre du temps. Vous ne pouvez pas associer le nom de domaine personnalisé défini dans l'enregistrement A avec le site Web avant la propagation de l'enregistrement CNAME awverify. Vous pouvez utiliser un service tel que [http://www.digwebinterface.com/][11] pour vérifier que l'enregistrement CNAME est disponible.

### Ajout du nom de domaine à votre site Web

Une fois la propagation de l'enregistrement CNAME **awverify** comme nom de domaine terminée, vous pouvez associer le domaine personnalisé défini par l'enregistrement A avec votre site Web. Vous pouvez ajouter le nom de domaine personnalisé défini par l'enregistrement A à votre site Web en utilisant l'interface de ligne de commande interplateforme Azure ou le portail de gestion Azure.

**Ajout d'un nom de domaine via les outils en ligne de commande**

Installez et configurez l'[interface de ligne de commande interplateforme Azure][10], puis utilisez la commande suivante :

    azure site domain add domainepersonnalisé nomdevotresite

Par exemple, la ligne suivante ajoute le nom de domaine personnalisé **contoso.com** au site Web **contoso.azurewebsite.net** :

    azure site domain add contoso.com contoso

Vous pouvez confirmer que le nom de domaine personnalisé a été ajouté au site Web en utilisant la commande suivante :

    azure site domain list nomdevotresite

La liste renvoyée par cette commande doit contenir le nom de domaine personnalisé, ainsi que l'entrée par défaut **.azurewebsite.net**.

**Ajout d'un nom de domaine via le portail de gestion Azure**

1.  Dans votre navigateur, ouvrez le [portail de gestion Azure][6].

2.  Dans l'onglet **Web Sites**, cliquez sur le nom de votre site, sélectionnez **Dashboard**, puis **Manage Domains** en bas de la page.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3.  Dans la zone de texte **DOMAIN NAMES**, entrez le nom de domaine que vous avez configuré.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4.  Cliquez sur la coche pour accepter le nom de domaine.

Une fois la configuration terminée, le nom de domaine personnalisé est répertorié dans la section **domain names** de la page **Configure** de votre site Web.

> [WACOM.NOTE] Après avoir ajouté le nom de domaine personnalisé défini par l'enregistrement A à votre site Web, vous pouvez supprimer l'enregistrement CNAME awverify à l'aide des outils fournis par votre bureau d'enregistrement. Toutefois, si vous souhaitez ajouter un autre enregistrement A ultérieurement, vous devrez créer un enregistrement awverify avant de pouvoir associer le nouveau nom de domaine défini par le nouvel enregistrement A avec le site Web.

## Étapes suivantes

* [Gestion des sites Web][12]

* [Configuration d'un certificat SSL pour les sites Web][13]

<!-- Bookmarks -->

<!-- Links -->

<!-- images -->

<!-- images -->



[1]: http://www.windowsazure.com/fr-fr/develop/net/common-tasks/custom-dns/
[2]: http://www.windowsazure.com/fr-fr/pricing/details/web-sites/
[3]: http://en.wikipedia.org/wiki/CNAME_record
[4]: http://tools.ietf.org/html/rfc1035
[5]: https://www.windowsazure.com/en-us/pricing/details/
[6]: http://manage.windowsazure.com
[7]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn339012.aspx
[8]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh744839.aspx
[9]: http://www.windowsazure.com/fr-fr/manage/install-and-configure-windows-powershell/
[10]: http://www.windowsazure.com/fr-fr/manage/install-and-configure-cli/
[11]: http://www.digwebinterface.com/
[12]: http://www.windowsazure.com/fr-fr/manage/services/web-sites/how-to-manage-websites/
[13]: http://www.windowsazure.com/fr-fr/develop/net/common-tasks/enable-ssl-web-site/