<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a custom domain name" pageTitle="Configure a custom domain name for an Azure web site that uses Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Configuration d'un nom de domaine personnalisé pour un Site Web Azure utilisant Traffic Manager
===============================================================================================

[Domaine personnalisé](/en-us/documentation/articles/web-sites-custom-domain-name "Domaine personnalisé")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Site Web](/en-us/documentation/articles/web-sites-custom-domain-name/ "Sites Web") | [Site Web utilisant Traffic Manager](/en-us/documentation/articles/web-sites-traffic-manager-custom-domain-name/ "Site Web utilisant Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article contient des instructions génériques sur l'utilisation d'un nom de domaine personnalisé avec le service Sites Web Azure en utilisant Traffic Manager pour l'équilibrage de charge. Veuillez consulter les onglets en haut de cet article pour vérifier si votre bureau d'enregistrement de domaines est bien répertorié. Le cas échéant, veuillez sélectionner cet onglet pour consulter la procédure relative à ce bureau d'enregistrement de domaines.

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Dans cet article :

-   [Présentation des enregistrements DNS](#understanding-records)
-   [Configuration des sites Web pour le mode Standard](#bkmk_configsharedmode)
-   [Ajout d'un enregistrement DNS pour votre domaine personnalisé](#bkmk_configurecname)
-   [Activation de Traffic Manager pour votre site Web](#enabledomain)

Présentation des enregistrements DNS
------------------------------------

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

Configuration des sites Web pour le mode Standard
-------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

## Ajout d'un enregistrement DNS pour votre domaine personnalisé 

Pour associer votre domaine personnalisé à un Site Web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par le bureau d'enregistrement de domaines auprès duquel vous avez acheté votre nom de domaine. Pour localiser et utiliser les outils DNS, procédez comme suit.

1.  Connectez-vous à votre compte du bureau d'enregistrement de domaines, puis recherchez une page pour la gestion des enregistrements DNS. Recherchez la mention **Domain Name**, **DNS** ou **Name Server Management**. Vous trouverez généralement un lien vers cette page en affichant vos informations de compte, puis en recherchant un lien tel que **Mes domaines**.

2.  Lorsque vous avez trouvé la page de gestion de votre nom de domaine, recherchez un lien vous permettant de modifier les enregistrements DNS. Il est probablement répertorié en tant que lien de configuration de **fichier de zone**, d'**enregistrements DNS**, ou **avancé**.

    -   Il est possible que plusieurs enregistrements soient déjà créés pour cette page, par exemple, une entrée associant « **@** » ou « \* » à une page de parc de domaine. Cette page peut également contenir des enregistrements pour des sous-domaines courants, tels que **www**.
    -   La page mentionnera les **enregistrements CNAME** ou fournira une liste déroulante permettant de sélectionner un type d'enregistrement. Elle peut également mentionner d'autres enregistrements, tels que des **enregistrements A** et des **enregistrements MX**. Dans certains cas, les enregistrements CNAME sont appelés différemment, par exemple **Enregistrement d'alias**.
    -   Cette page comprend également des champs permettant de **mapper** à partir d'un **nom d'hôte** ou d'un **nom de domaine** vers un autre nom de domaine.

3.  Même si chaque bureau d'enregistrement a ses particularités, vous pouvez généralement mapper *à partir* de votre nom de domaine personnalisé (comme **contoso.com**) *vers* le nom de domaine Traffic Manager (**contoso.trafficmanager.net**) utilisé pour votre Site Web Azure.

4.  Lorsque vous avez fini d'ajouter ou de modifier des enregistrements DNS auprès de votre bureau d'enregistrement, enregistrez les modifications.

Activation du site Web Traffic Manager
--------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

