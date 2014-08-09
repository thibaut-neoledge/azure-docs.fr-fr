<properties title="Learn how to configure an Azure web site to use a domain name registered with GoDaddy" pageTitle="Configure a GoDaddy domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Configuration d'un nom de domaine personnalisé pour un site Web Azure (GoDaddy)
===============================================================================

[Domaine personnalisé](/fr-fr/documentation/articles/web-sites-custom-domain-name "Domaine personnalisé")[GoDaddy](/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/fr-fr/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Site Web](/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name/ "Sites Web") | [Site Web utilisant Traffic Manager](/fr-fr/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/ "Site Web utilisant Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Cet article contient des instructions sur l'utilisation d'un nom de domaine personnalisé acheté auprès de [Go Daddy](https://godaddy.com) avec le service Sites Web Azure.

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

Ajout d'un enregistrement DNS pour votre domaine personnalisé
-------------------------------------------------------------

Pour associer votre domaine personnalisé à un site Web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par GoDaddy. Pour trouver les outils DNS pour GoDaddy.com, procédez comme suit.

1.  Connectez-vous à votre compte GoDaddy.com et sélectionnez **My Account** puis **Manage your domains**. Enfin, sélectionnez le nom de domaine que vous souhaitez utiliser avec votre site Web Azure.

    ![page des domaines personnalisés de GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2.  Sur la page **Domain details**, sélectionnez l'onglet **DNS Zone File**. Il s'agit de la section qui vous permet d'ajouter et de modifier des enregistrements DNS pour votre nom de domaine. Cliquez sur le bouton **Edit** pour afficher l'éditeur **Zone File Editor**.

    ![Onglet DNS Zone File](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

3.  L'éditeur **Zone File Editor** se compose de différentes sections, une pour chaque type d'enregistrement, la première étant la section pour les enregistrements A **A (Host)**, suivie de celle des enregistrements CNAME (appelée **CNAME (Alias)**). Pour ajouter une entrée, cliquez sur le bouton **Quick Add** sous la section correspondante. Pour modifier une entrée existante, sélectionnez-la et modifiez-en les informations.

    > [WACOM.NOTE] Avant d'ajouter des entrées au fichier de zone, notez que GoDaddy a déjà créé des enregistrements DNS pour les sous-domaines populaires (sous **Host** dans l'éditeur), tels que **email**, **files**, **mail**, etc. Si le nom que vous souhaitez utiliser existe déjà, modifiez l'enregistrement existant plutôt que d'en créer un nouveau.

    -   Lors de l'ajout d'un enregistrement CNAME, vous devez définir le champ **Host** sur le sous-domaine à utiliser. Par exemple, **www**. Vous devez définir le champ **Points to** sur le nom de domaine **.azurewebsites.net** de votre site Web Azure. Par exemple, **contoso.azurwebsites.net**.

        ![éditeur de fichier de zone](./media/web-sites-custom-domain-name/godaddy-quickaddcname.png)

        > [WACOM.NOTE] Si vous utilisez un enregistrement A, vous devez également ajouter un enregistrement CNAME avec l'une des configurations suivantes :
        >
        > -   Une valeur **Host** de **www** dont la valeur **Points to** est définie sur **&lt;nomdevotresiteweb\>.azurewebsites.net**
        >
        > OU
        >
        > -   Une valeur **Host** de **awverify.www** dont la valeur **Points to** est définie sur **awverify.&lt;nomdevotresiteweb\>.azurewebsites.net**
        >
        > Cet enregistrement CNAME permet à Azure de confirmer que vous possédez le domaine décrit par l'enregistrement A.

    -   Lors de l'ajout d'un enregistrement A, vous devez définir le champ **Host** sur **@** (ce qui correspond au nom de domaine racine, comme **contoso.com**), sur \* (un caractère générique pour la correspondance de plusieurs sous-domaines) ou sur le sous-domaine que vous souhaitez utiliser (par exemple, **www**). Vous devez définir le champ **Points to** sur l'adresse IP de votre site Web Azure.

        ![éditeur de fichier de zone pour un enregistrement](./media/web-sites-custom-domain-name/godaddy-quickaddarecord.png)

4.  Lorsque vous avez fini d'ajouter ou de modifier des enregistrements, cliquez sur **Save Zone File** pour enregistrer les modifications.

Activation du nom de domaine sur votre site Web
-----------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

