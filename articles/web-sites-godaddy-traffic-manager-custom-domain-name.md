<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with GoDaddy" pageTitle="Configure a GoDaddy domain name for an Azure web site that uses Traffic Manager" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Configuration d'un nom de domaine personnalisé pour un site Web Azure à l'aide de Traffic Manager (GoDaddy)
===========================================================================================================

[Domaine personnalisé](/en-us/documentation/articles/web-sites-custom-domain-name "Domaine personnalisé")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Site Web](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name/ "Sites Web") | [Site Web utilisant Traffic Manager](/en-us/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/ "Site Web utilisant Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article contient des instructions sur l'utilisation d'un nom de domaine personnalisé acheté auprès de [Go Daddy](https://godaddy.com) avec le service Sites Web Azure.

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

Pour associer votre domaine personnalisé à un site Web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par GoDaddy. Pour trouver les outils DNS pour GoDaddy.com, procédez comme suit.

1.  Connectez-vous à votre compte GoDaddy.com et sélectionnez **My Account** puis **Manage your domains**. Enfin, sélectionnez le nom de domaine que vous souhaitez utiliser avec votre site Web Azure.

    ![page des domaines personnalisés de GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2.  Sur la page **Domain details**, sélectionnez l'onglet **DNS Zone File**. Il s'agit de la section qui vous permet d'ajouter et de modifier des enregistrements DNS pour votre nom de domaine. Cliquez sur le bouton **Edit** pour afficher l'éditeur **Zone File Editor**.

    ![Onglet DNS Zone File](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

3.  L'éditeur **Zone File Editor** se compose de différentes sections, une pour chaque type d'enregistrement, la première étant la section pour les enregistrements A **A (Host)**, suivie de celle des enregistrements CNAME (appelée **CNAME (Alias)**). Pour ajouter une entrée, cliquez sur le bouton **Quick Add** sous la section correspondante. Pour modifier une entrée existante, sélectionnez-la et modifiez-en les informations.

    ![éditeur de fichier de zone](./media/web-sites-custom-domain-name/godaddy-quickaddcname.png)

    > [WACOM.NOTE] Avant d'ajouter des entrées au fichier de zone, notez que GoDaddy a déjà créé des enregistrements DNS pour les sous-domaines populaires (sous **Host** dans l'éditeur), tels que **email**, **files**, **mail**, etc. Si le nom que vous souhaitez utiliser existe déjà, modifiez l'enregistrement existant plutôt que d'en créer un nouveau.

    Lors de l'ajout d'un enregistrement CNAME, vous devez définir le champ **host** sur le sous-domaine à utiliser. Par exemple, **www**. Vous devez définir le champ **Points to** sur le nom de domaine **.trafficmanager.net** du profil Traffic Manager utilisé avec votre site Web Azure. Par exemple, **contoso.trafficmanager.net**.

    > [WACOM.NOTE] Vous devez uniquement utiliser les enregistrements CNAME lorsque vous associez votre nom de domaine personnalisé à un site Web équilibré en charge à l'aide de Traffic Manager.

4.  Lorsque vous avez fini d'ajouter ou de modifier des enregistrements, cliquez sur **Save Zone File** pour enregistrer les modifications.

Activation du site Web Traffic Manager
--------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

