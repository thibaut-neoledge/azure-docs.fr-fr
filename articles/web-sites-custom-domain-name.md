<properties  title="Learn how to configure an Azure web site to use a custom domain name" pageTitle="Configure a custom domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

# Configuration d'un nom de domaine personnalisé pour un site Web Azure

 
<div  class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/web-sites-custom-domain-name" title="Domaine personnalisé" class="current">Domaine personnalisé</a><a href="/en-us/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/en-us/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/en-us/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/en-us/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/en-us/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

 
<div  class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/web-sites-custom-domain-name/" title="Sites Web" class="current">Sites Web</a> | <a href="/en-us/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Site Web utilisant Traffic Manager">Site Web utilisant Traffic Manager</a></div>

 [WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Cet article contient des instructions génériques permettant d'utiliser un nom de domaine personnalisé avec des sites Web Azure. Veuillez consulter les onglets en haut de cet article pour vérifier si votre bureau d'enregistrement de domaines est bien répertorié. Le cas échéant, veuillez sélectionner cet onglet pour consulter la procédure relative à ce bureau d'enregistrement de domaines.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Dans cet article :

* [Présentation des enregistrements DNS](#understanding-records)
* [Configuration de vos sites Web pour les modes Basique, Partagé ou Standard](#bkmk_configsharedmode)
* [Ajout d'un enregistrement DNS pour votre domaine personnalisé](#bkmk_configurecname)
* [Activation du domaine sur votre site Web](#enabledomain)

<h2><a name="understanding-records"></a>Présentation des enregistrements DNS</h2>


[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Configuration de vos sites Web pour les modes Basique, Partagé ou Standard</h2>


[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>Ajout d'un enregistrement DNS pour votre domaine personnalisé</h2>

Pour associer votre domaine personnalisé a un site Web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par le bureau d'enregistrement de domaines auprès duquel vous avez acheté votre nom de domaine. Pour localiser et utiliser les outils DNS, procédez comme suit.

1. Connectez-vous à votre compte du bureau d'enregistrement de domaines, puis recherchez une page pour la gestion des enregistrements DNS. Recherchez la mention **Domain Name**, **DNS** ou **Name Server Management**. Vous trouverez généralement un lien vers cette page en affichant vos informations de compte, puis en recherchant un lien tel que **Mes domaines**.

2. Lorsque vous avez trouvé la page de gestion de votre nom de domaine, recherchez un lien vous permettant de modifier les enregistrements DNS. Il est probablement répertorié en tant que lien de configuration de **fichier de zone**, d'**enregistrements DNS**, ou **avancé**.
    
    * Il est possible que plusieurs enregistrements soient déjà créés pour cette page, par exemple, une entrée associant << **@** >> ou << \* >> à une page de parc de domaine. Cette page peut également contenir des enregistrements pour des sous-domaines courants, tels que **www**.
    * La page mentionnera les **enregistrements A** et les **enregistrements CNAME**, ou fournira une liste déroulante vous permettant de sélectionner un type d'enregistrement. Elle peut également mentionner d'autres enregistrements, tels que des **enregistrements MX**. Dans ce cas, ils porteront un autre nom, comme **enregistrements d'adresses IP** au lieu d'enregistrement A, ou **enregistrements d'alias** au lieu d'enregistrements CNAME.
    * Cette page comprendra également des champs vous permettant de **mapper** à partir d'un **nom d'hôte** ou d'un **nom de domaine** vers une **adresse IP** ou d'autres noms de domaine.

1. Même si chaque bureau d'enregistrement a ses particularités, vous pouvez généralement mapper *à partir* de votre nom de domaine personnalisé (comme **contoso.com**) *vers* le nom de domaine de votre site Web Azure 	(**contoso.azurewebsites.net**) ou l'adresse IP virtuelle du site Web Azure.
    
    * Les enregistrements CNAME mappent toujours vers le domaine des sites Web Azure : **contoso.azurewebsites.net**. Vous procéderez donc au mappage *à partir* d'un domaine tel que **www** *vers* votre adresse **<nom\_de\_votre\_site\_Web>.azurewebsites.net**.


     
      > [WACOM.NOTE] Si vous utilisez un enregistrement A, vous devez également ajouter un enregistrement CNAME avec l'une des
      > configurations suivantes :
      > 
      > * Mappage *à partir* de **www** *vers* votre adresse
      >   **<nom\_de\_votre\_site\_Web>.azurewebsites.net**.
      > 
      > OU
      > 
      > * Mappage *à partir* de **awverify.www** *vers*
      >   **awverify.<nom\_de\_votre\_site\_Web>.azurewebsites.net**.
      > 
      > Cet enregistrement CNAME permet à Azure de confirmer que vous possédez le domaine décrit par l'enregistrement A.
    
    * Les enregistrements A mappent toujours vers l'adresse IP virtuelle des sites Web Azure. Le mappage s'effectue donc *à partir* d'un domaine tel que **www** *vers* l'adresse IP virtuelle du site Web.


     
      > [WACOM.NOTE] Pour mapper un domaine racine (tel que **contoso.com**) vers un site Web Azure, vous procéderez souvent à un mappage à partir de << **@** >> ou d'une entrée vierge vers l'adresse IP virtuelle. Pour créer un mappage de caractère générique mappant tous les sous-domaines vers l'adresse IP virtuelle, vous utiliserez généralement un mappage à partir de << \* >> vers l'adresse IP virtuelle.
      > 
      > Les spécificités du mappage d'une racine ou d'un caractère générique varient selon les bureaux d'enregistrement. Pour plus d'informations, consultez la documentation fournie par votre bureau d'enregistrement.

4.  Lorsque vous avez fini d'ajouter ou de modifier des enregistrements
    DNS auprès de votre bureau d'enregistrement, enregistrez les
    modifications.

<h2><a name="enabledomain"></a>Activation du domaine sur votre site Web</h2>


[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]