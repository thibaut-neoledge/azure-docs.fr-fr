<properties title="Securing an Azure Website" pageTitle="Sécurisation d'un site web Azure." description="Learn how to secure an Azure Website." metaKeywords="Azure web site security, azure web site https, azure web site ftps, azure web site ssl, azure web site ssl rewrite" services="web-sites" solutions="" documentationCenter="web" authors="larryfr" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />


#Sécurisation d'une application web sur un site web Azure

Lors du développement d'une application web, il est primordial de s'engager à fournir un service sûr et sécurisé à vos clients. Dans cet article, vous découvrirez les fonctionnalités des sites web Azure qui peuvent sécuriser votre application web.

> [WACOM.NOTE] Ce document n'a pas pour but d'évoquer toutes les questions de sécurité concernant les applications web. Pour démarrer l'étude de la sécurisation des applications web, consultez la page [Open Web Application Security Project (OWASP)] (https://www.owasp.org/index.php/Main_Page), et en particulier la rubrique [top 10 project](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), qui répertorie les 10 problèmes de sécurité actuels les plus critiques des applications web, selon les membres de l'OWASP.

###Sommaire

* [Sécurisation des communications](#https)
* [Sécurisation du développement](#develop)
* [Étapes suivantes](#next)
 
##<a name="https"></a>Sécurisation des communications

Si vous utilisez le nom de domaine ***.azurewebsites.net** créé pour votre site web, vous pouvez immédiatement utiliser HTTPS, car un certificat SSL est fourni pour tous les noms de domaine ***.azurewebsites.net**. Si votre site utilise un [nom de domaine personnalisé](http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-custom-domain-name/), vous pouvez charger un certificat SSL et activer HTTPS pour le domaine personnalisé.

##<a name="develop"></a>Sécurisation du développement 

###Profils et paramètres de publication

Pendant le développement d'applications, la réalisation de tâches de gestion ou de tâches automatiques à l'aide d'utilitaires tels que **Visual Studio**, **Web Matrix**, **Azure PowerShell** ou **l'interface de ligne de commande interplateforme Azure**, vous pouvez utiliser un fichier de *paramètres de publication* ou de *profil de publication*. Les deux vous authentifient auprès d'Azure et doivent être sécurisés pour empêcher tout accès sans autorisation.

* Un fichier de **paramètres de publication** contient :

	* L'identifiant de votre abonnement Azure.

	* Un certificat de gestion qui vous permet de réaliser des tâches de gestion pour votre abonnement *sans avoir à fournir un nom de compte ou un mot de passe*.

* Un fichier de **profil de publication** contient :

	* Des informations pour publier sur votre site web Azure

Si vous faites appel à un utilitaire qui utilise des paramètres de publication ou un profil de publication, importez le fichier contenant les paramètres ou le profil de publication dans l'utilitaire, puis **supprimez** le fichier. Si vous devez garder le fichier, comme par exemple pour le partager avec d'autres personnes travaillant sur le projet, stockez-le dans un emplacement sécurisé comme un répertoire **chiffré** avec autorisations restreintes.

De plus, assurez-vous que les informations d'identification importées sont sécurisées. Par exemple, **Azure PowerShell** et **l'interface de ligne de commande interplateforme Azure** stockent les informations importées dans votre **répertoire de base** (*~* sous les systèmes Linux ou OS X et */utilisateurs/nomdutilisateur* sous les systèmes Windows.) Pour plus de sécurité, vous pouvez **chiffrer** ces emplacements à l'aide d'outils de chiffrement disponibles pour votre système d'exploitation.

###Paramètres de configuration et chaînes de connexion
Il est d'usage courant de stocker les chaînes de connexion, les informations d'identification et autres informations sensibles dans les fichiers de configuration. Malheureusement, ces fichiers peuvent être exposés sur votre site web ou déposés dans un référentiel public, exposant ces informations.

Les sites Web Azure vous permettent de stocker des informations de configuration comme éléments d'environnement d'exécution de Sites Web sous forme de **paramètres d'application** et de **chaînes de connexion**. Ces valeurs sont exposées sur votre application au moment de l'exécution via des *variables d'environnement* pour la plupart des langages de programmation. Pour les applications .NET, ces valeurs sont injectées dans votre configuration .NET au moment de l'exécution.

Les **paramètres d'application** et les **chaînes de connexion** sont configurables via le portail de gestion Azure ou les utilitaires comme PowerShell ou l'interface de ligne de commande interplateforme Azure.

Pour plus d'informations sur les paramètres d'application et les chaînes de connexion, consultez la page [Configuration de Sites Web](/fr-fr/documentation/articles/web-sites-configure/).

###FTPS

Azure fournit un accès FTP sécurisé au système de fichiers pour votre site web via **FTPS**. Ceci vous permet d'accéder en toute sécurité au code d'application sur le site web ainsi qu'aux journaux de diagnostic. Le lien FTPS de votre site web se trouve dans le **Tableau de bord** du site, dans le [portail de gestion Azure](https://manage.windowsazure.com).

Pour plus d'informations sur FTPS, consultez la page [Protocole FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

##Étapes suivantes

Pour plus d'informations sur la sécurité de la plateforme Azure ou pour savoir comment signaler **un incident de sécurité ou un abus**, ou pour informer Microsoft que vous allez exécuter un **test de pénétration** de votre site, consultez la section Sécurité du [Centre de gestion de la confidentialité Microsoft Azure](/fr-fr/support/trust-center/security/).

Pour plus d'informations sur les fichiers **web.config** ou **applicationhost.config** dans Sites Web Azure, consultez [Options de configuration déverrouillées dans les sites web Azure](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/).

Pour des détails sur les informations de journalisation pour les sites Web Azure, qui peuvent être utiles pour détecter les attaques, consultez la page [Activation de la journalisation des diagnostics](/fr-fr/documentation/articles/web-sites-enable-diagnostic-log/).
