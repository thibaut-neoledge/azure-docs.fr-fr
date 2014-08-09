<properties title="Learn how to configure an Azure web site to use a domain name registered with Dotster" pageTitle="Configure a Dotster domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, Dotster" description="Learn how to configure an Azure web site to use a domain name registered with Dotster" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Configuration d'un nom de domaine personnalisé pour un site Web Azure (Dotster)
===============================================================================

[Domaine personnalisé](/fr-fr/documentation/articles/web-sites-custom-domain-name "Domaine personnalisé")[GoDaddy](/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/fr-fr/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Site Web](/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name/ "Sites Web") | [Site Web utilisant Traffic Manager](/fr-fr/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/ "Site Web utilisant Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Cet article contient des instructions permettant d'utiliser un nom de domaine personnalisé acheté auprès de [Dotster.com](https://dotster.com) avec le service Sites Web Azure.

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

    ![Options de configuration de domaine Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME.png)

    -   Lors de l'ajout d'un enregistrement CNAME, vous devez définir le champ **Host** sur le sous-domaine à utiliser. Par exemple, **www**. Vous devez définir le champ **Points To** sur le nom de domaine **.azurewebsites.net** de votre site Web Azure. Par exemple, **contoso.azurwebsites.net**.

    -   Lorsque vous ajoutez un enregistrement A, vous devez définir le champ **Host** sur **@** (ce qui correspond au nom de domaine racine, comme **contoso.com**,) ou sur le sous-domaine que vous comptez utiliser (par exemple, **www**). Vous devez définir le champ **Points To** sur l'adresse IP de votre site Web Azure.

        > [WACOM.NOTE] Si vous utilisez un enregistrement A, vous devez également ajouter un enregistrement CNAME avec l'une des configurations suivantes :
        >
        > -   A Une valeur **Host** de **www** dont la valeur **Points To** est définie sur **&lt;nomdevotresiteweb\>.azurewebsites.net**
        >
        > OU
        >
        > -   Une valeur **Host** de **awverify.www** dont la valeur **Points To** est définie sur **awverify.&lt;nomdevotresiteweb\>.azurewebsites.net**
        >
        > Cet enregistrement CNAME permet à Azure de confirmer que vous possédez le domaine décrit par l'enregistrement A.

Activation du nom de domaine sur votre site Web
-----------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

