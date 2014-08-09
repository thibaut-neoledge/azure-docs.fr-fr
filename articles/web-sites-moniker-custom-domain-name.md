<properties title="Learn how to configure an Azure web site to use a domain name registered with Moniker" pageTitle="Configure a Moniker domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="Learn how to configure an Azure web site to use a domain name registered with Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Configuration d'un nom de domaine personnalisé pour un site Web Azure (Moniker)
===============================================================================

[Domaine personnalisé](/fr-fr/documentation/articles/web-sites-custom-domain-name "Domaine personnalisé")[GoDaddy](/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/fr-fr/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Site Web](/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name/ "Sites Web") | [Site Web utilisant Traffic Manager](/fr-fr/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/ "Site Web utilisant Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Cet article fournit des instructions sur l'utilisation d'un nom de domaine personnalisé acheté auprès de [Moniker.com](https://moniker.com) avec les sites Web Azure.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Dans cet article :

-   [Présentation des enregistrements DNS](#understanding-records)
-   [Configuration de vos sites Web pour le mode Basique, Partagé ou Standard](#bkmk_configsharedmode)
-   [Ajout d'un enregistrement DNS pour votre domaine personnalisé](#bkmk_configurecname)
-   [Activation du domaine sur votre site Web](#enabledomain)

Présentation des enregistrements DNS
------------------------------------

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

Configuration de vos sites Web pour le mode Basique, Partagé ou Standard
------------------------------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

## Ajout d'un enregistrement DNS pour votre domaine personnalisé 

Pour associer votre domaine personnalisé à un site Web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par Moniker. Pour localiser les outils DNS pour Moniker.com, procédez comme suit.

1.  Connectez-vous à votre compte Moniker.com, puis sélectionnez **My Domains** et cliquez sur **Manage Templates**.

    ![Page My Domains pour Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png)

2.  Sur la page **Zone Template Management**, sélectionnez **Create New Template**.

    ![Page Zone Template Management de Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png)

3.  Indiquez le nom de modèle sous **Template Name**.

4.  Créez ensuite un enregistrement DNS en commençant par sélectionner un type d'enregistrement, dans **Record Type**. Remplissez ensuite les champs **Hostname** (non d'hôte) et **Address** (adresse).

    ![Page Create Zone Template de Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate.png)

    -   Lors de l'ajout d'un enregistrement CNAME, définissez le champ **Hostname** sur le sous-domaine que vous souhaitez utiliser. Par exemple, **www**. Vous devez définir le champ **Address** sur le nom de domaine **.azurewebsites.net** de votre site Web Azure. Par exemple, **contoso.azurwebsites.net**.

    -   Lorsque vous ajoutez un enregistrement A, vous devez définir le champ **Hostname** sur **@** (ce qui correspond au nom de domaine racine, comme **contoso.com**,) ou sur le sous-domaine que vous comptez utiliser (par exemple, **www**). Vous devez définir le champ **Address** sur l'adresse IP de votre site Web Azure.

        > [WACOM.NOTE] Si vous utilisez un enregistrement A, vous devez également ajouter un enregistrement CNAME avec l'une des configurations suivantes :
        >
        > -   Une valeur **Hostname** de **www** avec une valeur **Address** correspondant à **&lt;nomdusite\>.azurewebsites.net**.
        >
        > OU
        >
        > -   Une valeur **Hostname** de **awverify.www** avec une valeur **Address** correspondant à **awverify.&lt;nomdusite\>.azurewebsites.net**.
        >
        > Cet enregistrement CNAME permet à Azure de confirmer que vous possédez le domaine décrit par l'enregistrement A.

5.  Cliquez sur le bouton **Add** pour ajouter l'entrée.

6.  Une fois toutes les entrées ajoutées, cliquez sur le bouton **Save**.

7.  Sélectionnez **Domain Manager** pour revenir à votre liste de domaines.

8.  Cochez la case correspondant à votre domaine cible, puis cliquez à nouveau sur **Manage Templates**.

9.  Sélectionnez le nouveau modèle que vous avez créé au cours des étapes précédentes. Cliquez ensuite sur le lien **place selected domains (1) into this Template**.

    ![Page Create Zone Template de Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png)

Activation du nom de domaine sur votre site Web
-----------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

