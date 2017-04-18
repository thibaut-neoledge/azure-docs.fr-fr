---
title: "Alertes de sécurité par type dans Azure Security Center | Microsoft Docs"
description: "Cet article décrit les différents types d’alertes de sécurité disponibles dans Azure Security Center."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: b3e7b4bc-5ee0-4280-ad78-f49998675af1
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 1b0d278c102497eca978d8cd3fa29cd2527f186c
ms.lasthandoff: 04/06/2017


---
# <a name="security-alerts-by-type-in-azure-security-center"></a>Alertes de sécurité par type dans Azure Security Center
Cet article vous aide à comprendre les différents types d’alertes de sécurité disponibles dans Azure Security Center. Pour plus d’informations sur la gestion des alertes,voir [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md).

> [!NOTE]
> Pour configurer la détection avancée, effectuez une mise à niveau vers Azure Security Center Standard. Une version d’évaluation gratuite de 60 jours est disponible. Pour mettre à niveau, sélectionnez **Niveau tarifaire** sous [Stratégie de sécurité](security-center-policies.md). Pour en savoir plus, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/security-center/).
>
>

## <a name="what-type-of-alerts-are-available"></a>Quels types d’alertes sont disponibles ?
Azure Security Center fournit différentes alertes qui s’alignent sur les étapes de cyberchaîne de destruction. L’illustration suivante montre différentes alertes liées à certaines de ces étapes.

![Chaîne de destruction](./media/security-center-alerts-type/security-center-alerts-type-fig1.png)

**Cible et attaque**

* Attaques RDP/SSH entrantes
* Attaques d’applications et DDoS (partenaires WAF)
* Détection d’intrusion (partenaires pare-feu NG)

**Installation et exploitation**

* Signatures de programmes malveillants connus (partenaires AM)
* Tentatives de codes malveillants exploitant une faille de sécurité et de programmes malveillants en mémoire
* Exécution de processus suspects
* Manœuvres dissimulées pour éviter la découverte
* Mouvement latéral
* Reconnaissance interne
* Activité PowerShell suspecte

**Après une violation**  

* Communication vers une adresse IP malveillante connue (exfiltration des données ou commande et contrôle)
* Utilisation des ressources compromises pour lancer d’autres attaques (attaques en force brute RDP/SSH analysant le port sortant, et courrier indésirable)

Différents types d’attaques sont associées à chaque phase et elles ciblent différents sous-systèmes. Pour résoudre ces attaques pendant ces phases, Azure Security Center comporte trois catégories d’alertes :

* Analyse comportementale de la machine virtuelle (VMBA)
* Analyse du réseau
* Analyse des ressources

## <a name="virtual-machine-behavioral-analysis"></a>Analyse comportementale de la machine virtuelle
Azure Security Center peut utiliser l’analyse comportementale pour identifier les ressources compromises en se basant sur l’analyse des journaux d’événements. Par exemple, les événements de création de processus et les événements de connexion. En outre, il existe une corrélation avec les autres signaux pour rechercher les preuves d’une campagne généralisée.

> [!NOTE]
> Pour plus d’informations sur le fonctionnement des fonctionnalités de détection de Security Center, voir [Fonctionnalités de détection d’Azure Security Center](security-center-detection-capabilities.md).
>
>

### <a name="crash-analysis"></a>Analyse des incidents
L’analyse de la mémoire de vidage sur incident est une méthode utilisée pour détecter les programmes malveillants sophistiqués capables d’échapper aux solutions de sécurité traditionnelles. Différentes formes de programmes malveillants tentent de réduire le risque de détection par des produits antivirus en n’écrivant jamais sur le disque ou en chiffrant les composants logiciels écrits sur le disque. Ainsi, les programmes malveillants sont difficiles à détecter à l’aide des logiciels anti-programmes malveillants traditionnels. Toutefois, ce type de logiciel malveillant peut être détecté à l’aide de l’analyse de mémoire, car le programme malveillant laisse des traces en mémoire pour pouvoir fonctionner.

Lorsque le logiciel se bloque, un vidage sur incident capture une partie de la mémoire au moment de l’incident. L’incident peut être provoqué par un programme malveillant, une application générale ou des problèmes système. En analysant la mémoire dans le vidage sur incident, Azure Security Center peut détecter les techniques utilisées pour exploiter la vulnérabilité des logiciels, accéder aux données confidentielles et persister subrepticement au sein d’un ordinateur compromis. Ces opérations ont peu d’impact sur les performances des hôtes, car l’analyse est effectuée au cœur d’Azure Security Center.

Les champs suivants sont communs aux exemples d’alerte de vidage sur incident qui apparaissent plus loin dans cet article :

* DUMPFILE : nom du fichier de vidage sur incident.
* PROCESSNAME : nom du processus bloqué.
* PROCESSVERSION : version du processus bloqué.

### <a name="shellcode-discovered"></a>Shellcode détecté
Un shellcode est la charge utile exécutée après qu’un programme malveillant a exploité une vulnérabilité logicielle. Cette alerte indique que l’analyse de vidage sur incident a détecté du code exécutable présentant un comportement souvent associé à des charges utiles malveillantes. Bien que des logiciels non malveillants puissent présenter aussi ce comportement, il n’est pas typique des pratiques de développement logiciel normales.

Cet exemple d’alerte shellcode fournit le champ supplémentaire suivant :

* ADRESSE : emplacement du shellcode dans la mémoire.

Voici un exemple de ce type d’alerte :

![Alerte de shellcode](./media/security-center-alerts-type/security-center-alerts-type-fig2.png)

### <a name="module-hijacking-discovered"></a>Détournement de module détecté
Windows s’appuie sur des bibliothèques de liens dynamiques (DLL) pour exploiter les fonctionnalités système courantes. Un détournement de DLL se produit lorsqu’un programme malveillant modifie l’ordre de chargement des DLL pour charger des charges utiles malveillantes dans la mémoire, où du code arbitraire peut être exécuté. Cette alerte indique que l’analyse de vidage sur incident a détecté qu’un module portant un nom similaire a été chargé à partir de deux chemins d’accès différents. L’un des chemins d’accès chargés provient d’un emplacement binaire système Windows commun.

Les développeurs de logiciels légitimes modifient parfois l’ordre de chargement des DLL pour des raisons non malveillantes, telles que l’instrumentation ou l’extension du système d’exploitation Windows ou des applications Windows. Pour distinguer les modifications malveillantes et les modifications potentiellement bénignes apportées à l’ordre de chargement des DLL, Azure Security Center vérifie si un module chargé est conforme à un profil suspect. Le résultat de cette vérification est indiqué par le champ « SIGNATURE » de l’alerte et est répercuté dans le niveau de gravité de l’alerte, la description de l’alerte et les étapes de correction de l’alerte. Pour rechercher si le module est légitime ou malveillant, analysez la copie sur disque du module de détournement. Par exemple, vous pouvez vérifier la signature numérique du fichier, ou exécuter une analyse antivirus.

En plus des champs communs décrits dans la section précédente « Shellcode détecté », cette alerte fournit les champs suivants :

* SIGNATURE : indique si le module de détournement est conforme à un profil de comportement suspect.
* HIJACKEDMODULE : nom du module système Windows détourné.
* HIJACKEDMODULEPATH : chemin d’accès du module système Windows détourné.
* HIJACKINGMODULEPATH : chemin d’accès du module de détournement.

Voici un exemple de ce type d’alerte :

![Alerte de détournement de module](./media/security-center-alerts-type/security-center-alerts-type-fig3.png)

### <a name="masquerading-windows-module-detected"></a>Usurpation d’identité de module Windows détectée
Les programmes malveillants peuvent utiliser des noms courants de fichiers binaires système (par exemple, SVCHOST.EXE) ou de modules (par exemple, NTDLL.DLL) Windows pour *se fondre* et cacher la nature malveillante des logiciels aux administrateurs système. Cette alerte indique que l’analyse de vidage sur incident a détecté que le fichier de vidage sur incident contient des modules qui utilisent des noms de modules système Windows, mais ne remplissent pas les autres critères typiques des modules Windows. L’analyse de la copie sur disque du module d’usurpation d’identité peut fournir des informations supplémentaires quant à la nature légitime ou malveillante de ce dernier. L’analyse peut inclure les opérations suivantes :

* Confirmation que le fichier en question est fourni dans le cadre d’un package logiciel légitime.
* Vérification de la signature numérique du fichier.
* Exécution d’une analyse antivirus sur le fichier.

En plus des champs communs décrits dans la section précédente « Shellcode détecté », cette alerte fournit les champs suivants :

* DETAILS : indique si les métadonnées du module sont valides et si le module a été chargé depuis un chemin d’accès système.
* NOM : nom du module Windows d’usurpation d’identité.
* PATH : chemin d’accès du module Windows d’usurpation d’identité.

Cette alerte extrait et affiche également certains champs de l’en-tête PE du module, tels que « CHECKSUM » et « TIMESTAMP ». Ces champs sont affichés uniquement s’ils sont présents dans le module. Pour plus d’informations sur ces champs, consultez la [spécification Microsoft PE et COFF](https://msdn.microsoft.com/windows/hardware/gg463119.aspx) .

Voici un exemple de ce type d’alerte :

![Alerte d’usurpation d’identité Windows](./media/security-center-alerts-type/security-center-alerts-type-fig4.png)

### <a name="modified-system-binary-discovered"></a>Fichier binaire système modifié détecté
Les programmes malveillants peuvent modifier des fichiers binaires système de base pour accéder de façon dissimulée aux données ou persister subrepticement sur un système compromis. Cette alerte indique que l’analyse de vidage sur incident a détecté que des fichiers binaires de base du système d’exploitation Windows ont été modifiés dans la mémoire ou sur le disque.

Les développeurs de logiciels légitimes modifient parfois des modules système dans la mémoire pour des raisons non malveillantes, par exemple avec Detours ou pour la compatibilité des applications. Pour distinguer les modules malveillants et les modules potentiellement légitimes, Azure Security Center vérifie si le module modifié est conforme à un profil suspect. Le résultat de cette vérification est indiqué par le niveau de gravité de l’alerte, la description de l’alerte et les étapes de correction de l’alerte.

En plus des champs communs décrits dans la section précédente « Shellcode détecté », cette alerte fournit les champs suivants :

* MODULENAME : nom du fichier binaire système modifié.
* MODULEVERSION : version du fichier binaire système modifié.

Voici un exemple de ce type d’alerte :

![Alerte de fichier binaire système](./media/security-center-alerts-type/security-center-alerts-type-fig5.png)

### <a name="suspicious-process-executed"></a>Processus suspect exécuté
Azure Security Center identifie le processus suspect en cours d’exécution dans la machine virtuelle cible et déclenche une alerte. La détection ne recherche pas de nom spécifique, mais le paramètre du fichier exécutable. Par conséquent, même si l’attaquant renomme le fichier exécutable, Security Center peut toujours détecter le processus suspect.

Voici un exemple de ce type d’alerte :

![Alerte de processus suspect](./media/security-center-alerts-type/security-center-alerts-type-fig6-new.png)

### <a name="multiple-domain-accounts-queried"></a>Plusieurs comptes de domaine interrogés
Azure Security Center peut détecter plusieurs tentatives d’interrogation de comptes de domaine Active Directory, généralement effectuées par des personnes malveillantes au cours de la reconnaissance du réseau. Les personnes malveillantes peuvent exploiter cette technique pour interroger le domaine et identifier les utilisateurs, les comptes d’administration de domaine, les ordinateurs qui sont des contrôleurs de domaine et la relation d’approbation potentielle d’un domaine avec d’autres domaines.

Voici un exemple de ce type d’alerte :

![Alerte de compte de domaines multiples](./media/security-center-alerts-type/security-center-alerts-type-fig7-new.png)

### <a name="local-administrators-group-members-were-enumerated"></a>Les membres du groupe Administrateurs locaux ont été énumérés

Security Center va déclencher une alerte lorsque l’événement de sécurité 4798, dans Windows Server 2016 et Windows 10, est déclenché. Cela se produit lorsque le groupe Administrateurs locaux est énuméré, ce qui est généralement effectué par des pirates au cours de la reconnaissance du réseau. Les pirates peuvent exploiter cette technique pour interroger l’identité des utilisateurs avec des privilèges d’administrateur.

Voici un exemple de ce type d’alerte :

![Administrateur local](./media/security-center-alerts-type/security-center-alerts-type-fig14-new.png)

### <a name="anomalous-mix-of-upper-and-lower-case-characters"></a>Combinaison anormale de majuscules et minuscules

Security Center déclenche une alerte lorsqu’il détecte l’utilisation d’une combinaison de majuscules et minuscules à la ligne de commande. Certains pirates peuvent utiliser cette technique pour se dérober aux règles de hachage ou de casse.

Voici un exemple de ce type d’alerte :

![Combinaison anormale](./media/security-center-alerts-type/security-center-alerts-type-fig15-new.png)

### <a name="suspected-kerberos-golden-ticket-attack"></a>Suspicion d’attaque de golden ticket Kerberos

Une clé [krbtgt](https://technet.microsoft.com/library/dn745899.aspx) compromise peut être utilisée par un pirate pour créer des « golden tickets » Kerberos, lui permettant d’emprunter l’identité de l’utilisateur de leur choix. Security Center déclenche une alerte lorsqu’il détecte ce type d’activité.

> [!NOTE] 
> Pour plus d’informations sur les golden ticket Kerberos, lisez le [guide de prévention contre le vol d’informations d’identification Windows 10](http://download.microsoft.com/download/C/1/4/C14579CA-E564-4743-8B51-61C0882662AC/Windows%2010%20credential%20theft%20mitigation%20guide.docx).

Voici un exemple de ce type d’alerte :

![Golden ticket](./media/security-center-alerts-type/security-center-alerts-type-fig16-new.png)

### <a name="suspicious-account-created"></a>Création d’un compte suspect

Security Center déclenche une alerte lorsqu’un compte ressemblant fortement à un compte bénéficiant de privilèges d’administrateur intégré existant est créé. Cette technique peut être utilisée par des pirates pour créer un compte non autorisé sans être détecté par une vérification humaine.
 
Voici un exemple de ce type d’alerte :

![Compte suspect](./media/security-center-alerts-type/security-center-alerts-type-fig17-new.png)

### <a name="suspicious-firewall-rule-created"></a>Création d’une règle de pare-feu suspecte

Les pirates peuvent tenter de contourner la sécurité de l’ordinateur hôte en créant des règles de pare-feu personnalisées pour permettre aux applications malveillantes de communiquer avec la commande et le contrôle, ou pour lancer des attaques sur le réseau par le biais de l’hôte compromis. Security Center déclenche une alerte lorsqu’il détecte qu’une règle de pare-feu a été créée à partir d’un fichier exécutable à un emplacement suspect.
 
Voici un exemple de ce type d’alerte :

![Règle de pare-feu](./media/security-center-alerts-type/security-center-alerts-type-fig18-new.png)

### <a name="suspicious-combination-of-hta-and-powershell"></a>Combinaison suspecte de HTA et PowerShell

Security Center déclenche une alerte lorsqu’il détecte qu’un hôte d’application HTML (HTA) lance des commandes PowerShell. Il s’agit d’une technique utilisée par les pirates pour lancer des scripts PowerShell malveillants.
 
Voici un exemple de ce type d’alerte :

![HTA et PS](./media/security-center-alerts-type/security-center-alerts-type-fig19-new.png)


## <a name="network-analysis"></a>Analyse du réseau
La détection des menaces sur le réseau assurée par Azure Security Center fonctionne en collectant automatiquement les informations de sécurité à partir du trafic IPFIX (Internet Protocol Flow Information Export) Azure. Elle analyse ces informations, souvent issues de plusieurs sources, pour identifier les menaces.

### <a name="suspicious-outgoing-traffic-detected"></a>Trafic sortant suspect détecté
Les périphériques réseau peuvent être détectés et profilés de la même façon que les autres types de systèmes. Les pirates commencent généralement par l’analyse des ports ou le balayage des ports. L’exemple ci-dessous présente un trafic Secure Shell (SSH) suspect d’une machine virtuelle pouvant être en train de procéder à une attaque de force brute SSH ou à une attaque par balayage des ports contre une ressource externe.

![Alerte de trafic sortant suspect](./media/security-center-alerts-type/security-center-alerts-type-fig8.png)

Cette alerte fournit des informations permettant d’identifier la ressource utilisée pour lancer cette attaque. Cette alerte fournit également des informations permettant d’identifier l’ordinateur compromis, le temps de détection, ainsi que le protocole et le port utilisés. Ce panneau vous donne également une liste des étapes de résolution pouvant être utilisées pour résoudre ce problème.

### <a name="network-communication-with-a-malicious-machine"></a>Communication réseau avec un ordinateur malveillant
En exploitant les flux des informations sur les menaces de Microsoft, Azure Security Center peut détecter les ordinateurs compromis qui communiquent avec des adresses IP malveillantes. Dans de nombreux cas, l’adresse malveillante est un centre de commande et de contrôle. Dans ce cas, Security Center a détecté que la communication a été effectuée à l’aide du programme malveillant Pony Loader (également appelé [Fareit](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=PWS:Win32/Fareit.AF)).

![Alerte de communication réseau](./media/security-center-alerts-type/security-center-alerts-type-fig9.png)

Cette alerte fournit des informations qui vous permettent d’identifier la ressource qui a été utilisée pour lancer ce type d’attaque, la ressource attaquée, l’adresse IP de la victime, l’adresse IP de l’attaquant et l’heure de détection.

> [!NOTE]
> Les adresses IP dynamiques ont été supprimés de cette capture d’écran à des fins de confidentialité.
>
>

### <a name="possible-outgoing-denial-of-service-attack-detected"></a>Attaque potentielle par déni de service sortant détectée
Un trafic réseau anormal provenant d’une machine virtuelle peut amener Azure Security Center à déclencher un type d’attaque potentielle par déni de service.

Voici un exemple de ce type d’alerte :

![Déni de service sortant](./media/security-center-alerts-type/security-center-alerts-type-fig10-new.png)

## <a name="resource-analysis"></a>Analyse des ressources
Dans Security Center, l’analyse des ressources se concentre sur les services PaaS, comme l’intégration avec la fonctionnalité de [détection des menaces de la base de données SQL Azure](../sql-database/sql-database-threat-detection.md). Selon les résultats de l’analyse de ces zones, Azure Security Center déclenche une alerte associée aux ressources.

### <a name="potential-sql-injection"></a>Injection potentielle de code SQL
Une attaque par injection de code SQL est une attaque dans laquelle un code malveillant est inséré dans les chaînes transmises ultérieurement à une instance de SQL Server pour analyse et exécution. Toute procédure qui construit des instructions SQL doit être analysée pour rechercher les vulnérabilités d’injection, car SQL Server exécutera toutes les requêtes syntaxiquement valides qu’il reçoit. La fonctionnalité de détection des menaces SQL utilise l’apprentissage machine, l’analyse comportementale et la détection d’anomalie pour déterminer les événements suspects pouvant survenir dans les bases de données SQL Azure. Par exemple :

* Tentative d’accès à la base de données par un ancien employé
* Attaques par injection de code SQL
* Accès inhabituel à une base de données de production par un utilisateur à son domicile

![Alerte d’injection potentielle de code SQL](./media/security-center-alerts-type/security-center-alerts-type-fig11.png)

Les informations contenues dans cette alerte peuvent être utilisées pour identifier la ressource attaquée, le temps de détection et l’état de l’attaque. Elles fournissent également un lien vers d’autres étapes d’enquête.

### <a name="vulnerability-to-sql-injection"></a>Vulnérabilité par injection de code SQL
Cette alerte est déclenchée lorsqu’une erreur d’application est détectée sur une base de données. Cette alerte peut éventuellement indiquer une vulnérabilité aux attaques par injection de code SQL.

![Alerte d’injection potentielle de code SQL](./media/security-center-alerts-type/security-center-alerts-type-fig12-new.png)

### <a name="unusual-access-from-unfamiliar-location"></a>Accès inhabituel depuis un emplacement inconnu
Cette alerte est déclenchée lorsqu’un événement d’accès à partir d’une adresse IP inconnue a été détecté sur le serveur, qui n’était pas visible lors de la dernière période.

![Alerte d’accès inhabituel](./media/security-center-alerts-type/security-center-alerts-type-fig13-new.png)

## <a name="see-also"></a>Voir aussi
Cet article vous a présenté les différents types d’alertes de sécurité dans Azure Security Center. Pour plus d’informations sur Security Center, consultez :

* [Gestion des incidents de sécurité dans Azure Security Center](security-center-incident.md)
* [Fonctionnalités de détection d’Azure Security Center](security-center-detection-capabilities.md)
* [Guide des opérations et de planification d’Azure Security Center](security-center-planning-and-operations-guide.md)
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : recherchez des billets de blog sur la sécurité et la conformité Azure.

