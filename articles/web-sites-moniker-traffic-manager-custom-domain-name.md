<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with Moniker" pageTitle="Configure a Moniker domain name for an Azure web site using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, Moniker, Traffic Manager" description="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Configuration d'un nom de domaine personnalisé pour un site Web Azure à l'aide de Traffic Manager (Moniker)
===========================================================================================================

[Domaine personnalisé](/en-us/documentation/articles/web-sites-custom-domain-name "Domaine personnalisé")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Site Web](/en-us/documentation/articles/web-sites-moniker-custom-domain-name/ "Sites Web") | [Site Web utilisant Traffic Manager](/en-us/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/ "Site Web utilisant Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article contient des instructions permettant d'utiliser un nom de domaine personnalisé acheté auprès de [Moniker](https://moniker.com) avec le service Sites Web Azure.

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

Pour associer votre domaine personnalisé à un site Web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par Moniker. Pour localiser les outils DNS pour Moniker.com, procédez comme suit.

1.  Connectez-vous à votre compte Moniker.com, puis sélectionnez **My Domains** et cliquez sur **Manage Templates**.

    ![Page My Domains pour Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png)

2.  Sur la page **Zone Template Management**, sélectionnez **Create New Template**.

    ![Page Zone Template Management de Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png)

3.  Indiquez le nom de modèle sous **Template Name**.

4.  Créez ensuite un enregistrement DNS en commençant par sélectionner un type d'enregistrement, dans **Record Type**. Remplissez ensuite les champs **Hostname** (non d'hôte) et **Address** (adresse).

    ![Page Create Zone Template de Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate_TM.png)

    -   Lors de l'ajout d'un enregistrement CNAME, définissez le champ **Hostname** sur le sous-domaine que vous souhaitez utiliser. Par exemple, **www**. Définissez le champ **Address** sur le nom de domaine **.trafficmanager.net** du profil Traffic Manager utilisé avec votre site Web Azure. Par exemple, **contoso.trafficmanager.net**.

        > [WACOM.NOTE] Vous devez uniquement utiliser les enregistrements CNAME lorsque vous associez votre nom de domaine personnalisé à un site Web équilibré en charge à l'aide de Traffic Manager.

5.  Cliquez sur le bouton **Add** pour ajouter l'entrée.

6.  Une fois toutes les entrées ajoutées, cliquez sur le bouton **Save**.

7.  Sélectionnez **Domain Manager** pour revenir à votre liste de domaines.

8.  Cochez la case correspondant à votre domaine cible, puis cliquez à nouveau sur **Manage Templates**.

9.  Sélectionnez le nouveau modèle que vous avez créé au cours des étapes précédentes. Cliquez ensuite sur le lien **place selected domains (1) into this Template**.

    ![Page Create Zone Template de Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png)

Activation du site Web Traffic Manager
--------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

