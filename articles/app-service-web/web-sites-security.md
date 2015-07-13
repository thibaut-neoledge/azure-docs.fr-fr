<properties
	pageTitle="Sécuriser une application web dans Azure App Service"
	description="Découvrez comment sécuriser une application web Azure."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="cephalin"/>


#Sécuriser une application web dans Azure App Service

Lors du développement d’une application web, il est primordial de s’engager à fournir un service sûr et sécurisé à vos clients. Dans cet article, vous allez découvrir les fonctionnalités d’[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) qui peuvent sécuriser votre application web.

> [AZURE.NOTE]Ce document n’a pas pour but d’évoquer toutes les questions de sécurité concernant les applications web. Pour démarrer l'étude de la sécurisation des applications web, consultez la page [Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page), et en particulier la rubrique [top 10 project](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), qui répertorie les 10 problèmes de sécurité actuels les plus critiques des applications web, selon les membres de l'OWASP.

##<a name="https"></a> Sécuriser les communications

Si vous utilisez le nom de domaine ***.azurewebsites.net** créé pour votre application web, vous pouvez utiliser immédiatement le protocole HTTPS, car un certificat SSL est fourni pour tous les noms de domaine ***.azurewebsites.net**. Si votre site utilise un [nom de domaine personnalisé](web-sites-custom-domain-name.md), vous pouvez charger un certificat SSL et [activer HTTPS](web-sites-configure-ssl-certificate.md) pour le domaine personnalisé.

##<a name="develop"></a> Sécuriser le développement

### Profils et paramètres de publication

Pendant le développement d’applications, la réalisation de tâches de gestion ou de tâches automatiques à l’aide d’utilitaires tels que **Visual Studio**, **Web Matrix**, **Azure PowerShell** ou l’**interface de ligne de commande Azure (CLI Azure)**, vous pouvez utiliser un fichier de *paramètres de publication* ou de *profil de publication*. Les deux vous authentifient auprès d’Azure et doivent être sécurisés pour empêcher tout accès sans autorisation.

* Un fichier de **paramètres de publication** contient :

	* L'identifiant de votre abonnement Azure.

	* Un certificat de gestion qui vous permet de réaliser des tâches de gestion pour votre abonnement *sans avoir à fournir un nom de compte ou un mot de passe*.

* Un fichier de **profil de publication** contient :

	* Des informations sur la publication de votre application web.

Si vous faites appel à un utilitaire qui utilise des paramètres de publication ou un profil de publication, importez le fichier contenant les paramètres ou le profil de publication dans l'utilitaire, puis **supprimez** le fichier. Si vous devez garder le fichier, comme par exemple pour le partager avec d'autres personnes travaillant sur le projet, stockez-le dans un emplacement sécurisé comme un répertoire **chiffré** avec autorisations restreintes.

De plus, assurez-vous que les informations d’identification importées sont sécurisées. Par exemple, **Azure PowerShell** et l’**interface de ligne de commande Azure (CLI Azure)** stockent des informations importées dans votre **répertoire de base** (*~* sur les systèmes Linux ou OS X et */users/votrenomutilisateur* sur les systèmes Windows.) Pour plus de sécurité, vous pouvez **chiffrer** ces emplacements à l’aide d’outils de chiffrement disponibles pour votre système d’exploitation.

### Paramètres de configuration et chaînes de connexion
Il est d'usage courant de stocker les chaînes de connexion, les informations d'identification et autres informations sensibles dans les fichiers de configuration. Malheureusement, ces fichiers peuvent être exposés sur votre site web, ou déposés dans un référentiel public, exposant ces informations.

Azure App Service vous permet de stocker les informations de configuration dans l’environnement d’exécution Web Apps en tant que **paramètres d’application** et **chaînes de connexion**. Ces valeurs sont exposées sur votre application au moment de l'exécution via des *variables d'environnement* pour la plupart des langages de programmation. Pour les applications .NET, ces valeurs sont injectées dans votre configuration .NET au moment de l’exécution.

Les **paramètres d’application** et les **chaînes de connexion** sont configurables via le [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715) ou des utilitaires tels que PowerShell ou l’interface de ligne de commande Azure.

Pour plus d’informations sur les paramètres d’application et les chaînes de connexion, consultez la page [Configuration des applications web](web-sites-configure.md).

### FTPS

Azure fournit un accès FTP sécurisé au système de fichiers de votre application web via **FTPS**. Ceci vous permet d’accéder en toute sécurité au code de l’application web ainsi qu’aux journaux de diagnostic. Le lien FTPS de votre application web se trouve dans la page **Tableau de bord** du [portail de gestion Azure](https://manage.windowsazure.com).

Pour plus d'informations sur FTPS, consultez la page [Protocole FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Étapes suivantes

Pour plus d'informations sur la sécurité de la plateforme Azure, ou pour savoir comme signaler **un incident de sécurité ou un abus**, ou pour informer Microsoft que vous allez exécuter un **test de pénétration** de votre site, consultez la section Sécurité du [Centre de gestion de la confidentialité Microsoft Azure](http://azure.microsoft.com/support/trust-center/security/).

Pour plus d’informations sur les fichiers **web.config** ou **applicationhost.config** des applications web, consultez la page [Options de configuration déverrouillées dans Azure App Service Web Apps](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/).

Pour plus d’informations sur la journalisation des informations des applications web, ce qui peut être utile pour détecter les attaques, consultez la page [Activer la journalisation des diagnostics](web-sites-enable-diagnostic-log.md).

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).

* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).
 

<!---HONumber=62-->