<properties urlDisplayName="Create a Global Web Presence on Azure Websites" pageTitle="Créer une présence sur le web à l'international dans Sites Web Azure" metaKeywords="" description="This guide provides a technical overview of how to host your organization's (.COM) site on Azure Websites. This includes deployment, custom domains, SSL, and monitoring." metaCanonical="http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-global-web-presence-solution-overview/" services="" documentationCenter="" title="Create a Global Web Presence on Azure Websites" authors="jroth" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/01/2014" ms.author="jroth" />





# Créer une présence sur le web à l'international dans Sites Web Azure

Ce guide offre un aperçu technique de l'hébergement du site (.COM) de votre organisation sur Azure. Ce scénario est également nommé " présence sur le Web à l'international ". Ce guide aborde l'utilisation d'[Azure Web Sites][websitesoverview], car ce dernier est le moyen le plus rapide et le plus simple de créer, migrer, mettre à l'échelle et gérer une application web sur Azure. Cependant, certaines exigences d'application sont plus adaptées à [Azure Cloud Services][csoverview] ou [Azure Virtual Machines][vmoverview] exécutant IIS, qui représentent également un excellent choix pour l'hébergement d'applications Web. Si vous en êtes aux étapes de planification initiales, consultez le document [Comparaison entre Azure Web Sites, Azure Cloud Services et Azure Virtual Machines][chooseservice]. En l'absence de besoins relatifs à Azure Cloud Services ou Azure Virtual Machines, nous vous recommandons de recourir à Websites pour héberger votre présence sur le web à l'international. La suite de ce document fournit des informations concernant l'utilisation de Websites avec ce scénario. 

Les questions couvertes par ce guide sont les suivantes :

- [Création d'un site web Azure](#createwebsite)
- [Déploiement du site Web](#deploywebsite)
- [Ajout d'un domaine personnalisé](#customdomain)
- [Sécurisation du site Web avec SSL](#ssl)
- [Surveillance du site](#monitor)

> [WACOM.NOTE]
> Ce guide présente les domaines et les tâches parmi les plus courants s'adaptant au développement de sites .COM publics. Cependant, Sites Web Azure offre encore d'autres fonctionnalités, que vous pouvez utiliser pour votre implémentation spécifique. Pour passer en revue ces fonctionnalités, consultez les autres guides <a href="http://www.windowsazure.com/fr-fr/manage/services/web-sites/digital-marketing-campaign-solution-overview">Campagnes de marketing numérique</a> and <a href="http://www.windowsazure.com/fr-fr/manage/services/web-sites/business-application-solution-overview">Applications métier</a>.
> 
> Si vous voulez prendre en main Azure Web Sites avant de créer un compte, accédez à <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, où vous pourrez immédiatement et gratuitement créer un site de départ ASP.NET de courte durée dans Azure Web Sites. Aucune carte de crédit n'est requise, vous ne prenez aucun engagement.

##<a name="createwebsite"></a>Création d'un site web Azure
À l'aide du portail de gestion Azure, vous pouvez créer un site web Azure de plusieurs façons. En cliquant sur le bouton **Nouveau**, en bas du portail, la boîte de dialogue suivante s'affiche:

![GlobalWebCreate][GlobalWebCreate]

Il existe trois options pour créer un site web : **Création rapide**, **Création personnalisée** et **À partir de la galerie**. Avec chacune de ces options, vous devez sélectionner une région Azure s'adaptant à la majorité de votre base utilisateur.

Si vous migrez un site web existant, l'option **Création personnalisée** vous permet de créer ou d'associer une base de données SQL ou MySQL. Cette option offre également la possibilité de spécifier plusieurs options de contrôle du code source pour le déploiement, telles que GitHub ou Team Foundation Server (TFS). Si vous gérez déjà votre site web à l'aide d'un mécanisme de contrôle de code source, cette option permet de configurer rapidement votre site web Azure pour un déploiement.

L'option **À partir de la galerie** permet de configurer un nouveau site avec l'une des nombreuses infrastructures, telles que Drupal ou WordPress, ce qui permet de configurer rapidement un nouveau site que vous pouvez ensuite personnaliser dans l'infrastructure choisie.

Comme la plupart des services Azure, vous devez sélectionner une région Azure pour votre nouveau site web. Azure dispose de plusieurs régions réparties dans le monde entier. Une fois votre site web déployé dans une région, il est accessible mondialement sur Internet. Cependant, plusieurs régions permettent d'obtenir une plus grande flexibilité. Le déploiement de sites dans les régions les plus proches des utilisateurs constitue un avantage évident. 

Pour plus d'informations sur les étapes de création d'un site web, consultez [Prise en main d'Azure Web Sites et d'ASP.NET][howtocreatewebsites].

##<a name="deploywebsite"></a>Déployer le site web
Il existe plusieurs façons de déployer votre site web vers Azure. Si vous avez sélectionné une infrastructure de la galerie, vous disposez déjà d'un site de démarrage déployé. Toutefois, pour avancer, vous devez encore configurer certains types de procédures de modification et de déploiement. Les options de déploiement incluent entre autres :

- utilisation d'un client FTP ;
- déploiement à partir d'un contrôle de code source ;
- publication à partir de Visual Studio ;
- Publication à partir de [WebMatrix][webmatrix].

Chacune de ces options présente divers points forts. La possibilité de publier à partir d'un client FTP constitue une solution simple et directe pour envoyer les nouveaux fichiers vers votre site. Cela signifie également que les outils de publication ou les processus existants s'appuyant sur FTP peuvent continuer de fonctionner avec Sites Web Azure. Le contrôle de code source offre le meilleur contrôle sur les versions de contenu de site, les modifications pouvant être suivies, publiées et restaurées à des versions antérieures si nécessaire. Les options pour publier directement depuis Visual Studio ou Web Matrix sont pratiques pour les développeurs utilisant l'un de ces outils. Un scénario utile de cette fonctionnalité intervient lors des étapes préliminaires d'un projet ou pour un prototypage. Dans les deux cas, les publications et les tests fréquents sont potentiellement plus pratiques à partir de l'environnement de développement. 

La plupart des tâches de développement ici impliquent l'utilisation d'informations du portail de gestion Azure. Accédez à votre site web, sélectionnez l'onglet **Tableau de bord**, puis cherchez la section **aperçu rapide**. La capture d'écran qui suit présente plusieurs options.

![GlobalWebQuickGlance][GlobalWebQuickGlance]

Certains outils de contrôle de code source et clients FTP nécessitent un accès par nom d'utilisateur/mot de passe. Pour un nouveau site web, les informations d'identification ne sont pas automatiquement créées. Néanmoins, vous pouvez facilement le faire en cliquant sur **Réinitialisez vos informations d'identification de déploiement**. Quand vous avez fini, vous pouvez utiliser un client FTP pour déployer votre site web à l'aide de ces informations d'identification et du **Nom de l'hôte FTP** dans la même page **Tableau de bord**.

![GlobalWebFTPSettings][GlobalWebFTPSettings]

Notez que le nom d'utilisateur de déploiement/FTP correspond à une combinaison du nom de site web et du nom d'utilisateur que vous avez fournis. Par conséquent, si votre site s'intitulait " http://contoso.azurewebsite.net " et votre nom d'utilisateur " myuser ", le nom d'utilisateur du déploiement et FTP correspondra à " contoso\myuser ".

Vous pouvez également choisir d'effectuer le déploiement via un service de gestion de contrôle de code source, tel que GitHub ou TFS Online. Cliquez sur l'option pour **Configurer le déploiement à partir du contrôle de code source**. Suivez ensuite les instructions du système de contrôle de code source ou du service de votre choix. Pour obtenir des instructions pas à pas concernant la publication à partir d'un référentiel Git, consultez [Publication à partir du contrôle de code source dans Azure Web Sites][publishingwithgit].

Si vous prévoyez d'utiliser Visual Studio pour créer et gérer votre site, vous pouvez choisir de publier directement à partir de Visual Studio. Pour cela, il suffit de cliquer sur l'option **Télécharger le profil de publication**. Cela vous permet d'enregistrer un fichier publishsettings pouvant être importé dans Visual Studio pour une publication Web. 

> [WACOM.NOTE]
> Il est important de maintenir le fichier <i>publishsettings</i> en sécurité et hors du contrôle de code source, car il contient les noms et les mots de passe des utilisateurs, tant pour le déploiement que pour toute chaîne de connexion à une base de données liée.

Il est également possible d'importer les informations d'abonnement directement dans Visual Studio. Par exemple, imaginez un projet ASP.NET local dans Visual Studio. Cliquez avec le bouton droit sur le projet web, puis sélectionnez **Publier**. Le bouton **Importer** de la boîte de dialogue **Publier le site web** vous permet d'importer un fichier contenant vos paramètres d'abonnement Azure ou le fichier publishsettings que vous avez téléchargé depuis le tableau de bord d'Azure Web Sites. La capture d'écran qui suit présente ces options.

![GlobalWebVSPublish][GlobalWebVSPublish]

Pour plus d'informations concernant la publication sur Azure à partir de Visual Studio, consultez Déploiement d'une application web ASP.NET sur un site web Azure. 

WebMatrix à partir du portail de gestion Azure est une option supplémentaire pour le développement et le déploiement.

![GlobalWebWebMatrix][GlobalWebWebMatrix]

Pour plus d'informations sur cette option, consultez [Développement et déploiement d'un site web avec Microsoft WebMatrix][aspnetgetstarted].

Bien que ces étapes vous fournissent le nécessaire pour déployer votre site .COM, vous devez créer un plan pour gérer le cycle de publication de contenu en cours. Ces options peuvent aller de regrouper une solution personnalisée, en passant par des redéploiements périodiques pour un site qui change rarement, à un système de gestion de contenu (CMS) complet. Si vous créez un site web, notez qu'il existe des options dans la galerie permettant d'utiliser les infrastructures CMS existantes, par exemple [Drupal][drupal] ou [Umbraco][umbraco].

##<a name="customdomain"></a>Ajout d'un domaine personnalisé
S'il s'agit de votre présence sur le web à l'international, vous pouvez associer votre nom de domaine inscrit au site web. Il existe plusieurs fournisseurs tiers offrant des services d'inscription de domaine. Chacun d'eux prend en charge la création de différents types d'enregistrements DNS pour gérer votre domaine. Un enregistrement DNS permet de mapper une URL conviviale, telle que " www.contoso.com ", vers l'URL ou l'adresse IP hébergeant votre site. 

<div class="dev-callout">
<strong>Remarque</strong>
<p>Dans la discussion ci-dessous, il y a deux types d'enregistrement DNS susceptibles de vous intéresser. Le premier, un enregistrement CNAME, peut rediriger une URL, telle que " www.contoso.com ", vers une URL différente, telle que " contoso.azurewebsites.net ". Le second, un enregistrement A, peut mapper une URL, telle que " www.contoso.com ", vers une adresse IP, telle que 172.16.48.1.</p>
</div>

Pour Sites Web Azure, vous devez commencer par créer un enregistrement CNAME sur le site web Azure. Ce paramètre s'effectue via le site du bureau d'enregistrement tiers. Ce qui suit est un exemple d'enregistrement CNAME.

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">Type</th>
   <th align="left" valign="top">Hôte</th>
   <th align="left" valign="top">Réponse</th>
   <th align="left" valign="top">TTL</th>
</tr>
<tr>
   <td valign="top"><strong>CNAME</strong></td>
   <td valign="top">www.contoso.com</td>
   <td valign="top">contoso.azurewebsites.net</td>
   <td valign="top">8000</td>
</tr>
</table>

Si votre domaine vient d'être inscrit, la résolution du domaine peut nécessiter un ou plusieurs jours sur tous les serveurs DNS, lesquels fonctionnent hors des entrées DNS en cache. Cependant, si votre domaine existe déjà, la modification de CNAME doit se produire en une minute. Notez que l'enregistrement CNAME fournit un mappage entre votre domaine (devant être nommé avec un alias de sous-domaine, tel que " www ") et l'URL de site web Azure. Aucun côté de l'enregistrement CNAME n'inclut le préfixe " http:// ".

Dans le portail de gestion Azure, vérifiez que vous êtes en mode **Partagé** ou Sta****ndard sous l'onglet **Mettre à l'échelle** (les domaines personnalisés ne sont pas pris en charge pour les sites web en mode **Gratuit**). Accédez ensuite à l'onglet **Configurer** et cliquez sur le bouton **Gérer les domaines**. Cela vous permet d'associer le site web au nom de domaine personnalisé. 

![GlobalWebWebMatrix][GlobalWebWebMatrix]

Avant de placer votre domaine personnalisé dans la liste, vous devez passer par votre fournisseur DNS et créer un enregistrement CNAME de votre domaine personnalisé (www.contoso.com) pointant sur l'URL de votre site web Azure (contoso.azurewebsites.net). Après cette propagation, vous pouvez entrer le domaine personnalisé dans la boîte de dialogue présentée dans la capture d'écran précédente. La présence de l'enregistrement CNAME pour www.contoso.com, qui pointe vers ce site web, garantit que vous disposez de l'autorité pour utiliser ce nom de domaine avec ce site web. À présent, vous pouvez créer un enregistrement A avec l'adresse IP en bas de la boîte de dialogue.

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">Type</th>
   <th align="left" valign="top">Hôte</th>
   <th align="left" valign="top">Réponse</th>
   <th align="left" valign="top">TTL</th>
</tr>
<tr>
   <td valign="top"><strong>A</strong></td>
   <td valign="top">contoso.com</td>
   <td valign="top">172.16.48.1</td>
   <td valign="top">8000</td>
</tr>
</table>

Pour plus d'informations, consultez [Configuration d'un nom de domaine personnalisé pour un site web Azure][customdns].

##<a name="ssl"></a>Sécuriser le site web avec SSL
Si votre site contient des informations en lecture seule, il est inutile de fournir un accès sécurisé au site. Néanmoins, si vous collectez des informations d'utilisateur, effectuez un commerce électronique ou gérez d'autres données sensibles, vous devez sécuriser le site. La sécurité est un sujet important, et ce document ne peut pas traiter l'ensemble des meilleures pratiques et techniques. Cependant, il est essentiel de mettre en lumière le processus d'activation de chiffrement de données (SSL) de votre site web. Le chiffrement de données (SSL) permet aux utilisateurs de se connecter à votre site d'une façon chiffrée avec des adresses HTTPS et non HTTP. Il existe des étapes spécifiques à effectuer pour utiliser SSL avec Sites Web Azure. 

Sites Web Azure fournit automatiquement une connexion sécurisée à l'URL de site réelle. Par exemple, si votre site est de type http://contoso.azurewebsites.net, vous pouvez vous connecter sur SSL simplement en remplaçant " http " par " https ", par exemple **https**://contoso.azurewebsites.net.

Toutefois, si vous utilisez un nom de domaine personnalisé, vous devez télécharger un certificat et activer SSL via le portail de gestion Azure pour votre site web. Les étapes suivantes présentent un résumé de ce processus, mais vous pouvez obtenir des instructions détaillées dans la rubrique  [Configuration d'un certificat SSL pour un site web Azure][ssl].

Tout d'abord, obtenez un certificat SSL d'une autorité de certification. Si vous envisagez de sécuriser votre domaine avec plusieurs sous-domaines (par exemple www.contoso.com et staging.contoso.com), vous devez obtenir un certificat avec caractères génériques (*.contoso.com). Le coût risquant d'être plus élevé, vous devez décider si la flexibilité de ce type de certificat le justifie.

Dès que vous avez obtenu le certificat de l'autorité de certification, vous ne pouvez pas simplement le télécharger sur Azure au même format. Vous devez générer un fichier .pfx à l'aide de la commande openssl qui fait partie du projet OpenSSL. Les sources sont distribuées sur le [site web OpenSSL][openssl], mais vous pouvez généralement trouver une version précompilée de l'outil sur Internet. Dans l'exemple suivant, le certificat myserver.crt et le fichier de clé privée myserver.key sont utilisés pour créer un fichier .pfx.

	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

Pour télécharger le certificat dans Azure, accédez d'abord à l'onglet **Mettre à l'échelle** et vérifiez que vous êtes en mode **Standard**. SSL pour les domaines personnalisés n'est pas pris en charge pour les modes **Gratuit** ou **Partagé**. Sous l'onglet **Configurer**, cliquez sur le bouton**télécharger un certificat**.

![GlobalWebUplodateCert][GlobalWebUplodateCert]

Ensuite, dans la section **liaisons ssl**, mappez le certificat au nom de domaine qu'il sécurise. Il existe deux options pour ce mappage : SSL SNI et SSL basé IP.

![GlobalWebSSLBindings][GlobalWebSSLBindings]

L'option **SSL basé sur IP** est la façon classique de mapper l'adresse IP publique dédiée au nom de domaine. Cela fonctionne avec tous les navigateurs. L'option **SSL SNI** permet à plusieurs domaines de partager la même adresse IP, tout en ayant différents certificats SSL associés à chaque domaine. SSL SNI ne fonctionne pas avec certains navigateurs plus anciens (pour plus d'informations concernant la compatibilité, consultez l'[article de Wikipedia sur SSL SNI][sni]). Une facturation mensuelle (au prorata des heures) est associée à chaque certificat SSL, et la tarification varie en fonction du choix du SSL basé IP ou SNI. Pour plus d'informations sur la tarification, consultez [Tarification - Sites web][sslpricing]. Pour plus d'informations sur ce processus, consultez [Configuration d'un certificat SSL pour un site web Azure][ssl].

##<a name="monitor"></a>Surveillance du site
Une fois que votre site traite activement les requêtes utilisateur, il est important de recourir à une surveillance. Par exemple, vous souhaitez savoir si une charge utilisateur provoque un temps processeur élevé, indiquant probablement la nécessité de mettre à l'échelle le site. Ou bien l'inefficacité d'une application peut augmenter le temps de réponse ou entraîner des erreurs. Cette section présente certaines possibilités de surveillance intégrées au portail de gestion Azure.

L'onglet **Surveiller** contient certaines mesures essentielles de votre site web sous forme graphique. 

![GlobalWebMonitor1][GlobalWebMonitor1]

Vous pouvez personnaliser les mesures de ce graphique à l'aide du bouton Ajouter des mesures.

![GlobalWebMonitor2][GlobalWebMonitor2]

Pour les sites exécutés en mode **Standard**, vous pouvez également activer une surveillance et des alertes au point de terminaison. Sous l'onglet **Configurer**, accédez à la section **surveillance**, et configurez un point de terminaison. Ce dernier s'exécute à partir d'un ou de plusieurs emplacements que vous spécifiez, et essaie régulièrement d'accéder à votre site web. Les informations de temps et d'erreurs sont collectées. 

Sous l'onglet **Surveiller**, ce point de terminaison apparaît indiquant le temps de réponse. Si vous sélectionnez la mesure de point de terminaison, vous pouvez alors ajouter une règle d'alerte en cliquant sur l'icône **Ajouter une règle**.

![GlobalWebMonitor3][GlobalWebMonitor3]

La règle peut correspondre à l'envoi d'une notification par courrier électronique aux administrateurs ou à d'autres intervenants lorsque le temps de réponse dépasse le seuil spécifié.

![GlobalWebMonitor4][GlobalWebMonitor4]

Si votre site requiert une mise à l'échelle, utilisez l'onglet **Mettre à l'échelle** pour le faire manuellement ou via la version préliminaire de la mise à l'échelle automatique. Cet onglet offre la possibilité d'augmenter l'échelle (machines dédiées plus volumineuses) ou de la diminuer (instances partagées supplémentaires ou instances dédiées de même taille). Cependant, la version préliminaire de la mise à l'échelle automatique ne prend en charge que la montée en charge. Pour plus d'informations sur la surveillance des sites web, consultez la section " Évolution en fonction de la demande " du scénario [Campagne de marketing numérique][scenariodigitalmarketing]. Consultez également [Surveillance des sites web][howtomonitor].

##<a name="summary"></a>Résumé
Pour créer le site (.COM) de votre organisation, les tâches standard incluent le choix d'une infrastructure de développement, la création du site, le déploiement, l'affectation d'un domaine personnalisé et la surveillance. Pour les sites nécessitant de sécuriser les données utilisateur, SSL est fortement recommandé. Cet article a présenté un aperçu des tâches à effectuer durant l'utilisation de Sites Web Azure. Pour plus d'informations, consultez les articles techniques suivants référencés dans le document.

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">Domaine</th>
   <th align="left" valign="top">Ressources</th>
</tr>
<tr>
   <td valign="middle"><strong>Planification</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/fr-fr/manage/services/web-sites/choose-web-app-service">Comparaison entre Azure Web Sites, Azure Cloud Services et Azure Virtual Machines</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Création</strong></td>
   <td valign="top">- <a href="http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-dotnet-get-started/">Prise en main d'Azure et ASP.NET</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Déploiement</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/fr-fr/develop/net/common-tasks/publishing-with-git/">Publication à partir du contrôle de code source dans Azure Web Sites</a><br/>- <a href="http://www.windowsazure.com/fr-fr/develop/net/tutorials/get-started/">Déploiement d'une application Web ASP.NET sur un site web Azure</a><br/>- <a href="http://www.windowsazure.com/fr-fr/develop/net/tutorials/website-with-webmatrix/">Développement et déploiement d'un site web avec Microsoft WebMatrix</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Domaines personnalisé</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/fr-fr/develop/net/common-tasks/custom-dns-web-site/">Configuration d'un nom de domaine personnalisé pour un site web Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>SSL</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/fr-fr/develop/net/common-tasks/enable-ssl-web-site/">Configuration d'un certificat SSL pour un site web Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Surveiller</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/fr-fr/manage/services/web-sites/how-to-monitor-websites/">Surveillance de sites web</a></td>
</tr>
</table>

  [websitesoverview]:/fr-fr/documentation/services/web-sites/
  [csoverview]:/fr-fr/documentation/services/cloud-services/
  [vmoverview]:/fr-fr/documentation/services/virtual-machines/
  [chooseservice]:/fr-fr/manage/services/web-sites/choose-web-app-service
  
  
  [scenariodigitalmarketing]:/fr-fr/manage/services/web-sites/digital-marketing-campaign-solution-overview
  [howtocreatewebsites]:/fr-fr/documentation/articles/web-sites-dotnet-get-started
  [webmatrix]:http://www.microsoft.com/web/webmatrix/
  [publishingwithgit]:/fr-fr/develop/net/common-tasks/publishing-with-git/
  [aspnetgetstarted]:/fr-fr/develop/net/tutorials/get-started/
  [drupal]:https://drupal.org/
  [umbraco]:http://umbraco.com/
  [customdns]:/fr-fr/develop/net/common-tasks/custom-dns-web-site/
  [ssl]:/fr-fr/develop/net/common-tasks/enable-ssl-web-site/
  [openssl]:http://www.openssl.org/
  [sni]:http://en.wikipedia.org/wiki/Server_Name_Indication
  [sslpricing]:/fr-fr/pricing/details/web-sites/#service-ssl
  [howtomonitor]:/fr-fr/manage/services/web-sites/how-to-monitor-websites/
  
 [GlobalWebCreate]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Create.png
  [GlobalWebQuickGlance]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_QuickGlance.png
  [GlobalWebMonitor1]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor1.png
  [GlobalWebMonitor2]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor2.png
  [GlobalWebMonitor3]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor3.png
  [GlobalWebMonitor4]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor4.png
  [GlobalWebVSPublish]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_VS_Publish.png
  [GlobalWebSSLBindings]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_SSL_Bindings.png
  [GlobalWebUplodateCert]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Uplodate_Cert.png
  [GlobalWebCustomDomain]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_CustomDomain.png
  [GlobalWebWebMatrix]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_WebMatrix.png
  [GlobalWebFTPSettings]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_FTPSettings.png
  
  
  
  
  
  
  
  
  
  
  

<!--HONumber=35_1-->
