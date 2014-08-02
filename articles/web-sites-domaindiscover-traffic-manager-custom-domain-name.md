<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with DomainDiscover - TierraNet" pageTitle="Configure a DomainDiscover domain name for an Azure web site using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, DomainDiscover, TierraNet, Traffic Manager" description="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with DomainDiscover - TierraNet" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Configuration d'un nom de domaine personnalisé pour un site Web Windows Azure utilisant Traffic Manager (DomainDiscover / TierraNet)
====================================================================================================================================

[Domaine personnalisé](/en-us/documentation/articles/web-sites-custom-domain-name "Domaine personnalisé")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Site Web](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name/ "Sites Web") | [Site Web utilisant Traffic Manager](/en-us/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/ "Site Web utilisant Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article contient des instructions permettant d'utiliser un nom de domaine personnalisé acheté auprès de [DomainDiscover.com](https://domaindiscover.com) avec le service Sites Web Azure. DomainDiscover.com fait partie de [TierraNet](https://www.tierra.net/).

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

Pour associer votre domaine personnalisé à un site Web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par DomainDiscover. Pour localiser et utiliser les outils DNS pour DomainDiscover.com, procédez comme suit.

1.  Connectez-vous à votre compte sur DomainDiscover.com (TierraNet) en sélectionnant **Control Panel** dans le menu **Login**.

    ![Menu Login de DomainDiscover](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png)

2.  Sur la page **Domain Services**, sélectionnez le domaine que vous souhaitez utiliser pour votre site Web Azure.

    ![Page de gestion de domaine](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png)

3.  Dans les paramètres de domaine, cliquez sur le bouton **Edit** correspondant à **DNS Service**.

    ![Bouton de modification des DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png)

4.  Dans la fenêtre **Manage DNS**, sélectionnez le type d'enregistrement DNS à ajouter à la liste **Add Records**. Cliquez ensuite sur le bouton **Add**.

    ![Bouton de modification des DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png)

5.  Sur la page suivante, entrez les valeurs d'enregistrement DNS. Cliquez ensuite sur le bouton **Add**.

    ![Bouton de modification des DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords_TM.png)

    -   Lorsque vous ajoutez un enregistrement CNAME, vous devez d'abord sélectionner **CNAME (Alias)** sur la page **Manage DNS**. Définissez ensuite le champ **Host** sur le sous-domaine que vous souhaitez utiliser. Par exemple, **www**. Définissez le champ **Alias Hostname** sur le nom de domaine **.trafficmanager.net** du profil Traffic Manager utilisé avec votre site Web Azure. Par exemple, **contoso.trafficmanager.net**. Ensuite, indiquez une valeur de durée de vie « time-to-live » (TTL), par exemple 1 800 secondes.

        > [WACOM.NOTE] Vous devez uniquement utiliser les enregistrements CNAME lorsque vous associez votre nom de domaine personnalisé à un site Web équilibré en charge à l'aide de Traffic Manager.

Activation du site Web Traffic Manager
--------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

