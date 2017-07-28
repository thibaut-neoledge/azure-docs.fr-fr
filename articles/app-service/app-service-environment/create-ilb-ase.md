---
title: "Créer et utiliser un équilibreur de charge interne avec un environnement Azure App Service Environment"
description: "Informations détaillées sur la création et l’utilisation d’un environnement Azure App Service Environment isolé d’Internet"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 8bc82a297ef8e3f1beac773a6a3b0c4a9b80334f
ms.contentlocale: fr-fr
ms.lasthandoff: 06/26/2017

---
# <a name="create-and-use-an-internal-load-balancer-with-an-app-service-environment"></a>Créer et utiliser un équilibreur de charge interne avec un environnement Azure App Service Environment #

L’environnement App Service Environment (ASE) est un déploiement du service Azure App Service dans un sous-réseau de votre réseau virtuel Azure. Vous pouvez déployer un environnement ASE de deux façons : 

- à l’aide d’une adresse IP virtuelle sur une adresse IP externe ; cet environnement est souvent appelé _ASE externe_ ;
- avec l’adresse IP virtuelle sur une adresse IP interne ; cet environnement est souvent appelé _ASE ILB_, car le point de terminaison interne est un équilibreur de charge interne (ILB, Internal Load Balancer). 

Cet article vous explique comment créer un environnement _ASE ILB_.  Pour une vue d’ensemble de l’environnement ASE, vous pouvez commencer par l’article [An Introduction to the App Service Environment (Introduction à l’environnement App Service Environment)][Intro]. Si vous souhaitez découvrir comment créer un environnement ASE externe, commencez par l’article [Creating an External ASE (Création d’un environnement ASE externe)][MakeExternalASE].

## <a name="overview"></a>Vue d'ensemble ##

Un environnement ASE peut être déployé avec un point de terminaison accessible via Internet ou une adresse IP dans votre réseau virtuel. Pour définir l’adresse IP sur une adresse de réseau virtuel, vous devez déployer l’environnement ASE avec un équilibreur de charge interne. Lorsque vous déployez votre environnement ASE avec un équilibreur de charge interne, vous devez indiquer :

-   votre domaine avec lequel les applications sont créées ;
-   le certificat utilisé pour le protocole HTTPS
-   la gestion du service DNS pour votre domaine.

En retour, vous pouvez effectuer des tâches telles que :

-   héberger des applications intranet en toute sécurité dans le cloud, auquel vous accédez via un réseau privé virtuel (VPN, Virtual Private Network) de site à site ou ExpressRoute ;
-   héberger des applications dans le cloud qui ne figurent pas dans les serveurs DNS publics
-   créer des applications principales isolées d’Internet auxquelles vos applications frontales peuvent s’intégrer en toute sécurité.

***Fonctionnalités désactivées***

Lorsque vous utilisez un environnement ASE ILB, les opérations suivantes ne sont pas autorisées :

-   utilisation du protocole SSL basé sur IP ;
-   attribution d’adresses IP à des applications spécifiques
-   achat et utilisation d’un certificat avec une application via le portail. Cependant, vous pouvez toujours obtenir des certificats directement auprès d’une autorité de certification et les utiliser avec vos applications, mais pas via le portail Azure.

## <a name="create-an-ilb-ase"></a>Créer un environnement ASE ILB ##

Pour créer un ILB ASE :

1.  Dans le portail Azure, sélectionnez **Nouveau -&gt; Web + mobile -&gt;App Service Environment**.
2.  Sélectionnez votre abonnement.
3.  Sélectionnez ou créez un groupe de ressources.
4.  Sélectionnez ou créez un réseau virtuel.
5.  Créez un sous-réseau si vous sélectionnez un réseau virtuel. Veillez à définir une taille de sous-réseau suffisamment grande pour s’adapter à toute croissance éventuelle à venir de votre environnement ASE. La taille `/25` est recommandée. Elle comprend 128 adresses et peut gérer un environnement ASE dont la taille serait maximale. Vous ne pouvez pas utiliser une taille `/29` de sous-réseau ou inférieure.  L’infrastructure doit utiliser au moins 5 adresses.  Même avec une taille de `/28`, vous devez disposer d’une mise à l’échelle maximale de 11 instances dans un sous-réseau `/28`. Si vous pensez avoir besoin un jour de plus du maximum de 100 instances par défaut pour vos plans App Service ou si vous devez mettre à l’échelle près de 100 instances avec une mise à l’échelle frontale plus rapide, utilisez alors une taille /24 avec 256 adresses.
6.  Sélectionnez **Réseau virtuel/Emplacement -&gt;Configuration du réseau virtuel**, puis définissez le **Type d’adresse IP virtuelle** sur **Interne**.
7.  Indiquez le nom de domaine. Il s’agit du domaine utilisé pour les applications créées dans cet environnement ASE. Quelques restrictions s’appliquent. Le nom de domaine ne peut pas être :
    - net
    - azurewebsites.net
    - p.azurewebsites.net
    - &lt;asename&gt;.p.azurewebsites.net

    En outre, si vous envisagez d’utiliser un nom de domaine personnalisé pour les applications qui sont hébergées avec cet environnement ASE, vous ne pouvez pas avoir de chevauchement entre le nom de domaine personnalisé et le nom de domaine utilisé par votre environnement ASE. Pour un environnement ASE ILB pourvu du nom de domaine _contoso.com_, vous ne pouvez pas utiliser de noms de domaine personnalisés pour vos applications, du type :
    - www.contoso.com
    - abcd.def.contoso.com
    - abcd.contoso.com

    Si vous connaissez les noms de domaine personnalisés que vous voulez utiliser avec les applications que vous déployez dans votre environnement ASE ILB, choisissez un domaine pour cet environnement pendant sa création, qui ne créera pas de conflit. Dans cet exemple, il peut s’agir du nom de domaine _contoso-internal.com_.
8.  Sélectionnez **OK**, puis **Créer**.

    ![][1]

Le panneau Réseau virtuel contient une option **Configuration du réseau virtuel**. Elle vous permet de sélectionner une adresse VIP externe ou interne. La valeur par défaut est **Externe**. Si vous sélectionnez **Externe**, votre environnement ASE utilisera une adresse IP virtuelle accessible via Internet. Si vous sélectionnez **Interne**, votre environnement ASE sera configuré avec un équilibreur de charge interne sur une adresse IP au sein de votre réseau virtuel.

Après que vous avez sélectionné **Interne**, vous ne pouvez plus ajouter d’adresses IP à votre environnement ASE, et vous devez indiquer le domaine de l’environnement ASE. Dans un environnement ASE pourvu d’une adresse IP virtuelle externe, le nom de l’environnement ASE est utilisé dans le domaine pour les applications créées dans cet environnement ASE.

Si vous définissez **Type d’adresse IP virtuelle** sur **Interne**, le nom de votre environnement ASE n’est pas utilisé dans le domaine de l’environnement ASE. Vous spécifiez explicitement le domaine. Si votre domaine est ***contoso.corp.net*** et que créez une application dans cet environnement ASE nommé ***timereporting***, l’URL de cette application est ***timereporting.contoso.corp.net***.

## <a name="apps-in-an-ilb-ase"></a>Applications d’un ILB ASE ##

La création d’une application dans un ILB ASE est identique à la création d’une application dans un ASE standard.

1.  Dans le portail Azure, sélectionnez **Nouveau&gt; Web + mobile &gt;Web** ou **Mobile** ou **Application API**.
2.  Entrez le nom de l’application.
3.  Sélectionnez l’abonnement.
4.  Sélectionnez ou créez un groupe de ressources.
5.  Sélectionnez ou créez un plan App Service. Si vous souhaitez créer un plan App Service, sélectionnez votre environnement ASE comme emplacement, puis le pool de travail dans lequel vous voulez créer votre plan App Service. Lorsque vous créez le plan App Service, vous sélectionnez votre environnement ASE comme emplacement ainsi que le pool de travail. Lorsque vous spécifiez le nom de l’application, vous voyez que le domaine sous le nom de votre application est remplacé par celui de votre environnement ASE.
6.  Sélectionnez **Créer**. Vous devriez cocher la case **Épingler au tableau de bord** si vous souhaitez que l’application s’affiche sur votre tableau de bord.

    ![][2]

Sous le nom de l’application, le nom du domaine est mis à jour pour refléter le domaine de votre environnement ASE.

## <a name="post-ilb-ase-creation-validation"></a>Validation après la création de l’ILB ASE ##

Un ILB ASE est légèrement différent d’un ASE non-ILB. Comme indiqué précédemment, vous devez gérer votre propre service DNS et fournir votre propre certificat pour les connexions HTTPS.

Après avoir créé votre environnement ASE, vous remarquerez que le nom de domaine affiche le domaine que vous avez spécifié, et qu’un nouvel élément apparaît dans le menu **Paramètre**, appelé **Certificat ILB**. L’environnement ASE est créé avec un certificat qui ne spécifie pas le domaine de l’environnement ASE ILB. Si vous utilisez l’environnement ASE avec ce certificat, votre navigateur vous indique qu’il n’est pas valide. Ce certificat facilite le test du protocole HTTPS, mais il est courant que vous deviez charger votre propre certificat associé au domaine de votre environnement ASE ILB, qu’il soit auto-signé ou acquis auprès d’une autorité de certification.

![][3]

Il existe plusieurs façons d’obtenir un certificat SSL valide, parmi lesquelles les autorités de certification internes, l’achat d’un certificat auprès d’un émetteur externe et l’utilisation d’un certificat auto-signé. Quelle que soit la source du certificat SSL, les attributs de certificat suivants doivent être configurés correctement :

-   _Objet_ : cet attribut doit être défini sur _\*.votre-domaine-racine-ici_.
-   _Autre nom de l’objet_ : cet attribut doit inclure à la fois _\*.votre-domaine-racine-ici_ et _\*.scm.votre-domaine-racine-ici_. La seconde entrée est nécessaire, car les connexions SSL au site SCM/Kudu associé à chaque application sont établies à l’aide d’une adresse de la forme _votre-nom-application.scm.votre-domaine-racine-ici_.

Une fois le certificat SSL valide obtenu, deux étapes préparatoires supplémentaires sont nécessaires. Le certificat SSL doit être converti/enregistré au format de fichier .pfx. N’oubliez pas que le fichier .pfx doit inclure tous les certificats intermédiaires et racine et être sécurisé avec un mot de passe.

Pour créer votre propre certificat à l’aide de PowerShell, vous pouvez utiliser les commandes affichées ci-dessous.  Veillez à utiliser le nom de domaine de votre environnement ASE ILB à la place du domaine *internal.contoso.com*. 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "\*.internal-contoso.com","\*.scm.internal-contoso.com"
    
    $certThumbprint = "cert:\localMachine\my\" +$certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText
    
    $fileName = "exportedcert.pfx" 
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password

Le certificat que ces commandes PowerShell génèrent est toujours signalé par les navigateurs, car il n’est pas créé par une autorité de certification se trouvant dans la chaîne d’approbation utilisée par votre navigateur. La seule façon d’obtenir un certificat avec lequel votre navigateur n’aura pas de problèmes est de vous le procurer auprès d’une autorité de certification commerciale se trouvant dans la chaîne d’approbation du navigateur.  

![][4]

Pour charger vos propres certificats et tester l’accès :

1.  Accédez à l’interface utilisateur de l’environnement ASE après l’avoir créé (**ASE > Paramètres > Certificats ILB**).
2.  Définissez le certificat ILB en sélectionnant le fichier .pfx du certificat, puis indiquez le mot de passe. Cette étape prend un certain temps, et un message indiquant qu’un chargement est en cours s’affiche.
3.  Obtenez l’adresse ILB de votre environnement ASE (**ASE > Propriétés > Adresse IP virtuelle**).
4.  Créez une application web dans votre environnement ASE après l’avoir créé.
5.  Créez une machine virtuelle si vous n’en avez pas dans ce réseau virtuel.

    > [!NOTE] 
    > Ne créez pas cette machine virtuelle dans le même sous-réseau que celui de l’environnement ASE, car cela risque d’interrompre votre configuration.
    >
    >

6.  Définissez le service DNS pour le domaine de votre environnement ASE. Vous pouvez utiliser un caractère générique avec votre domaine dans votre service DNS ou, si vous voulez réaliser de simples tests, modifier le fichier hosts sur votre machine virtuelle pour définir le nom de l’application web sur l’adresse IP virtuelle. Si votre environnement ASE est pourvu du nom de domaine _.ilbase.com_ et que vous créez l’application web nommée _mytestapp_, son adresse sera _mytestapp.ilbase.com_. Vous devez ensuite définir _mytestapp.ilbase.com_ pour résoudre cette adresse en adresse ILB. (Sur Windows, le fichier hosts se situe sous _C:\Windows\System32\drivers\etc\_). Si vous souhaitez tester la publication du déploiement web ou l’accès à la console avancée, vous devez également créer un enregistrement pour _mytestapp.scm.ilbase.com_.
7.  Utilisez un navigateur sur cette machine virtuelle, puis accédez à ***http://mytestapp.ilbase.com*** (ou au nom de votre application web avec votre domaine).
8.  Utilisez un navigateur sur cette machine virtuelle, puis accédez à ***https://mytestapp.ilbase.com***. Vous devez accepter le manque de sécurité si vous utilisez un certificat auto-signé.

L’adresse IP de votre équilibreur de charge interne est répertoriée sous **Adresses IP**. Cette section contient également les adresses IP utilisées par l’adresse IP virtuelle externe et pour le trafic de gestion entrant.

![][5]

### <a name="functions-and-the-ilb-ase"></a>Fonctions et environnement ASE ILB

Si vous utilisez Fonctions dans un environnement ASE ILB, vous pouvez rencontrer le message d’erreur indiquant : *« Nous ne pouvons pas récupérer vos fonctions pour l’instant.  Réessayez plus tard. »*  Cela est dû au fait que l’interface utilisateur de Fonctions tire parti du site SCM via le protocole HTTPS.  Si vous utilisez un certificat HTTP pour votre environnement ASE qui est dépourvu d’un certificat racine qui est dans le navigateur, vous pouvez rencontrer ce message d’erreur.  En outre, les navigateurs IE\Edge ne partagent pas le paramètre *accept-invalid-cert* entre les onglets. Vous pouvez donc :

- ajouter le certificat à votre magasin de certificats approuvé ; 
- ou utiliser Chrome, mais vous devez accéder d’abord au site SCM, accepter le certificat non approuvé, puis accéder au portail.

## <a name="dns-configuration"></a>Configuration DNS ##

Lorsque vous utilisez une adresse IP virtuelle externe, le service DNS est géré par Azure. Toute application créée dans votre environnement ASE est automatiquement ajoutée au service Azure DNS, qui est un service DNS public. Dans un environnement ASE ILB, vous devez gérer votre propre service DNS. Pour un domaine donné tel que _contoso.net_, vous devez créer des enregistrements DNS A dans votre service DNS, qui pointent vers votre adresse ILB pour :

- *.contoso.net
- *.scm.contoso.net

Si le domaine de votre environnement ASE ILB est utilisé pour plusieurs éléments en dehors de cet environnement, vous devrez peut-être gérer le service DNS nom d’application par nom d’application. Cela s’avère beaucoup plus complexe, car vous devez ajouter chaque nom de nouvelle application dans votre service DNS lorsque vous la créez. C’est pour cette raison que l’utilisation d’un domaine dédié est recommandée.

## <a name="publishing-with-an-ilb-ase"></a>Publication avec un environnement ASE ILB ##

Pour chaque application créée, il existe deux points de terminaison. Dans un environnement ASE ILB, vous disposez des points de terminaison *&lt;nom_application>.&lt;domaine_ASE_ILB>* et *&lt;nom_application>.scm.&lt;domaine_ASE_ILB>*. 

Le nom du site SCM vous dirige vers la console Kudu, qui est appelée **portail avancé** dans le portail Azure. La console Kudu vous permet d’effectuer de nombreuses opérations, notamment d’afficher des variables d’environnement, d’explorer le disque, d’utiliser une console, et bien plus encore. Pour plus d’informations, voir [Kudu console for Azure App Service (Console Kudu pour Azure App Service)][Kudu]. 

L’architecture mutualisée d’App Service et un environnement ASE externe comportent l’authentification unique entre le portail Azure et la console Kudu. Pour l’environnement ASE ILB, vous devez néanmoins utiliser vos informations d’identification de publication pour vous connecter à la console Kudu.

Les systèmes d’intégration continue basés sur Internet, comme Github et VSTS (Visual Studio Team Services) ne fonctionnent pas avec un environnement ASE ILB, car le point de terminaison de publication n’est pas accessible via Internet. À la place, vous devez utiliser un système d’intégration continue utilisant un modèle d’extraction, par exemple Dropbox.

Les points de terminaison de publication pour les applications d’un environnement ASE ILB utilisent le domaine avec lequel l’environnement ASE ILB a été créé. Ce peut être observé dans le profil de publication de l’application et dans le panneau du portail de l’application (dans **Vue d’ensemble** > **Essentials** et également dans **Propriétés**). Si votre environnement ASE ILB est pourvu du sous-domaine *contoso.net* et d’une application nommée *Montest*, vous vous connectez au site FTP sur *montest.contoso.net* et effectuez le déploiement web sur *montest.scm.contoso.net*.

## <a name="coupling-an-ilb-ase-with-a-waf-device"></a>Couplage d’un environnement ASE ILB avec un périphérique WAF ##

Azure App Service fournit de nombreuses mesures de sécurité permettant de protéger le système et vous aidant à déterminer si une application a été piratée. La meilleure protection pour une application web consiste à associer une plateforme d’hébergement, par exemple le service Azure App Service, à un pare-feu d’applications web (WAF). Comme l’environnement ASE ILB possède un point de terminaison d’application isolé du réseau, il convient parfaitement à une telle utilisation.  

Pour en savoir plus sur la configuration de votre environnement ASE ILB avec un périphérique WAF, voir [Configuration d’un pare-feu d’applications web (WAF) pour un environnement App Service][ASEWAF]. Cet article indique comment utiliser une appliance virtuelle Barracuda avec votre environnement ASE. Une autre option consiste à utiliser le service Azure Application Gateway. Le service Application Gateway utilise les règles de base OWASP pour sécuriser les applications placées derrière lui. Pour plus d’informations sur le service Azure Application Gateway, voir [Pare-feu d’applications web (WAF)][AppGW].

### <a name="getting-started"></a>Prise en main ###

Tous les articles et procédures concernant les environnements App Service sont disponibles dans le [fichier LISEZMOI correspondant][ASEReadme].

Pour prendre en main les environnements App Service Environment, voir [Introduction to App Service Environments (Introduction aux environnements App Service Environment)][Intro].

Pour plus d’informations sur la plateforme Azure App Service, consultez la rubrique [Azure App Service](http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/).
 
<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[3]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate.png
[4]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate2.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md

