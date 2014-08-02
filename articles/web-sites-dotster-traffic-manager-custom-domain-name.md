<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with Dotster" pageTitle="Configure a Dotster domain name for an Azure web site using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, Dotster, Traffic Manager" description="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with Dotster" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Configuration d'un nom de domaine personnalisé pour un site Web Windows Azure utilisant Traffic Manager (Dotster)
=================================================================================================================

[Domaine personnalisé](/en-us/documentation/articles/web-sites-custom-domain-name "Domaine personnalisé")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Site Web](/en-us/documentation/articles/web-sites-dotster-custom-domain-name/ "Sites Web") | [Site Web utilisant Traffic Manager](/en-us/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/ "Site Web utilisant Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article contient des instructions permettant d'utiliser un nom de domaine personnalisé acheté auprès de [Dotster.com](https://dotster.com) avec le service Sites Web Azure.

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

Pour associer votre domaine personnalisé à un site Web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par Dotster. Pour trouver les outils DNS pour Dotster.com, procédez comme suit.

1.  Connectez-vous à votre compte Dotster.com et sélectionnez **Domain** puis **Domain Central**.

    ![Menu Domain Central de Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png)

2.  Sélectionnez votre domaine pour afficher une liste de paramètres. Sélectionnez ensuite le lien **Nameservers**.

    ![Options de configuration de domaine Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png)

3.  Sélectionnez l'option **Use different name servers**. Afin de bénéficier des services DNS sur Dotster, vous devez spécifier les serveurs de noms suivants : ns1.nameresolve.com, ns2.nameresolve.com, ns3.nameresolve.com et ns4.nameresolve.com.

    ![Options de configuration de domaine Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png)

    > [WACOM.NOTE] La modification des serveurs de noms peut prendre entre 24 et 48 h. Elle est indispensable au bon déroulement de la suite de cet article.

4.  Dans DomainCentral, sélectionnez votre domaine, puis sélectionnez **DNS**. Dans la liste **Modify**, sélectionnez le type d'enregistrement DNS à ajouter (**CNAME Alias** ou **A Record**).

    ![Options de configuration de domaine Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png)

5.  Ensuite, spécifiez les champs **Host** et **Points To** pour l'enregistrement. Lorsque vous avez terminé, cliquez sur le bouton **Add**.

    ![Options de configuration de domaine Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME_TM.png)

    -   Lors de l'ajout d'un enregistrement CNAME, vous devez définir le champ **Host** sur le sous-domaine à utiliser. Par exemple, **www**. Définissez le champ **Points To** sur le nom de domaine **.trafficmanager.net** du profil Traffic Manager utilisé avec votre site Web Azure. Par exemple, **contoso.trafficmanager.net**.

        > [WACOM.NOTE] Vous devez uniquement utiliser les enregistrements CNAME lorsque vous associez votre nom de domaine personnalisé à un site Web équilibré en charge à l'aide de Traffic Manager.

Activation du site Web Traffic Manager
--------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

