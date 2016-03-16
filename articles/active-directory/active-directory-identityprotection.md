<properties
	pageTitle="Azure Active Directory Identity Protection | Microsoft Azure"
	description="Découvrez comment Azure AD Identity Protection vous permet de limiter la capacité d’un cybercriminel à exploiter une identité ou un appareil compromis et de sécuriser une identité ou un appareil déjà identifié comme potentiellement ou effectivement compromis."
	services="active-directory"
	keywords="azure active directory identity protection, cloud app discovery, gestion d’applications, sécurité, risque, niveau de risque, vulnérabilité, stratégie de sécurité"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/02/2016"
	ms.author="markvi"/>

#Azure Active Directory Identity Protection 

Azure Active Directory Identity Protection est un service de sécurité offrant une vue consolidée des événements à risque et des vulnérabilités potentielles qui affectent les identités de votre organisation. Microsoft sécurise les identités dans le cloud depuis plus de dix ans. Avec Azure AD Identity Protection, Microsoft propose met ces mêmes systèmes de protection à disposition des clients d’entreprise. Identity Protection tire parti des fonctionnalités existantes de détection des anomalies d’Azure AD (disponibles via les rapports d’activités anormales d’Azure AD) et introduit de nouveaux types d’événements à risque capables de détecter les anomalies en temps réel.

> [AZURE.NOTE] La version préliminaire d’Identity Protection est pour le moment disponible uniquement pour les clients Azure AD approvisionnés aux États-Unis.
 

La grande majorité des violations de sécurité ont lieu lorsque des cybercriminels parviennent à accéder à un environnement en volant l’identité d’un utilisateur. Les cybercriminels arrivent de plus en plus à exploiter les failles de fournisseurs tiers et utilisent des attaques par hameçonnage (ou « phishing ») sophistiquées toujours plus efficaces. Une fois qu’un cybercriminel accède à un compte d’utilisateur, même si les privilèges de celui-ci sont faibles, il est relativement simple pour lui d’accéder à des ressources d’entreprise importantes de manière latérale. Il est donc essentiel de protéger toutes les identités et, lorsqu’une identité est compromise, d’empêcher que celle-ci soit utilisée à des fins malveillantes.

Détecter les identités compromises n’est pas chose aisée. Fort heureusement, le service Identity Protection peut vous y aider : s’appuyant sur des algorithmes d’apprentissage automatique adaptatif et des règles heuristiques, il assure la détection d’anomalies et d’événements à risque susceptibles d’indiquer qu’une identité a été compromise.
 
À l’aide de ces données, Identity Protection génère des rapports et des alertes qui vous permettent d’analyser ces événements à risque et de prendre les mesures de correction ou d’atténuation qui s’imposent.
 
Mais Azure Active Directory Identity Protection est plus qu’un outil de surveillance et de création de rapports. En fonction des événements à risque, Identity Protection calcule le niveau de risque des utilisateurs pour chaque utilisateur, ce qui vous permet de configurer des stratégies basées sur les risques pour protéger automatiquement les identités de votre organisation. Ces stratégies basées sur les risques, en plus des autres contrôles d’accès conditionnel fournis par Azure Active Directory et EMS, peuvent automatiquement bloquer l’accès ou appliquer des mesures de correction adaptatives qui incluent la réinitialisation de mot de passe et la mise en œuvre l’authentification multifacteur.

####Exploration du service Identity Protection 

**Détection des événements et des comptes à risque :**

- Détection de 6 types d’événements à risque à l’aide de l’apprentissage automatique et des règles heuristiques 

- Calcul du niveau de risque des utilisateurs

- Recommandations personnalisées visant à améliorer la posture de sécurité globale en mettant en évidence les vulnérabilités

<br>

**Examen des événements à risque :**

- Envoi de notifications pour les événements à risque

- Examen des événements à risque à l’aide d’informations contextuelles et pertinentes

- Workflows de base pour le suivi des investigations

- Accès rapide à des mesures de correction telles que la réinitialisation de mot de passe

<br>

**Stratégies d’accès conditionnel en fonction des risques :**

- Stratégie pour atténuer les connexions à risque en bloquant les connexions ou en imposant des demandes d’authentification multifacteur.

- Stratégie pour bloquer ou sécuriser les comptes d’utilisateurs à risque

- Stratégie pour exiger que les utilisateurs s’inscrivent à l’authentification multifacteur


## Détection et risque

### Événements à risque

Les événements à risque sont des événements qui ont été signalés comme suspects par Identity Protection et indiquent qu’une identité a peut-être été compromise. Pour obtenir une liste complète des événements à risque, consultez [Types d’événements à risque](#types-of-risk-events). Certains de ces événements à risque sont disponibles via les rapports d’activités anormales d’Azure AD dans le portail de gestion Azure. Le tableau ci-dessous répertorie les différents types d’événements à risque et le rapport d’**activités anormales d’Azure AD** correspondant. Microsoft poursuit ses investissements dans ce domaine et prévoit d’améliorer la précision de détection des événements à risque existants et d’ajouter de nouveaux types d’événements à risque de façon continue.



| Type d’événement à risque signalé par Identity Protection | Rapport d’activités anormales d’Azure AD correspondant |
| :-- | :-- |
| Informations d’identification divulguées | Utilisateurs avec des informations d’identification volées |
| Voyage impossible vers des emplacements inhabituels |	Activité de connexion anormale |
| Connexions depuis des appareils infectés | Connexions à partir d’appareils potentiellement infectés |
| Connexions depuis des adresses IP anonymes | Connexions à partir de sources inconnues |
| Connexions depuis des adresses IP avec des activités suspectes |	Connexions depuis des adresses IP avec des activités suspectes |
| Connexions depuis des emplacements non connus | - | 
| Événements de verrouillage (non disponible dans la version préliminaire publique) | - |

Les rapports d’activités anormales d’Azure AD suivants ne sont pas inclus en tant qu’événements à risque dans Azure AD Identity Protection et ne sont donc pas disponibles via Identity Protection. Ces rapports sont toujours disponibles dans le portail de gestion Azure, mais ils seront rendus obsolètes ultérieurement du fait de leur remplacement par des événements à risque dans Identity Protection.

- Connexions après plusieurs échecs
- Connexions depuis plusieurs zones géographiques

### Niveau de risque

Le niveau de risque d’un événement à risque est une indication (Élevé, Moyen ou Faible) de la gravité de l’événement à risque. Le niveau de risque permet aux utilisateurs du service Identity Protection de hiérarchiser les mesures qu’ils doivent prendre afin de réduire le risque pour leur organisation. Le niveau de gravité de l’événement à risque signalé représente la probabilité qu’une identité soit compromise, avec la quantité de bruit que cela implique généralement.

- **Élevé** : probabilité élevée et gravité élevée de l’événement à risque. Ces événements donnent une indication forte que l’identité de l’utilisateur a été compromise et les comptes d’utilisateurs concernés doivent immédiatement faire l’objet de mesures de correction.

- **Moyen** : sévérité élevée, mais probabilité moindre de l’événement à risque, ou inversement. Ces événements présentent des risques potentiels et les comptes d’utilisateurs concernés doivent faire l’objet de mesures de correction.

- **Faible** : probabilité faible et gravité limitée de l’événement à risque. Cet événement peut ne pas nécessiter une action immédiate, mais l’association à d’autres événements à risque peut donner une indication forte que l’identité a été compromise.


![Niveau de risque](./media/active-directory-identityprotection/01.png "Niveau de risque")

 

Les événements de risques sont soit identifiés en **temps réel**, soit en post-traitement après la survenue de l’événement à risque (hors connexion). À l’heure actuelle, la plupart des événements à risque sont calculés hors connexion et s’affichent dans Identity Protection dans un délai de 2 à 4 heures. Bien qu’évalués au moment où ils surviennent, les événements à risque en temps réel s’affichent dans la console d’Identity Protection dans un délai de 5 à 10 minutes.

Plusieurs clients hérités ne prennent pas en charge la détection et la prévention des événements en temps réel pour le moment. Par conséquent, les connexions depuis ces clients ne peuvent pas être détectées ou bloquées en temps réel.

### Types d’événements à risque

Cette section présente les différents types d’événements à risque disponibles.

#### Informations d’identification divulguées

Il arrive que les chercheurs en sécurité de Microsoft découvrent des informations d’identification divulguées dans le web invisible. Ces informations d’identification sont généralement publiées en texte brut. Elles sont comparées aux informations d’identification d’Azure AD, et s’il existe une correspondance, elles sont signalées comme étant des « informations d’identification divulguées » dans Identity Protection.

Les événements à risque liés à des informations d’identification divulguées sont définis sur un niveau de gravité « Élevé », car ils indiquent clairement que le nom d’utilisateur et le mot de passe sont à la disposition de personnes malveillantes.

#### Voyage impossible vers des emplacements inhabituels

Ce type d’événement à risque identifie deux connexions depuis des emplacements géographiquement distants, dont l’un au moins un est inhabituel pour l’utilisateur compte tenu de son comportement passé. De plus, le délai écoulé entre les deux connexions est inférieur au temps nécessaire pour se déplacer du premier emplacement au second, ce qui indique qu’un autre utilisateur utilise les mêmes informations d’identification.

Il s’agit d’un algorithme d’apprentissage automatique qui ignore les « *faux positifs* » évidents contribuant à la condition de voyage impossible, tels que les VPN et les emplacements régulièrement utilisés par d’autres membres de l’organisation. Le système présente une période d’apprentissage initiale de 14 jours lui servant à assimiler le comportement de connexion des nouveaux utilisateurs.

En règle générale, les événements de ce type donnent une bonne indication qu’un pirate est parvenu à se connecter avec le compte correspondant. Cependant, des faux positifs peuvent se produire lorsqu’un utilisateur en déplacement utilise un nouvel appareil ou un VPN qu’aucun autre membre de l’organisation n’utilise. Des faux positifs peuvent également survenir si des applications transmettent incorrectement des adresses IP de serveurs en tant qu’adresses IP de clients, ce qui peut donner l’impression que les connexions se font depuis le centre de données où le serveur principal de cette application est hébergé (il s’agit souvent d’un centre de données Microsoft, ce qui peut donner l’impression que les connexions se font depuis des adresses IP appartenant à Microsoft). En raison de ces faux positifs, le niveau de risque de cet événement est défini sur « **Moyen** ».

####Connexions depuis des appareils infectés

Ce type d’événement à risque identifie les connexions depuis des appareils infectés par des logiciels malveillants, qui sont connus pour communiquer activement avec un serveur robot, grâce à la mise en corrélation des adresses IP des appareils des utilisateurs avec des adresses ayant été en contact avec un serveur robot.

Cet événement à risque identifie les adresses IP, pas les appareils des utilisateurs. Si plusieurs appareils utilisent une même adresse IP, mais que seuls certains sont contrôlés par un réseau de robots, les connexions depuis les autres appareils peuvent déclencher inutilement cet événement. C’est pourquoi le niveau de risque de ce type d’événement est défini sur « **Faible** ».

Nous vous recommandons de contacter l’utilisateur et d’analyser tous ses appareils. Il est également possible que les appareils personnels d’un utilisateur soient infectés ou, comme indiqué précédemment, qu’une tierce personne utilisait un appareil infecté depuis la même adresse IP. Les appareils infectés le sont souvent par des logiciels malveillants qui n’ont pas encore été identifiés par les logiciels antivirus et dont la présence peut être le signe de mauvaises habitudes de la part de l’utilisateur.

Pour plus d'informations sur le traitement des infections de logiciels malveillants, consultez le [Centre de protection contre les programmes malveillants](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


#### Connexions depuis des adresses IP anonymes

Ce type d’événement à risque signale les utilisateurs qui se sont connectés depuis une adresse IP ayant été identifiée comme l’adresse IP d’un proxy anonyme. Ces proxys sont utilisés par des individus souhaitant masquer l’adresse IP de leur appareil et peuvent être utilisés dans un but malveillant.

Nous vous recommandons de contacter immédiatement l’utilisateur pour vérifier s’il utilisait une adresse IP anonyme. Le niveau de risque de ce type d’événement risque est défini sur « **Moyen** », car une adresse IP anonyme n’est pas en soi une indication forte qu’un compte a été compromis.

#### Connexions depuis des adresses IP avec des activités suspectes

Ce type d’événement à risque identifie les adresses IP depuis lesquelles un grand nombre de tentatives de connexion ayant échoué ont été constatées, pour plusieurs comptes d’utilisateurs et sur une courte période. Cela correspond aux modèles de trafic des adresses IP utilisées par les cybercriminels et donne une indication forte que les comptes sont déjà compromis ou sont sur le point de l’être. Il s’agit d’un algorithme d’apprentissage automatique qui ignore les « *faux positifs* » évidents, tels que les adresses IP régulièrement utilisées par d’autres membres de l’organisation. Le système présente une période d’apprentissage initiale de 14 jours lui servant à assimiler le comportement de connexion des nouveaux utilisateurs et clients.

Nous vous conseillons de contacter l’utilisateur pour vérifier s’il s’est réellement connecté depuis une adresse IP qui a été signalée comme suspecte. Le niveau de risque pour ce type d’événement est défini sur « **Moyen** », car il arrive que plusieurs appareils utilisent la même adresse IP, mais que seuls certains soient responsables des activités suspectes.


#### Connexions depuis des emplacements non connus

Ce type d’événement à risque est un mécanisme d’évaluation de connexion en temps réel qui prend en compte les emplacements de connexion passés (IP, latitude/longitude et NSA) pour déterminer les emplacements non connus/nouveaux. Le système stocke les informations sur les emplacements précédents d’un utilisateur et considère ces emplacements comme « connus ». L’événement à risque est déclenché lorsque la connexion a lieu depuis un emplacement qui ne figure pas dans la liste des emplacements connus. Le système présente une période d’apprentissage initiale de 14 jours, durant laquelle il ne signale pas les nouveaux emplacements en tant qu’emplacements non connus. Le système ignore également les connexions depuis les appareils connus et les emplacements géographiquement proches d’un emplacement connu. <br> Les emplacements non connus peuvent donner une indication forte qu’un pirate tente d’utiliser une identité volée. Des faux positifs peuvent se produire lorsqu’un utilisateur est en déplacement, essaie un nouvel appareil ou utilise un nouveau VPN. C’est pourquoi le niveau de risque de ce type d’événement est défini sur « **Moyen** ».

### Vulnérabilités

Les vulnérabilités sont des points faibles exploitables par un cybercriminel au sein de votre environnement. Nous vous recommandons de les résoudre afin d’améliorer la posture de sécurité de votre organisation et d’empêcher des personnes malveillantes d’en tirer parti.

#### Inscription à l’authentification multifacteur non configurée 

Cette vulnérabilité vous permet de contrôler le déploiement d’Azure Multi-Factor Authentication, service d’authentification multifacteur d’Azure, dans votre organisation.

L’authentification multifacteur d’Azure ajoute une deuxième couche de sécurité à l’authentification utilisateur. Elle contribue à sécuriser l’accès aux données et aux applications tout en répondant à la demande des utilisateurs souhaitant un processus d’authentification simple. Il fournit une authentification forte via diverses options de vérification simples : appel téléphonique, message texte, notification par application mobile ou code de vérification et jetons OATH tiers.

Nous vous recommandons d’exiger l’authentification multifacteur pour les connexions des utilisateurs. L’authentification multifacteur joue un rôle clé dans les stratégies d’accès conditionnel en fonction des risques disponibles via Identity Protection.

Pour plus d’informations, consultez [Qu’est-ce qu’Azure Multi-Factor Authentication ?](../multi-factor-authentication/multi-factor-authentication.md)


#### Applications cloud non gérées

Cette vulnérabilité vous permet d’identifier les applications cloud non gérées au sein de votre organisation.
 
Dans les entreprises modernes, les services informatiques n’ont souvent pas connaissance de toutes les applications cloud utilisées par les membres de l’organisation pour effectuer leur travail. Il est facile de comprendre pourquoi les administrateurs s’inquiètent d’un accès non autorisé aux données d'entreprise, de possibles fuites de données et autres risques de sécurité.

Nous vous recommandons de déployer Cloud App Discovery dans votre organisation pour détecter les applications cloud non gérées et de gérer ces applications à l’aide d’Azure Active Directory.

Pour plus d’informations, consultez l’article [Détection des applications cloud non gérées avec Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).



####Alertes de sécurité du service Privileged Identity Management

Cette vulnérabilité vous aide à détecter et à résoudre les alertes relatives aux identités privilégiées dans votre organisation.

Pour permettre aux utilisateurs d’effectuer des opérations privilégiées, les organisations doivent leur accorder un accès privilégié temporaire ou permanent à des ressources Azure AD, Azure ou Office 365 ou à d’autres applications SaaS. Chacun de ces utilisateurs privilégiés augmente la surface d’attaque de votre organisation. Cette vulnérabilité vous permet d’identifier les utilisateurs disposant d’un accès privilégié inutile et de prendre les mesures qui s’imposent pour réduire ou éliminer le risque associé.

Nous vous recommandons d’utiliser le service Azure AD Privileged Identity Management dans votre organisation pour gérer, contrôler et surveiller les identités privilégiées et leur accès aux ressources dans Azure AD et dans d’autres services en ligne Microsoft tels qu’Office 365 ou Microsoft Intune.

Pour plus d’informations, consultez l’article [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md).

## Investigation
Votre parcours dans Identity Protection commence généralement par le tableau de bord d’Identity Protection.

<br><br> ![Correction](./media/active-directory-identityprotection/29.png "Correction") <br>

Le tableau de bord vous donne accès à :
 
- des rapports comme **Utilisateurs associés à un indicateur de risque**, **Événements à risque** et **Vulnérabilités** ;
- des paramètres vous permettant notamment de configurer vos **stratégies de sécurité**, vos **notifications** et l’**inscription à l’authentification multifacteur**.
 

Il s’agit généralement de votre point de départ pour l’investigation, processus consistant à vérifier les activités, les journaux et les autres informations pertinentes concernant un événement à risque, afin de déterminer s’il est nécessaire d’appliquer des mesures de correction ou d’atténuation, de comprendre comment l’identité a été compromise et d’identifier la manière dont l’identité compromise a été exploitée.


### Notifications

Azure AD Identity Protection envoie deux types d’e-mails de notification automatisés pour vous aider à gérer le risque des utilisateurs et les événements à risque :

- E-mail d’alerte en cas d’utilisateur compromis

- E-mail de synthèse hebdomadaire

#### E-mail d’alerte en cas d’utilisateur compromis

Un e-mail d’alerte en cas d’utilisateur compromis est généré lorsqu’Azure AD Identity Protection identifie un compte compromis. Cet e-mail inclut un lien vers le rapport Utilisateurs associés à un indicateur de risque dans la console d’Identity Protection. Nous vous recommandons d’examiner immédiatement les notifications d’utilisateurs compromis.


#### E-mail de synthèse hebdomadaire

L’e-mail de synthèse hebdomadaire contient un récapitulatif des nouveaux événements à risque.<br> Il inclut :
- Les utilisateurs à risque
- Les événements à risque signalés
- Les vulnérabilités détectées
- Des liens vers les rapports connexes dans Identity Protection


<br> ![Correction](./media/active-directory-identityprotection/400.png "Correction") <br>

Par défaut, l’alerte en cas d’utilisateur compromis est envoyée à tous les administrateurs d’Azure Active Directory. Pour personnaliser les destinataires, vous pouvez :

- Suivre le lien Gérer les destinataires en bas de l’alerte

- Cliquer sur Notifications dans les paramètres d’Identity Protection
 
<br> ![Risque des utilisateurs](./media/active-directory-identityprotection/62.png "Risque des utilisateurs") <br>
 



## Qu’est-ce que le niveau de risque d’un utilisateur ?

Le niveau de risque d’un utilisateur est une indication (Élevé, Moyen ou Faible) de la probabilité que l’identité de l’utilisateur ait été compromise. Il est calculé en fonction des événements à risque associés à l’identité de l’utilisateur.

L’état d’un événement à risque est soit **Actif**, soit **Fermé**. Seuls les événements dont l’état est défini sur **Actif** entrent dans le calcul du risque d’un utilisateur.

Le niveau de risque d’un utilisateur est calculé à l’aide des données suivantes :

- Événements à risque actifs ayant un impact sur l’utilisateur
- Niveau de risque de ces événements 
- Application ou non de mesures de correction 

<br> ![Risque des utilisateurs](./media/active-directory-identityprotection/86.png "Risque des utilisateurs") <br>



Vous pouvez utiliser le niveau de risque des utilisateurs pour créer des stratégies d’accès conditionnel empêchant les utilisateurs à risque de se connecter ou les forçant à modifier leur mot de passe de façon sécurisée.


## Fermeture manuelle des événements à risque

Dans la plupart des cas, vous pouvez prendre des mesures de correction telles qu’une réinitialisation de mot de passe sécurisée pour fermer automatiquement les événements à risque. Toutefois, il se peut que cela ne soit pas toujours possible. <br> Comme les événements à risque dont l’état est défini sur **Actif** entrent dans le calcul du risque des utilisateurs, vous pouvez avoir besoin de réduire manuellement un niveau de risque en fermant manuellement les événements à risque. <br> Au cours de l’investigation, vous pouvez choisir d’effectuer n’importe laquelle des actions suivantes pour modifier l’état d’un événement à risque :

<br> ![Actions](./media/active-directory-identityprotection/34.png "Actions") <br>

- **Résoudre** : si après avoir examiné un événement à risque, vous avez pris une mesure de correction appropriée en dehors d’Identity Protection et pensez que l’événement à risque doit être considéré comme fermé, marquez l’événement comme résolu. L’état des événements à risque résolus est défini sur Fermé et ces événements n’entrent plus dans le calcul du risque d’un utilisateur.

- **Marquer comme faux positif** : dans certains cas, il se peut qu’un événement à risque soit signalé à tort en tant que tel. Vous pouvez réduire le nombre de ces événements en les marquant comme faux positifs. Vous aiderez ainsi les algorithmes d’apprentissage automatique à améliorer la classification des événements similaires par la suite. L’état des événements marqués comme faux positifs est défini sur **Fermé** et ces événements n’entrent plus dans le calcul du risque d’un utilisateur.

- **Ignorer** : si vous n’avez pris aucune mesure de correction, mais souhaitez que l’événement à risque soit supprimé de la liste active, vous pouvez choisir de l’ignorer. Son état sera alors défini sur Fermé. Les événements ignorés n’entrent plus dans le calcul du risque d’un utilisateur. Cette option doit uniquement être utilisée dans des circonstances inhabituelles.

- **Réactiver** : les événements à risque qui ont été fermés manuellement (en choisissant **Résoudre**, **Marquer comme faux positif** ou **Ignorer**) peuvent être réactivés. Leur statut est alors défini à nouveau sur **Actif**. Les événements à risque réactivés contribuent au calcul du niveau de risque d’un utilisateur. Les événements à risque fermés à l’aide d’une mesure de correction (telle qu’une réinitialisation de mot de passe sécurisée) ne peuvent pas être réactivés.



## Correction des événements à risque d’un utilisateur

Une correction est une mesure visant à sécuriser une identité ou un appareil déjà identifié comme potentiellement ou effectivement compromis. Une mesure de correction permet de rétablir la sécurité de l’identité ou de l’appareil et de résoudre les anciens événements à risque associés à l’identité ou à l’appareil.

Pour corriger les événements à risque d’un utilisateur, vous pouvez :

- effectuer une réinitialisation de mot de passe sécurisée pour corriger manuellement les événements à risque de l’utilisateur ; 

- configurer une stratégie de sécurité en matière de risque des utilisateurs pour atténuer ou corriger automatiquement les événements à risque de l’utilisateur ;

- réimager l’appareil infecté.


### Réinitialisation de mot de passe sécurisée

Une réinitialisation de mot de passe sécurisée est une mesure de correction efficace pour de nombreux événements à risque. Lorsqu’elle est effectuée, ces événements à risque sont fermés et le niveau de risque de l’utilisateur correspondant recalculé automatiquement. Vous pouvez utiliser la console d’Identity Protection afin de lancer une réinitialisation de mot de passe pour un utilisateur à risque.

La console offre deux méthodes pour réinitialiser un mot de passe :

**Réinitialiser le mot de passe** : sélectionnez **Demander à l’utilisateur de réinitialiser le mot de passe** pour permettre à l’utilisateur de récupérer lui-même son compte s’il s’est inscrit à l’authentification multifacteur. La prochaine fois que l’utilisateur se connectera, il devra résoudre une demande d’authentification multifacteur, puis sera obligé de changer le mot de passe. Cette option n’est pas disponible si le compte d’utilisateur n’est pas déjà inscrit à l’authentification multifacteur.

**Mot de passe temporaire** : sélectionnez **Générer un mot de passe temporaire** pour invalider immédiatement le mot de passe existant et créer un nouveau mot de passe temporaire pour l’utilisateur. Envoyez le nouveau mot de passe temporaire à une autre adresse de messagerie de l’utilisateur ou au responsable de l’utilisateur. Étant donné que le mot de passe est temporaire, l’utilisateur sera invité à changer le mot de passe lors de la connexion.

<br> ![Stratégie](./media/active-directory-identityprotection/71.png "Stratégie") <br>



## Stratégie de sécurité en matière de risque des utilisateurs

Une stratégie de sécurité en matière de risque des utilisateurs est une stratégie d’accès conditionnel qui évalue le niveau de risque d’un utilisateur spécifique et applique des mesures de correction et d’atténuation en fonction de conditions et de règles prédéfinies. <br><br> ![Stratégie en matière de risque des utilisateurs](./media/active-directory-identityprotection/500.png "Stratégie en matière de risque des utilisateurs") <br>

Azure AD Identity Protection vous aide à gérer les mesures de correction et d’atténuation pour les utilisateurs associés à un indicateur de risque en vous permettant de :

- définir les utilisateurs et les groupes auxquels la stratégie s’applique ; <br><br> ![Stratégie en matière de risque des utilisateurs](./media/active-directory-identityprotection/501.png "Stratégie en matière de risque des utilisateurs") <br>

- définir le niveau de risque d’un utilisateur (Faible, Moyen ou Élevé) qui déclenche un changement de mot de passe ; <br><br> ![Stratégie en matière de risque des utilisateurs](./media/active-directory-identityprotection/502.png "Stratégie en matière de risque des utilisateurs") <br>

- définir le niveau de risque d’un utilisateur (Faible, Moyen ou Élevé) qui entraîne un blocage de la connexion ; <br><br> ![Stratégie en matière de risque des utilisateurs](./media/active-directory-identityprotection/503.png "Stratégie en matière de risque des utilisateurs") <br>

- vérifier et évaluer l’impact d’un changement avant de l’appliquer. <br><br> ![Stratégie en matière de risque des utilisateurs](./media/active-directory-identityprotection/504.png "Stratégie en matière de risque des utilisateurs") <br>


La sélection d’un niveau de risque **Élevé** réduit la fréquence de déclenchement d’une stratégie et minimise l’impact sur les utilisateurs. Cependant, cela a pour effet d’exclure les utilisateurs associés à un indicateur de risque **Faible** et **Moyen**. Par conséquent, il se peut que des identités ou des appareils déjà identifiés comme potentiellement ou effectivement compromis ne soient pas sécurisés.

Lors de la définition de la stratégie,

- excluez les utilisateurs susceptibles de générer un grand nombre de faux positifs (développeurs, analystes de sécurité) ;

- excluez les utilisateurs situés dans des régions où l’activation de la stratégie n’est pas adaptée (par exemple, aucun accès au support technique) ;

- utilisez un niveau de risque **Élevé** pendant le déploiement initial de la stratégie ou si vous devez minimiser la complexité pour les utilisateurs finaux ;

- utilisez un niveau de risque **Faible** si votre organisation requiert une sécurité accrue. La sélection d’un niveau de risque **Faible** complique la connexion pour les utilisateurs, mais renforce la sécurité.

Pour la plupart des organisations, nous recommandons de configurer un niveau de risque **Moyen** afin d’établir un juste équilibre entre facilité d’utilisation et sécurité.



### Atténuation des événements à risque d’un utilisateur
Les administrateurs peuvent définir une stratégie de sécurité en matière de risque des utilisateurs pour bloquer les utilisateurs lors de la connexion selon le niveau de risque.

Le blocage d’une connexion :
 
- empêche la génération de nouveaux événements à risque pour l’utilisateur concerné ;

- permet aux administrateurs de corriger les événements à risques affectant l’identité de l’utilisateur et de sécuriser à nouveau cette dernière.





### Flux de correction et d’atténuation du risque d’un utilisateur  

Les sections suivantes fournissent une vue d’ensemble de l’expérience utilisateur pour les flux de correction et d’atténuation du risque d’un utilisateur.

#### Flux de récupération de compte compromis

Lorsqu’une stratégie de sécurité en matière de risque des utilisateurs a été configurée, les utilisateurs dont le niveau de risque correspond à celui spécifié dans la stratégie (et qui sont donc considérés comme compromis) doivent passer par le flux de récupération de compte compromis avant de pouvoir se connecter.

Le flux de récupération de compte compromis comporte trois étapes :

1. L’utilisateur est informé que la sécurité de son compte est menacée en raison d’activités suspectes ou de la divulgation de ses informations d’identification.

<br> ![Correction](./media/active-directory-identityprotection/101.png "Correction") <br>

2.	L’utilisateur doit prouver son identité en répondant à une question de sécurité. Si l’utilisateur est inscrit à l’authentification multifacteur, il peut récupérer lui-même son compte compromis. Il devra saisir un code de sécurité envoyé sur son téléphone. 

<br> ![Correction](./media/active-directory-identityprotection/110.png "Correction") <br>


3.	Enfin, l’utilisateur est obligé de changer son mot de passe, car il se peut que quelqu’un d’autre ait eu accès à son compte. Vous trouverez ci-dessous des captures d’écran de cette expérience.
 
<br> ![Correction](./media/active-directory-identityprotection/111.png "Correction") <br>







 
#### Réinitialiser le mot de passe
Si un utilisateur voit sa connexion bloquée, un administrateur peut générer un mot de passe temporaire pour lui. Il devra changer son mot de passe la prochaine fois qu’il se connectera. Un changement de mot de passe corrige et atténue la plupart des types d’événements à risque pour l’utilisateur.

<br> ![Correction](./media/active-directory-identityprotection/160.png "Correction") <br>


### Atténuation du niveau de risque d’un utilisateur

### Compte compromis : bloqué 

Pour débloquer un compte bloqué par une stratégie de sécurité en matière de risque des utilisateurs, l’utilisateur doit contacter un administrateur ou son support technique. Il n’a pas la possibilité de récupérer lui-même son compte en résolvant l’authentification multifacteur dans ce cas précis.

<br> ![Correction](./media/active-directory-identityprotection/104.png "Correction") <br>


## Atténuation des événements à risque à la connexion 
Une atténuation est une mesure visant à limiter la probabilité qu’un pirate exploite une identité ou un appareil compromis, mais qui ne permet pas de rétablir la sécurité de l’identité ou de l’appareil en question. Une atténuation ne résout pas les précédents événements de connexion à risque associés à l’identité ou à l’appareil.

Vous pouvez utiliser l’accès conditionnel dans Azure AD Identity Protection pour atténuer automatiquement les événements à risque à la connexion. Avec ce type de stratégie, vous prenez en compte le niveau de risque de l’utilisateur ou de la connexion pour bloquer les connexions à risque ou exiger une authentification multifacteur de la part de l’utilisateur. Ces mesures peuvent empêcher un cybercriminel d’exploiter une identité volée pour causer des dommages et vous donner le temps de sécuriser l’identité.


## Stratégie de sécurité en matière de risque à la connexion

Une stratégie en matière de risque à la connexion est une stratégie d’accès conditionnel consistant à évaluer le risque associé à une connexion spécifique et qui applique des mesures d’atténuation à partir de règles et de conditions prédéfinies.<br><br> ![Stratégie en matière de risque à la connexion](./media/active-directory-identityprotection/700.png "Stratégie en matière de risque à la connexion") <br>

Azure AD Identity Protection vous aide à gérer l’atténuation des connexions à risque en vous permettant de :

- définir les utilisateurs et les groupes auxquels la stratégie s’applique ; <br><br> ![Stratégie en matière de risque à la connexion](./media/active-directory-identityprotection/701.png "Stratégie en matière de risque à la connexion") <br>

- définir le niveau de risque à la connexion (Faible, Moyen ou Élevé) qui déclenche une demande d’authentification multifacteur pour les connexions concernées ; <br><br> ![Stratégie en matière de risque à la connexion](./media/active-directory-identityprotection/702.png "Stratégie en matière de risque à la connexion") <br>

- définir le niveau de risque à la connexion (Faible, Moyen ou Élevé) qui entraîne le blocage des connexions concernées ; <br><br> ![Stratégie en matière de risque à la connexion](./media/active-directory-identityprotection/703.png "Stratégie en matière de risque à la connexion") <br>

- vérifier et évaluer l’impact d’un changement avant de l’appliquer. <br><br> ![Stratégie en matière de risque à la connexion](./media/active-directory-identityprotection/704.png "Stratégie en matière de risque à la connexion") <br>

 
La sélection d’un niveau de risque **Élevé** réduit la fréquence de déclenchement d’une stratégie et minimise l’impact sur les utilisateurs.<br> Cependant, cela a pour effet d’exclure les connexions associées à un indicateur de risque **Faible** et **Moyen**. Par conséquent, il se peut qu’un cybercriminel soit en mesure d’exploiter une identité compromise.

Lors de la définition de la stratégie,

- excluez les utilisateurs qui ne sont pas inscrits/ne peuvent pas s’inscrire à l’authentification multifacteur ;

- excluez les utilisateurs situés dans des régions où l’activation de la stratégie n’est pas adaptée (par exemple, aucun accès au support technique) ;

- excluez les utilisateurs susceptibles de générer un grand nombre de faux positifs (développeurs, analystes de sécurité) ;

- utilisez un niveau de risque **Élevé** pendant le déploiement initial de la stratégie ou si vous devez minimiser la complexité pour les utilisateurs finaux ;

- utilisez un niveau de risque **Faible** si votre organisation requiert une sécurité accrue. La sélection d’un niveau de risque **Faible** complique la connexion pour les utilisateurs, mais renforce la sécurité.

Pour la plupart des organisations, nous recommandons de configurer un niveau de risque **Moyen** afin d’établir un juste équilibre entre facilité d’utilisation et sécurité.

 
La stratégie en matière de risque à la connexion :

- est appliquée à l’ensemble du trafic de navigateur et des connexions utilisant une authentification moderne ;
- n’est pas appliquée aux applications utilisant des protocoles de sécurité plus anciens en désactivant le point de terminaison WS-Trust sur le fournisseur d’identité fédérée, tels qu’ADFS.

La page **Événements à risque** de la console Identity Protection répertorie tous les événements :

- auxquels cette stratégie a été appliquée ;
- pour lesquels vous pouvez consulter l’activité afin de déterminer si la mesure était appropriée ou non. 




## Stratégie d’inscription à l’authentification multifacteur

L’authentification multifacteur offre une garantie supplémentaire de l’identité d’un utilisateur.<br> L’inscription à l’authentification multifacteur est une étape essentielle pour aider votre organisation à se protéger contre les compromissions de comptes et à récupérer les comptes compromis. <br> ![Inscription à MFA](./media/active-directory-identityprotection/600.png "Inscription à MFA") <br>

Azure AD Identity Protection vous permet de gérer le déploiement de l’inscription à l’authentification multifacteur en vous permettant de :

- définir les utilisateurs et les groupes auxquels la stratégie s’applique ; <br><br> ![Inscription à MFA](./media/active-directory-identityprotection/601.png "Inscription à MFA") <br>

- définir la durée pendant laquelle ils sont autorisés à ignorer l’inscription ; <br><br> ![Inscription à MFA](./media/active-directory-identityprotection/602.png "Inscription à MFA") <br>

- afficher l’état d’inscription actuel des utilisateurs concernés. <br><br> ![Inscription à MFA](./media/active-directory-identityprotection/603.png "Inscription à MFA") <br>


##Flux d’atténuation du risque à la connexion 

#### Flux de récupération de connexion à risque

Lorsqu’un administrateur a configuré une stratégie pour les risques à la connexion, les utilisateurs affectés sont avertis quand ils tentent de se connecter.

Le flux de connexion à risque comporte deux étapes :

1. L’utilisateur est informé que quelque chose d’inhabituel a été détecté concernant sa connexion, par exemple en cas de connexion depuis un nouvel emplacement, un nouvel appareil ou une nouvelle application. <br> ![Correction](./media/active-directory-identityprotection/120.png "Correction") <br>

2. L’utilisateur doit prouver son identité en répondant à une question de sécurité. Si l’utilisateur est inscrit à l’authentification multifacteur, il doit saisir un code de sécurité envoyé sur son téléphone. Comme il s’agit simplement d’une connexion à risque et non pas d’un compte compromis, l’utilisateur ne doit pas changer le mot de passe dans ce flux. <br> ![Correction](./media/active-directory-identityprotection/121.png "Correction") <br>
 
#### Connexion à risque bloquée
Les administrateurs peuvent également choisir de définir une stratégie en matière de risque à la connexion pour bloquer les utilisateurs lors de la connexion selon le niveau de risque. Pour débloquer leur accès, les utilisateurs finaux doivent contacter un administrateur ou leur support technique, ou ils peuvent essayer de se connecter depuis un emplacement ou un appareil connu. Il n’a pas la possibilité de récupérer lui-même son compte en résolvant l’authentification multifacteur dans ce cas précis.

<br> ![Correction](./media/active-directory-identityprotection/130.png "Correction") <br>
 
#### Inscription à l’authentification multifacteur

L’utilisateur bénéficie d’une expérience optimale pour le flux de récupération de compte compromis et de connexion à risque lorsqu’il peut effectuer lui-même l’opération de récupération. Si un utilisateur est inscrit à l’authentification multifacteur, il a déjà un numéro de téléphone associé à son compte qu’il peut utiliser pour répondre aux questions de sécurité. La récupération d’un compte suite à sa compromission ne nécessite pas l’intervention du support technique ou d’un administrateur. Par conséquent, nous vous recommandons vivement de demander à vos utilisateurs de s’inscrire à l’authentification multifacteur.

Les administrateurs peuvent :

- définir une stratégie qui impose aux utilisateurs d’ajouter une vérification de sécurité supplémentaire à leur compte ; 
- autoriser les utilisateurs à ignorer l’inscription à l’authentification multifacteur pendant 30 jours maximum, s’ils souhaitent leur accorder un délai de grâce avant l’inscription.

 
 <br> ![Correction](./media/active-directory-identityprotection/140.png "Correction") <br> <br> ![Correction](./media/active-directory-identityprotection/141.png "Correction") <br> <br> ![Correction](./media/active-directory-identityprotection/142.png "Correction") <br>

 

#### Inscription à l’authentification multifacteur au cours d’une connexion à risque

Il est important que les utilisateurs s’inscrivent à l’authentification multifacteur afin qu’ils soient prêts à répondre aux demandes de sécurité et en mesure de le faire. Si un utilisateur n’est pas inscrit à l’authentification multifacteur, mais que la stratégie exige qu’il le soit, il se peut qu’il soit invité à s’inscrire pendant une connexion à risque. Cela signifie qu’un cybercriminel peut se retrouver à devoir ajouter un numéro de téléphone à la place de l’utilisateur correct. <br> Pour éviter cette situation, exigez des utilisateurs qu’ils s’inscrivent à l’authentification multifacteur dès que possible, afin qu’un numéro de téléphone soit déjà associé à leur compte en cas de compromission de ce dernier. Les administrateurs peuvent également bloquer complètement les utilisateurs compromis qui ne sont pas inscrits à l’authentification multifacteur.

 <br> ![Correction](./media/active-directory-identityprotection/150.png "Correction") <br> <br> ![Correction](./media/active-directory-identityprotection/151.png "Correction") <br>






 
## Manuel

### Simulation des événements à risque

Cette section donne une vue d’ensemble de la simulation des types d’événements à risque suivants (le niveau de difficulté est indiqué entre parenthèses) :

- Connexions depuis des adresses IP anonymes (facile)

- Connexions depuis des emplacements non connus (moyen)

- Voyage impossible vers des emplacements inhabituels (difficile)

Les autres événements à risque ne peuvent pas être simulés de manière sécurisée.


#### Connexions depuis des adresses IP anonymes

Ce type d’événement à risque signale les utilisateurs qui se sont connectés depuis une adresse IP ayant été identifiée comme l’adresse IP d’un proxy anonyme. Ces proxys sont utilisés par des individus souhaitant masquer l’adresse IP de leur appareil et peuvent être utilisés dans un but malveillant.

Pour simuler une connexion depuis une adresse IP anonyme, procédez comme suit :

1.	Téléchargez le [navigateur Tor](https://www.torproject.org/projects/torbrowser.html.en).
2.	À l’aide du navigateur Tor, accédez à https://myapps.microsoft.com.   
3.	Entrez les informations d’identification du compte que vous souhaitez voir apparaître dans le rapport Connexions depuis des adresses IP anonymes.

Et le tour est joué ! La connexion devrait être visible dans Identity Protection dans un délai de 5 minutes.


####Connexions depuis des emplacements non connus
Le risque lié aux emplacements non connus est déterminé à l’aide d’un mécanisme d’évaluation de connexion en temps réel qui prend en compte les emplacements de connexion passés (IP, latitude/longitude et NSA) pour déterminer les emplacements non connus/nouveaux. Le système stocke les adresses IP, la latitude/longitude et les NSA précédents d’un utilisateur et considère ces emplacements comme « connus ». Un emplacement de connexion est considéré comme non connu si l’emplacement de connexion ne correspond à aucun emplacement connu existant. Le système présente une période d’apprentissage initiale de 14 jours, durant laquelle il ne signale pas les nouveaux emplacements en tant qu’emplacements non connus. Le système ignore également les connexions depuis les appareils connus et les emplacements géographiquement proches d’un emplacement connu existant.

Pour simuler des emplacements non connus, vous devez vous connecter depuis un emplacement et un appareil depuis lesquels le compte ne s’est jamais connecté. Voici la procédure à suivre :

1.	Choisissez un compte qui présente un historique de connexion d’au moins 14 jours. 

2.	Effectuez ensuite l’une ou l’autre de ces étapes :
	
    a. Tout en utilisant un VPN, accédez à [https://myapps.microsoft.com](https://myapps.microsoft.com) et entrez les informations d’identification du compte pour lequel vous souhaitez simuler cet événement à risque.

    b. Demandez à un collaborateur se trouvant à un emplacement différent de se connecter à l’aide des informations d’identification du compte (non recommandé).

Et le tour est joué ! La connexion devrait être visible dans Identity Protection dans un délai de 5 minutes.
 
#### Voyage impossible vers des emplacements inhabituels
La simulation de la condition de voyage impossible est difficile, car l’algorithme utilise l’apprentissage automatique pour éliminer les faux positifs, tels que le voyage impossible depuis des appareils connus ou les connexions depuis des VPN utilisés par d’autres utilisateurs du répertoire. De plus, l’algorithme requiert un historique de connexion de 3 à 14 jours pour l’utilisateur avant de commencer à générer des événements à risque.

1.	À l’aide de votre navigateur standard, accédez à [https://myapps.microsoft.com](https://myapps.microsoft.com).  

2.	Entrez les informations d’identification du compte pour lequel vous souhaitez générer cet événement à risque.

3.	À présent, changez votre agent utilisateur. Vous pouvez changer l’agent utilisateur depuis les outils de développement dans Internet Explorer ou à l’aide d’un module complémentaire de sélecteur d’agents utilisateur dans Firefox ou Chrome.

4.	À présent, changez votre adresse IP. Vous pouvez changer votre adresse IP en utilisant un VPN, le module complémentaire Tor ou en créant une nouvelle machine au sein d’Azure dans un autre centre de données.

5.	Connectez-vous à [https://myapps.microsoft.com](https://myapps.microsoft.com) à l’aide des mêmes informations d’identification que précédemment et dans un délai de quelques minutes seulement après la connexion précédente.

La connexion devrait être visible dans Identity Protection dans un délai de 2 à 4 minutes. Toutefois, en raison des modèles d’apprentissage automatique complexes impliqués, il se peut qu’elle ne soit pas détectée. Il peut être judicieux de répéter ces étapes pour plusieurs comptes Azure AD.


#### Simulation de vulnérabilités 
Les vulnérabilités sont des points faibles exploitables par une personne malveillante au sein de votre environnement Azure AD. Actuellement, Azure AD Identity Protection présente trois types de vulnérabilités tirant parti d’autres fonctionnalités d’Azure AD. Ces vulnérabilités s’affichent automatiquement dans Identity Protection dès lors que ces fonctionnalités sont configurées.

-	Azure AD [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md).
-	Azure AD [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).
-	Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md). 



###Stratégies d’accès conditionnel en fonction des risques
####Risque de compromission de l’utilisateur

**Pour tester le risque de compromission de l’utilisateur, procédez comme suit** :

1.	Connectez-vous à [https://portal.azure.com](https://portal.azure.com) à l’aide des informations d’identification d’administrateur général pour votre client.

2.	Accédez à **Identity Protection**.

3.	Dans le panneau principal d’Azure AD Identity Protection, cliquez sur **Paramètres**.

4.	Dans le panneau **Paramètres du portail** panneau, sous **Règles de sécurité**, cliquez sur **Risque de compromission de l’utilisateur**.

5.	Dans le panneau **Risque à la connexion**, désactivez l’option **Activer la règle**, puis cliquez sur **Enregistrer**.

6.	Pour un compte d’utilisateur donné, simulez un événement à risque lié à des emplacements non connus ou à une adresse IP anonyme. Cela aura pour effet d’élever le niveau de risque de cet utilisateur à **Moyen**.

7.	Patientez quelques minutes et vérifiez que le niveau de risque de votre utilisateur est défini sur **Moyen**.

8.	Accédez au panneau **Paramètres du portail**.

9.	Dans le panneau **Risque de compromission de l’utilisateur**, sélectionnez **Oui** sous **Activer la règle**.

10.	Sélectionnez l’une des options suivantes :

    a. Pour bloquer l’accès, sélectionnez **Moyen** sous **Bloquer la connexion**.

    b. Pour appliquer un changement de mot de passe sécurisé, sélectionnez **Moyen** sous **Exiger une authentification multifacteur**.

13.	Cliquez sur **Enregistrer**.

14. Vous pouvez maintenant tester l’accès conditionnel en fonction des risques en vous connectant à l’aide d’un compte d’utilisateur présentant un niveau de risque élevé. Si le risque de l’utilisateur est défini sur Moyen, votre connexion sera bloquée ou vous serez obligé de changer votre mot de passe, selon la stratégie que vous avez configurée. <br><br> ![Manuel](./media/active-directory-identityprotection/201.png "Manuel") <br>

 
####Risque à la connexion

 


Pour tester le risque à la connexion, procédez comme suit :

1.	Connectez-vous à [https://portal.azure.com](https://portal.azure.com) à l’aide des informations d’identification d’administrateur général pour votre client.

2.	Accédez à **Identity Protection**.

3.	Dans le panneau principal d’**Azure AD Identity Protection**, cliquez sur **Paramètres**.

4.	Dans le panneau **Paramètres du portail** panneau, sous **Règles de sécurité**, cliquez sur **Risque à la connexion**.

5.	Dans le panneau **Risque à la connexion**, sélectionnez **Oui** sous **Activer la règle**.

7.	Sélectionnez l’une des options suivantes :

    a. Pour bloquer l’accès, sélectionnez **Moyen** sous **Bloquer la connexion**.

    b. Pour appliquer un changement de mot de passe sécurisé, sélectionnez **Moyen** sous **Exiger une authentification multifacteur**.

8.	Pour bloquer l’accès, sélectionnez Moyen sous Bloquer la connexion...

9.	Pour appliquer l’authentification multifacteur, sélectionnez Moyen sous Exiger une authentification multifacteur.

10.	Cliquez sur **Save** (Enregistrer).

11.	Vous pouvez maintenant tester l’accès conditionnel en fonction des risques en simulant les événements à risque liés à des emplacements inhabituels ou à des adresses IP anonymes, dont le risque est considéré comme moyen.

<br> ![Manuel](./media/active-directory-identityprotection/200.png "Manuel") <br>


## Voir aussi

 - [Azure AD and Identity Show: Identity Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview) (Émission sur Azure AD et l’identité : présentation d’Identity Protection)
 - [Glossaire du service Identity Protection](active-directory-identityprotection-glossary.md)

<!----HONumber=AcomDC_0302_2016-->