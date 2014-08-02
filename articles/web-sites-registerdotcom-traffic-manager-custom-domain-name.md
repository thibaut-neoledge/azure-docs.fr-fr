<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with Register.com" pageTitle="Configure a Register.com domain name for an Azure web site that uses Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Configuration d’un nom de domaine personnalisé pour un site Web Azure utilisant Traffic Manager (Register.com)
==============================================================================================================

[Domaine personnalisé](/en-us/documentation/articles/web-sites-custom-domain-name "Domaine personnalisé")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Site Web](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name/ "Sites Web") | [Site Web utilisant Traffic Manager](/en-us/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/ "Site Web utilisant Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article contient des instructions permettant d'utiliser un nom de domaine personnalisé acheté auprès de [Register.com](https://register.com) avec le service Sites Web Azure.

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

Pour associer votre domaine personnalisé à un site Web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par Register.com. Pour localiser et utiliser les outils DNS, procédez comme suit.

1.  Connectez-vous à votre compte sur register.com et sélectionnez **Your Account** dans le coin supérieur droit pour afficher vos domaines, puis sélectionnez votre nom de domaine personnalisé.

    ![Page My Account](./media/web-sites-custom-domain-name/rdotcom-myaccount.png)

2.  Faites défiler la page pour accéder à **Advanced Technical Settings**. Les liens de cette section vous permettent de gérer les enregistrements de votre domaine. Pour les enregistrements CNAME, utilisez le lien **Edit Domain Aliases Records**.

    ![Paramètres techniques avancés](./media/web-sites-custom-domain-name/rdotcom-advancedsettingstm.png)

3.  Lorsque vous cliquez sur le bouton **Edit**, vous accédez à un formulaire permettant de modifier les enregistrements existants ou d'en ajouter de nouveaux. Le formulaire est identique pour les enregistrements CNAME et A.

    -   Lors de l'ajout d'un enregistrement CNAME, vous devez définir le champ **.mydomainname.com** sur le sous-domaine à utiliser. Par exemple, **www**. Définissez la valeur **points to** sur le nom de domaine **.trafficmanager.net** du profil Traffic Manager utilisé avec votre site Web Azure. Par exemple, **contoso.trafficmanager.net**. Laissez le champ **Refers to Host Name** sur **Select**, car ce champ n'est pas obligatoire lors de la création d'un enregistrement CNAME à utiliser avec le service Sites Web Azure.

        ![Formulaire cname](./media/web-sites-custom-domain-name/rdotcom-editcnamerecordtm.png)

4.  Lorsque vous avez fini d'ajouter ou de modifier des enregistrements, cliquez sur **Continue** pour vérifier les modifications. Sélectionnez à nouveau **Continue** pour enregistrer les modifications.

Activation du site Web Traffic Manager
--------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

