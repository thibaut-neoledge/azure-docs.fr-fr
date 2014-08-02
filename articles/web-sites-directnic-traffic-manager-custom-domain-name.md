<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with Directnic" pageTitle="Configure a Directnic domain name for an Azure web site using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, Directnic, Traffic Manager" description="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with Directnic" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Configuration d'un nom de domaine personnalisé pour un site Web Windows Azure utilisant Traffic Manager (Directnic)
===================================================================================================================

[Domaine personnalisé](/en-us/documentation/articles/web-sites-custom-domain-name "Domaine personnalisé")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Site Web](/en-us/documentation/articles/web-sites-directnic-custom-domain-name/ "Sites Web") | [Site Web utilisant Traffic Manager](/en-us/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/ "Site Web utilisant Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article contient des instructions permettant d'utiliser un nom de domaine personnalisé acheté auprès de [DirectNic.com](https://directnic.com) avec le service Sites Web Azure.

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

Pour associer votre domaine personnalisé à un site Web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par Directnic. Pour localiser les outils DNS pour Directnic.com, procédez comme suit.

1.  Connectez-vous à votre compte Directnic.com et sélectionnez **My Services** puis **Domains**.

    ![Menu des services Directnic](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png)

2.  Cliquez sur le nom de domaine que vous souhaitez utiliser pour votre site Web Azure.

3.  Dans la page de gestion de votre domaine, cliquez sur le bouton **Manage** correspondant à **DNS** dans le volet **Services**.

    ![Volet Services](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png)

4.  Ajoutez des enregistrements DNS en remplissant les champs **Type**, **Name** et **Data**. Lorsque vous avez terminé, cliquez sur le bouton **Add Record**.

    ![](.\media\web-sites-directnic-custom-domain-name\Directnic_DNS_TM.png)

    -   Lorsque vous ajoutez un enregistrement CNAME, vous devez définir le champ **Name** sur le sous-domaine que vous souhaitez utiliser. Par exemple, **www**. Définissez le champ **Data** sur le nom de domaine **.trafficmanager.net** du profil Traffic Manager utilisé avec votre site Web Azure. Par exemple, **contoso.trafficmanager.net**.

        > [WACOM.NOTE] Vous devez uniquement utiliser les enregistrements CNAME lorsque vous associez votre nom de domaine personnalisé à un site Web équilibré en charge à l'aide de Traffic Manager.

Activation du site Web Traffic Manager
--------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

