---
title: "Sécurisation d'une application dans Azure App Service"
description: "Découvrez comment sécuriser une application web, un serveur principal d’application mobile ou une application API dans Azure App Service."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 5ce560b4-42d7-4b20-935c-0445fd539e39
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/12/2016
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: b70d74441f3d6d9793ae516b3f04e36e786a9a8f
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017


---
# <a name="secure-an-app-in-azure-app-service"></a>Sécurisation d'une application dans Azure App Service
Cet article vous aide à démarrer la sécurisation de votre application web, de votre serveur principal d’application mobile ou de votre application API dans Azure App Service. 

Il existe deux niveaux de sécurité dans Azure App Service. 

* **Sécurité de la plateforme et de l’infrastructure** - Vous pouvez faire confiance à Azure pour disposer des services dont vous avez besoin pour exécuter vos processus dans le cloud en toute sécurité.
* **Sécurité des applications** - Vous devez pouvoir concevoir l’application elle-même en toute sécurité. Il s’agit entre autres de l’intégration dans Azure Active Directory, ainsi que de la manière dont vous gérez les certificats et dont vous vous assurez que vous pouvez appeler différents services en toute sécurité. 

#### <a name="infrastructure-and-platform-security"></a>Sécurité de la plateforme et de l’infrastructure
Étant donné qu’App Service gère les machines virtuelles Azure, le stockage, les connexions réseau, les infrastructures web, les fonctionnalités de gestion et d’intégration et bien plus encore, il est activement sécurisé et renforcé et fait l’objet de contrôles de conformité et de tests continus pour vous assurer que :

* vos applications App Service sont isolées à la fois d’Internet et des ressources Azure d’autres clients ;
* la communication de clés secrètes (par exemple, des chaînes de connexion) entre votre application App Service et d’autres ressources Azure (par exemple, la base de données SQL) dans un groupe de ressources reste dans Azure et ne franchit par les limites du réseau. Les clés secrètes sont toujours chiffrées ;
* toutes les communications entre votre application App Service et des ressources externes, telles que la gestion de PowerShell, une interface de ligne de commande, des kits de développement logiciel Azure, des API REST et des connexions hybrides, sont correctement chiffrés ;
* la gestion continue des menaces protège les ressources App Service contre les programmes malveillants, le déni de service distribué (DDoS), les attaques de l’intercepteur (man-in-the-middle, MITM) et bien d’autres menaces. 

Pour plus d’informations sur la sécurité de l’infrastructure et de la plateforme dans Azure, consultez [Centre de gestion de la confidentialité Azure](https://azure.microsoft.com/support/trust-center/security/).

#### <a name="application-security"></a>Sécurité des applications
Si Azure est responsable de la sécurisation de l’infrastructure et de la plateforme sur laquelle votre application s’exécute, il vous incombe de sécuriser votre application elle-même. En d’autres termes, vous devez développer, déployer et gérer votre code d’application et le contenu de manière sécurisée. Sinon, votre code d’application ou le contenu peut encore être vulnérable aux menaces telles que :

* l’injection de code SQL ;
* le piratage de session ;
* l’exécution de scripts de site à site ;
* les attaques de l’intercepteur au niveau des applications ;
* le déni de service distribué au niveau des applications.

Ce document n’a pas pour but d’évoquer toutes les questions de sécurité concernant les applications web. Pour démarrer l’étude de la sécurisation de votre application, consultez la page [Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page), et en particulier la rubrique [top 10 project](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), qui répertorie les 10 problèmes de sécurité actuels les plus critiques des applications web selon les membres de l’OWASP.

## <a name="perform-penetration-testing-on-your-app"></a>Réalisation de tests d’intrusion sur votre application
L’une des méthodes les plus simples pour démarrer les tests de vulnérabilité de votre application App Service consiste à utiliser l’ [intégration dans Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) pour effectuer l’analyse des vulnérabilités sur votre application en un clic. Vous pouvez afficher les résultats des tests dans un rapport facile à comprendre et découvrir comment résoudre chaque problème de vulnérabilité grâce à des instructions pas à pas.

Si vous préférez effectuer vos propres tests d’intrusion ou utiliser une autre suite logicielle d’analyse ou un autre fournisseur, vous devez suivre le [processus d’approbation de test d’intrusion Azure](https://security-forms.azure.com/penetration-testing/terms) et obtenir une approbation préalable pour effectuer les tests d’intrusion de votre choix.

## <a name="https"></a> Sécurisation des communications avec les clients
Si vous utilisez le nom de domaine **\*.azurewebsites.net** créé pour votre application App Service, vous pouvez utiliser immédiatement le protocole HTTPS, car un certificat SSL est fourni pour tous les noms de domaine **\*.azurewebsites.net**. Si votre site utilise un [nom de domaine personnalisé](app-service-web-tutorial-custom-domain.md), vous pouvez charger un certificat SSL et [activer HTTPS](app-service-web-tutorial-custom-ssl.md) pour le domaine personnalisé.

L’activation du protocole [HTTPS](https://en.wikipedia.org/wiki/HTTPS) protège la communication entre votre application et ses utilisateurs contre les attaques de l’intercepteur.

## <a name="secure-data-tier"></a>Sécurisation de la couche Données
App Service est hautement intégré dans la base de données SQL, de sorte que toutes les chaînes de connexion sont chiffrées sur le panneau et sont décryptées uniquement sur la machine virtuelle sur laquelle l’application s’exécute *et* uniquement lorsque l’application s’exécute. En outre, Azure SQL Database comprend plusieurs fonctionnalités de sécurité qui protègent vos données d’application des cyber-menaces, notamment [le chiffrement au repos](https://msdn.microsoft.com/library/dn948096.aspx), [le chiffrement intégral (Always Encrypted)](https://msdn.microsoft.com/library/mt163865.aspx), [le masquage dynamique des données (Dynamic Data Masking)](../sql-database/sql-database-dynamic-data-masking-get-started.md) et [la détection des menaces](../sql-database/sql-database-threat-detection.md). Si vous avez des données sensibles ou devez répondre à des exigences de conformité, consultez la rubrique [Sécurisation de votre base de données SQL](../sql-database/sql-database-security-overview.md) pour plus d’informations sur la manière de sécuriser vos données.

Si vous utilisez un fournisseur de base de données tiers, tels que ClearDB, vous devez consulter directement la documentation du fournisseur quant aux meilleures pratiques de sécurité.  

## <a name="develop"></a> Déploiement et développement sécurisés
### <a name="publishing-profiles-and-publish-settings"></a>Profils et paramètres de publication
Pendant le développement d’applications, la réalisation de tâches de gestion ou l’automatisation de tâches à l’aide d’utilitaires tels que **Visual Studio**, **Web Matrix**, **Azure PowerShell** ou l’**interface de ligne de commande Azure (CLI Azure)**, vous pouvez utiliser un fichier de *paramètres de publication* ou de *profil de publication*. Les deux types de fichier vous authentifient auprès d’Azure et doivent être sécurisés pour empêcher tout accès non autorisé.

* Un fichier de **paramètres de publication** contient :
  
  * L'identifiant de votre abonnement Azure.
  * Un certificat de gestion qui vous permet de réaliser des tâches de gestion pour votre abonnement *sans avoir à fournir un nom de compte ou un mot de passe*.
* Un fichier de **profil de publication** contient :
  
  * Informations sur la publication de votre application

Si vous faites appel à un utilitaire qui utilise un fichier de paramètres de publication ou de profil de publication, importez-le dans l'utilitaire, puis **supprimez** le fichier. Si vous devez garder le fichier, comme par exemple pour le partager avec d'autres personnes travaillant sur le projet, stockez-le dans un emplacement sécurisé comme un répertoire *chiffré* avec autorisations restreintes.

De plus, assurez-vous que les informations d’identification importées sont sécurisées. Par exemple, **Azure PowerShell** et l’**interface de ligne de commande Azure (Azure CLI)** stockent tous deux les informations importées dans votre **répertoire de base** (*~* sur les systèmes Linux ou OS X et */users/votrenomutilisateur* sur les systèmes Windows). Pour plus de sécurité, vous pouvez **chiffrer** ces emplacements à l’aide d’outils de chiffrement disponibles pour votre système d’exploitation.

### <a name="configuration-settings-and-connection-strings"></a>Paramètres de configuration et chaînes de connexion
Il est d'usage courant de stocker les chaînes de connexion, les informations d'identification et autres informations sensibles dans les fichiers de configuration. Malheureusement, ces fichiers peuvent être exposés sur votre site web, ou déposés dans un référentiel public, exposant ces informations. Une recherche simple dans [GitHub](https://github.com), par exemple, peut renvoyer d’innombrables fichiers de configuration avec des clés secrètes exposées dans les référentiels publics.

La meilleure pratique consiste à conserver ces informations en dehors des fichiers de configuration de votre application. App Service vous permet de stocker des informations de configuration dans l’environnement d’exécution en tant que **paramètres d’application** et **chaînes de connexion**. Ces valeurs sont exposées sur votre application au moment de l'exécution via des *variables d'environnement* pour la plupart des langages de programmation. Pour les applications .NET, ces valeurs sont injectées dans votre configuration .NET au moment de l’exécution. En dehors de ces cas, ces paramètres de configuration restent chiffrés, à moins que vous les affichiez ou les configuriez en utilisant le [portail Azure](https://portal.azure.com) ou des utilitaires tels que PowerShell ou l’interface de ligne de commande Azure. 

Le stockage d’informations de configuration dans App Service permet à l’administrateur de l’application de verrouiller les informations sensibles pour les applications de production. Les développeurs peuvent utiliser un ensemble de paramètres de configuration distinct pour le développement d’applications. Ils peuvent être automatiquement remplacés par les paramètres configurés dans App Service. Même les développeurs ne doivent pas connaître les clés secrètes configurées pour l’application de production. Pour plus d’informations sur la configuration des paramètres d’application et des chaînes de connexion dans App Service, consultez [Configuration des applications web](web-sites-configure.md).

### <a name="ftps"></a>FTPS
Azure App Service fournit un accès FTP sécurisé au système de fichiers de votre application par le biais de **FTPS**. Ceci vous permet d’accéder en toute sécurité au code de l’application web ainsi qu’aux journaux de diagnostic. Il est recommandé de toujours utiliser FTPS au lieu de FTP. 

Vous pouvez rechercher le lien FTPS pour votre application en procédant comme suit :

1. Ouvrez le [portail Azure](https://portal.azure.com).
2. Sélectionnez **Parcourir tout**.
3. Dans le panneau **Parcourir**, sélectionnez **App Services**.
4. Dans le panneau **App Services** , sélectionnez l’application souhaitée.
5. Dans le panneau de l’application, sélectionnez **Tous les paramètres**.
6. Dans le panneau **Paramètres**, sélectionnez **Propriétés**.
7. Les liens FTP et FTPS sont fournis dans le panneau **Paramètres** . 

Pour plus d'informations sur FTPS, consultez la page [Protocole FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la sécurité de la plateforme Azure, ou pour savoir comme signaler **un incident de sécurité ou un abus**, ou pour informer Microsoft que vous allez exécuter un **test de pénétration** de votre site, consultez la section Sécurité du [Centre de gestion de la confidentialité Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

Pour plus d’informations sur les fichiers **web.config** ou **applicationhost.config** des applications App Service, consultez la page [Options de configuration déverrouillées dans les applications web Azure App Service](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/).

Pour plus d’informations sur la journalisation des informations des applications App Service, également utile pour détecter les attaques, consultez la page [Activer la journalisation des diagnostics](web-sites-enable-diagnostic-log.md).

> [!NOTE]
> Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](https://azure.microsoft.com/try/app-service/), où vous pourrez créer immédiatement une application temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
> 
> 

## <a name="whats-changed"></a>Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)


