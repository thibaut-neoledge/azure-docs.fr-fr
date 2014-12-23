<properties title="Operating System Functionality Available to Applications on Azure Websites" pageTitle="Fonctionnalités de système d'exploitation accessibles aux applications sur Sites Web Azure" description="Learn about the OS functionality available to web applications on Azure Websites" metaKeywords="Azure,Web Sites,web applications,operating system functionality" services="web-sites" solutions="web" documentationCenter="" authors="cephalin" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/11/2014" ms.author="cephalin" />

# Fonctionnalités de système d'exploitation accessibles aux applications sur Sites Web Azure #

Cet article décrit les fonctionnalités de système d'exploitation communes de base accessibles à toutes les applications exécutées sur Sites Web Azure. Ces fonctionnalités englobent notamment l'accès aux fichiers, l'accès réseau et l'accès au registre, ainsi que les journaux et événements de diagnostic. 

##Sommaire

* [Mode des sites Web](#websitemodes)
* [Infrastructures de développement](#developmentframeworks)
* [Accès aux fichiers](#FileAccess)
	* [Lecteurs locaux](#LocalDrives)
	* [Lecteurs réseau (partages UNC)](#NetworkDrives)
	* [Accès aux fichiers sur plusieurs instances](#multipleinstances)
	* [Types d'accès aux fichiers octroyés à une application Web](#TypesOfFileAccess)
* [Accès réseau](#NetworkAccess)
* [Exécution de code, processus et mémoire](#Code)
* [Journaux et événements de diagnostic](#Diagnostics)
* [Accès au registre](#RegistryAccess)

<a id="websitemodes"></a>
<h2>Modes de site web</h2>
Sites Web Azure exécute les sites web des clients dans un environnement d'hébergement multi-locataire. Les sites web déployés dans les modes de mise à l'échelle Gratuit et Partagé sont exécutés dans des processus de travail sur des machines virtuelles partagées, alors que les sites web déployés dans le mode de mise à l'échelle Standard sont exécutés sur des machines virtuelles spécialement dédiées aux sites web associés à un client individuel.

Dans la mesure où Sites Web Azure offre une expérience de mise à l'échelle transparente entre différents modes, la configuration de sécurité appliquée aux sites web reste la même. Cela empêche les changements de comportement soudains des applications web qui peuvent se traduire par un échec imprévu quand un site web passe d'un mode à un autre.

<a id="developmentframeworks"></a>
<h2>Infrastructures de développement</h2>

Le mode détermine la quantité de ressources de calcul (UC, stockage sur disque, mémoire et sortie réseau) accessible aux sites web. Toutefois, l'étendue des fonctionnalités d'infrastructure accessibles aux applications reste la même, quel que soit le mode.

Sites Web Azure prend en charge différentes infrastructures de développement, telles que ASP.NET, ASP, node.js, PHP et Python (toutes fonctionnant en tant qu'extensions sous IIS). Pour simplifier et normaliser la configuration de sécurité, Sites Web Azure exécute généralement les différentes infrastructures de développement avec leurs paramètres par défaut. Une approche de la configuration de Sites Web Azure aurait pu consister à personnaliser les fonctionnalités et la surface d'exposition des API de chaque infrastructure de développement. L'approche retenue pour Sites Web Azure est plus générale, car elle offre une base commune de fonctionnalités de système d'exploitation, quelle que soit l'infrastructure de développement d'application d'un site web.

Les sections suivantes présentent brièvement les principaux types de fonctionnalité de système d'exploitation accessibles aux sites web sur Azure.


<a id="FileAccess"></a>
<h2>Accès aux fichiers</h2>

Différents lecteurs sont disponibles dans Sites Web Azure, notamment les lecteurs locaux et les lecteurs réseau.

<a id="LocalDrives"></a>
<h3>Lecteurs locaux</h3>

Sites Web Azure est un service qui s'exécute au sommet de l'infrastructure PaaS (Platform as a Service) d'Azure. Par conséquent, les disques locaux " associés " à une machine virtuelle correspondent aux types de lecteurs accessibles à tous les rôles de travail exécutés sous Azure. Il peut s'agir d'un lecteur de système d'exploitation (lecteur D:\), d'un lecteur d'application contenant les fichiers .cspkg du package Azure, exclusivement utilisés par Sites Web Azure (et non accessibles aux clients) ou d'un lecteur " utilisateur " (lecteur C:\) dont la taille dépend de celle de la machine virtuelle.

<a id="NetworkDrives"></a>
<h3>Lecteurs réseau (partages UNC)</h3>

Pour simplifier le déploiement et la maintenance des applications web, Sites Web Azure stocke tout le contenu utilisateur sur un ensemble de partages UNC. Ce modèle s'adapte très bien au modèle commun de stockage de contenu utilisé par les environnements d'hébergement Web locaux dotés de plusieurs serveurs à charge équilibrée. 

Sites Web Azure intègre un certain nombre de partages UNC créés dans chaque centre de données. Un certain pourcentage du contenu de l'ensemble des utilisateurs de chaque centre de données est alloué à chacun des partages UNC. En outre, le contenu de tous les fichiers associés à l'abonnement d'un client est systématiquement placé sur le même partage UNC. 

Notez qu'en raison du mode de fonctionnement des services cloud, la machine virtuelle chargée d'héberger un partage UNC changera au fil du temps. Les partages UNC seront en effet associés à différentes machines virtuelles à mesure que celles-ci seront intégrées et écartées lors de l'évolution normale des opérations dans le cloud. C'est la raison pour laquelle les applications Web ne doivent jamais partir du principe que les informations machine présentes dans un chemin de fichier UNC resteront stables au fil du temps. Elles doivent utiliser le *faux* chemin d'accès absolu **D:\home\site** fourni par Sites Web Azure. Ce faux chemin d'accès absolu offre une méthode " portable " qui n'est pas propre au site ni à l'utilisateur pour faire référence à un site particulier. L'utilisation du chemin **D:\home\site** permet de transférer les fichiers partagés d'un site vers un autre sans avoir à configurer un nouveau chemin d'accès absolu à chaque transfert.

<a id="TypesOfFileAccess"></a>
<h3>Types d'accès aux fichiers octroyés à une application Web</h3>

L'abonnement de chaque client comporte une structure de répertoires réservée sur un partage UNC de centre de données spécifique. Un client peut disposer de plusieurs sites web créés dans un centre de données spécifique. Ainsi, tous les répertoires appartenant à l'abonnement de ce client sont créés sur le même partage UNC. Le partage peut inclure des répertoires tels que ceux destinés au contenu, aux journaux d'erreurs et de diagnostic et aux précédentes versions du site web créées par le contrôle de code source. Les répertoires du site web d'un client sont accessibles en lecture et en écriture durant l'exécution par le code d'application d'un site web.

Sur les lecteurs locaux associés à la machine virtuelle qui exécute un site web, Sites Web Azure réserve une partie de l'espace sur le lecteur C:\ au stockage local temporaire propre au site web. Bien qu'un site web dispose d'un accès en lecture/écriture complet à son propre stockage local temporaire, ce stockage n'est pas destiné à être directement utilisé par le code d'application. Il a pour but de fournir un stockage de fichiers temporaire à IIS et aux infrastructures d'applications Web. Sites Web Azure limite également le stockage local temporaire accessible à chaque site web pour empêcher les sites individuels d'utiliser une part excessive du stockage de fichiers local.

Deux exemples illustrent la façon dont Sites Web Azure utilise le stockage local temporaire : le répertoire de fichiers temporaires ASP.NET et le répertoire de fichiers compressés IIS. Le système de compilation ASP.NET utilise le répertoire de fichiers temporaires " Temporary ASP.NET Files " comme emplacement de cache de compilation temporaire. IIS utilise le répertoire de fichiers compressés " IIS Temporary Compressed Files " pour stocker les réponses compressées. Ces deux types d'utilisation de fichiers (et d'autres) sont de nouveau mappés dans Sites Web Azure à un stockage local temporaire par site web. Ce nouveau mappage préserve les fonctionnalités.

Chaque site web dans Sites Web Azure est exécuté sous une identité de processus de travail à faibles privilèges unique et aléatoire appelée " identité du pool d'applications ", comme décrit à la page suivante : [http://www.iis.net/learn/manage/configuring-security/application-pool-identities](http://www.iis.net/learn/manage/configuring-security/application-pool-identities). Le code d'application utilise cette identité pour l'accès en lecture seule de base au lecteur du système d'exploitation (lecteur D:\). Le code d'application peut ainsi dresser la liste les structures de répertoires communes et lire les fichiers communs sur le lecteur du système d'exploitation. Bien que ce niveau d'accès puisse sembler étendu, les mêmes répertoires et fichiers sont accessibles lorsque vous approvisionnez un rôle de travail dans le service hébergé Azure et lisez le contenu du lecteur. 

<a name="multipleinstances"></a>
### Accès aux fichiers sur plusieurs instances

Le répertoire de base contient le contenu d'un site, et les applications Web peuvent y accéder en écriture. Si un site web est exécuté sur plusieurs instances, le répertoire de base est partagé entre toutes les instances pour que celles-ci voient toutes le même répertoire. Ainsi, par exemple, si un site web enregistre des fichiers téléchargés dans le répertoire de base, ces fichiers sont immédiatement accessibles à toutes les instances. 

<a id="NetworkAccess"></a>
<h2>Accès réseau</h2>
Le code d'application peut utiliser les protocoles TCP/IP et UDP pour établir des connexions réseau sortantes avec des points de terminaison accessibles par Internet qui exposent des services externes. Les applications peuvent utiliser ces mêmes protocoles pour se connecter aux services Azure, par exemple en établissant des connexions HTTPS à SQL Azure.

La capacité des applications à établir une connexion de bouclage locale et à écouter sur le socket de bouclage local correspondant est également limitée. Cette fonctionnalité a pour principal objectif d'activer les applications configurées pour écouter sur les sockets de bouclage locaux. Notez que l'application de chaque client voit une connexion de bouclage " privée " ; une application " A " ne peut pas écouter sur un socket de bouclage local établi par une application " B ".

Les canaux nommés sont également pris en charge en tant que mécanisme de communication entre processus (IPC) entre les différents processus qui exécutent collectivement un site web. Par exemple, le module FastCGI d'IIS utilise des canaux nommés pour coordonner les processus individuels qui exécutent les pages PHP.


<a id="Code"></a>
<h2>Exécution de code, processus et mémoire</h2>
Comme mentionné précédemment, les sites web sont exécutés dans des processus de travail à faibles privilèges qui utilisent une identité du pool d'applications aléatoire. Le code d'application a accès à l'espace mémoire associé au processus de travail ainsi qu'aux processus enfants qui peuvent être générés par les processus CGI ou par d'autres applications. Toutefois, les applications d'un site web n'ont pas accès à la mémoire ou aux données du site web d'un autre client, même si celles-ci se trouvent sur la même machine virtuelle.

Les applications peuvent exécuter des scripts ou des pages créés avec des infrastructures de développement d'applications Web prises en charge. Sites Web Azure ne configure aucun paramètre d'infrastructure de développement d'applications web sur des modes plus restreints. Par exemple, les sites ASP.NET exécutés sur Sites Web Azure sont exécutés avec une confiance totale par opposition à un mode de confiance plus limité. Les infrastructures d'applications, telles que ASP classique et ASP.NET, peuvent appeler des composants COM en cours de processus (mais pas des composants COM hors processus), comme ADO (ActiveX Data Objects), qui sont enregistrés par défaut sur le système d'exploitation Windows.

Les applications Web peuvent générer et exécuter un code arbitraire. Une application Web est autorisée à effectuer des opérations telles que la génération d'une interface de commande ou l'exécution d'un script PowerShell. Toutefois, bien que du code et des processus arbitraires puissent être générés à partir d'une application Web, les programmes et scripts exécutables restent limités aux privilèges accordés au pool d'applications parent. Par exemple, un site web peut générer un exécutable pour passer un appel HTTP sortant, mais ce même exécutable ne peut pas tenter de dissocier l'adresse IP d'une machine virtuelle de sa carte réseau. Les appels réseau sortants vers le code à faibles privilèges sont autorisés, mais toute tentative de reconfiguration des paramètres réseau sur une machine virtuelle nécessite des privilèges d'administration.


<a id="Diagnostics"></a>
<h2>Journaux et événements de diagnostic</h2>
Certaines applications web peuvent également tenter d'accéder aux informations de journalisation. Les types d'information de journalisation accessibles au code exécuté dans Sites Web Azure englobent les informations de diagnostic et de journalisation générées par un site web, et sont également facilement accessibles à un site web. 

Par exemple, les journaux HTTP W3C générés par un site web actif sont disponibles dans un répertoire de journaux du partage réseau créé pour le site web ou dans un stockage d'objets blob, si le client a configuré la journalisation W3C pour le stockage. Cette dernière option permet de collecter de gros volumes de journaux sans risquer de dépasser les limites de stockage de fichiers associées à un partage réseau.

De même, les informations de diagnostic en temps réel des applications .NET peuvent être consignées via l'infrastructure de suivi et de diagnostic .NET, avec possibilité de consigner les informations de suivi sur le partage réseau du site web ou à un emplacement de stockage d'objets blob.

Les éléments de journalisation et de suivi des diagnostics non accessibles aux applications Web sous Azure sont les événements ETW Windows et les journaux d'événements Windows communs (par exemple, les journaux d'événements système, d'applications et de sécurité). Dans la mesure où les informations de suivi ETW sont potentiellement visibles sur toute la machine (avec les ACL qui conviennent), l'accès en écriture aux événements ETW est bloqué. Les développeurs peuvent constater que les appels d'API aux événements ETW en lecture et écriture, ainsi qu'aux journaux des événements Windows communs semblent fonctionner, car Sites Web Azure " simule " les appels pour donner l'impression qu'ils aboutissent. En réalité, le code d'application du site web n'a pas accès à ces données d'événements.

<a id="RegistryAccess"></a>
<h2>Accès au registre</h2>
Les applications disposent d'un accès en lecture seule à la majeure partie du registre (mais pas à la totalité de celui-ci) de la machine virtuelle sur laquelle elles sont exécutées. En pratique, cela signifie que les clés de registre qui permettent un accès en lecture seule au groupe Utilisateurs local sont accessibles aux applications Web. La ruche HKEY\_CURRENT\_USER est une zone du registre actuellement non prise en charge pour l'accès en lecture ou en écriture.

L'accès en écriture au registre est bloqué, y compris l'accès aux éventuelles clés de registre par utilisateur. Dans un environnement cloud, une application ne doit jamais compter sur l'accès en écriture au registre, car les applications peuvent être (et sont) migrées entre différentes machines virtuelles. Le seul stockage toujours accessible en écriture sur lequel une application web peut compter est la structure des répertoires de contenu par site web stockée dans les partages UNC de Sites Web Azure. 
