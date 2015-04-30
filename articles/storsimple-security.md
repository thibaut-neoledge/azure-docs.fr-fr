<properties 
   pageTitle="Sécurité StorSimple" 
   description="Décrit les fonctionnalités de sécurité et de confidentialité qui protègent votre service, vos appareils et vos données StorSimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="AdinaH" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="03/19/2015"
   ms.author="v-sharos"/>

# Sécurité StorSimple 

## Vue d'ensemble

La sécurité est une préoccupation majeure pour toute personne adoptant une nouvelle technologie, surtout lorsque cette technologie est utilisée avec des données confidentielles ou propriétaires. Lorsque vous évaluez différentes technologies, vous devez tenir compte des risques et des coûts relatifs à la protection des données. Microsoft Azure StorSimple fournit une solution de sécurité et de confidentialité pour la protection des données, afin de garantir : 

- **La confidentialité** : seules les entités autorisées peuvent consulter vos données.
- **L'intégrité** : seules les entités autorisées peuvent modifier ou supprimer vos données.

La solution Microsoft Azure StorSimple repose sur l'interaction de quatre composants :

- **Le service StorSimple Manager hébergé dans Microsoft Azure** : le service de gestion vous permettant de configurer et d'approvisionner l'appareil StorSimple.
- **L'appareil StorSimple** : un appareil physique installé dans votre centre de données. Tous les hôtes et les clients qui génèrent des données sont connectés à l'appareil StorSimple, qui gère les données et les déplace vers le cloud Azure selon vos besoins.
- **Les clients/hôtes connectés à l'appareil** : les clients de votre infrastructure se connectant à l'appareil StorSimple et générant des données devant être protégées.
- **Le stockage cloud** : l'emplacement dans le cloud Azure où les données sont stockées.

Les sections suivantes décrivent les fonctionnalités de sécurité StorSimple de chacun de ces composants et les données qu'ils protègent. Elles comprennent également une liste de questions que vous pouvez vous poser sur la sécurité de Microsoft Azure StorSimple et des réponses à celles-ci.

## Protection du service StorSimple Manager

Le service StorSimple Manager est un service de gestion hébergé dans Microsoft Azure, qui permet de gérer tous les appareils StorSimple de votre organisation. Vous pouvez accéder au service StorSimple Manager en vous connectant au portail de gestion Azure via un navigateur web, à l'aide de vos informations d'identification professionnelles. 

L'accès au service StorSimple Manager requiert que votre organisation dispose d'un abonnement Azure incluant StorSimple. Votre abonnement régit les fonctionnalités auxquelles vous pouvez accéder dans le portail Azure. Si votre organisation ne dispose pas d'un abonnement Azure et que vous souhaitez en savoir plus à ce sujet, consultez [Inscription à Azure en tant qu'organisation](sign-up-organization.md). 

Étant donné que le service StorSimple Manager est hébergé dans Azure, il est protégé par les fonctionnalités de sécurité Azure. Pour plus d'informations sur les fonctionnalités de sécurité fournies par Microsoft Azure, accédez au [Centre de gestion de la confidentialité Microsoft Azure](http://azure.microsoft.com/support/trust-center/security/).

## Protection de l'appareil StorSimple

L'appareil StorSimple est un appareil de stockage hybride local qui contient des disques SSD et des disques durs, ainsi que des contrôleurs redondants et des fonctionnalités de basculement automatique. Les contrôleurs gèrent la hiérarchisation du stockage en plaçant les données actuellement (ou souvent) utilisées sur le stockage local (sur l'appareil StorSimple ou les serveurs locaux), tout en déplaçant les données moins fréquemment utilisées sur le cloud.

## Contrôle de l'accès à StorSimple via l'inscription de l'appareil

Seuls les appareils autorisés par StorSimple peuvent rejoindre le service StorSimple Manager que vous avez créé dans votre abonnement Azure. 

Pour autoriser un appareil, vous devez l'inscrire auprès du service StorSimple Manager en fournissant la clé d'inscription. La clé d'inscription est une clé aléatoire de 128 bits, générée dans le portail. Pour savoir comment obtenir une clé d'inscription, passez à l'[étape 2 : obtention de la clé d'inscription](https://msdn.microsoft.com/library/azure/dn772346.aspx).

> [AZURE.NOTE] La clé d'inscription est une clé longue, contenant plus de 100 caractères. Nous vous recommandons de copier la clé et de l'enregistrer dans un fichier texte dans un emplacement sécurisé, afin que vous puissiez l'utiliser pour autoriser des appareils supplémentaires si nécessaire.
> 
> * Si vous égarez la clé d'inscription après avoir enregistré votre premier appareil, vous pouvez générer une nouvelle clé à partir du service StorSimple Manager. Cela n'a pas d'incidence sur le fonctionnement des appareils existants. 
> * Après l'inscription d'un appareil, il utilise des jetons pour communiquer avec Microsoft Azure. La clé d'inscription au service n'est pas utilisée après l'inscription de l'appareil.


## Protection de votre solution StorSimple par des mots de passe

Les mots de passe sont un aspect important de la sécurité informatique et sont très utilisés dans la solution Microsoft Azure StorSimple, afin de garantir que seuls les utilisateurs autorisés ont accès à vos données. StorSimple vous permet de configurer les mots de passe suivants :

- Mot de passe Windows PowerShell pour StorSimple
- Mot de passe Administrateur d'appareil StorSimple
- Mots de passe initiateur et cible de protocole CHAP (Challenge Handshake Authentication Protocol)
- Mot de passe de gestionnaire d'instantanés StorSimple

### Mots de passe Administrateur Windows PowerShell pour StorSimple et appareil StorSimple

Windows PowerShell pour StorSimple est une interface de ligne de commande que vous pouvez utiliser pour gérer l'appareil StorSimple. Windows PowerShell pour StorSimple dispose de fonctionnalités qui vous permettent d'inscrire votre appareil, de configurer l'interface réseau sur votre appareil, d'installer certains types de mises à jour, de résoudre les problèmes de votre appareil en accédant à la session de support et de modifier l'état de l'appareil. Vous pouvez accéder à Windows PowerShell pour StorSimple en vous connectant à la console série ou à l'aide de l'accès distant Windows PowerShell. 

L'accès distant PowerShell peut être effectué via HTTPS ou HTTP. Si la gestion à distance via HTTPS est activée, vous devez télécharger le certificat de gestion à distance à partir du périphérique et l'installer sur le client distant. 

> [AZURE.IMPORTANT] 

> * La gestion à distance est désactivée par défaut. Vous pouvez utiliser le service StorSimple Manager pour l'activer. Pour des raisons de sécurité, l'accès à distance doit être activé uniquement pendant la période durant laquelle il est réellement nécessaire.
> * Si vous modifiez le mot de passe, veillez à informer tous les utilisateurs de l'accès à distance afin d'éviter toute perte de connectivité inattendue.
> * Le service StorSimple Manager ne peut pas récupérer les mots de passe existants : il ne peut que les réinitialiser. Nous vous recommandons de stocker tous les mots de passe dans un endroit sûr, afin de ne pas être obligé de réinitialiser un mot de passe en cas d'oubli. Si vous devez réinitialiser un mot de passe, veillez à informer tous les utilisateurs au préalable. 
> * Vous pouvez accéder à l'interface Windows PowerShell à l'aide d'une connexion série à l'appareil. Vous pouvez également y accéder à distance à l'aide de HTTP ou HTTPS, qui offre une sécurité supplémentaire. Le protocole HTTPS assure un niveau de sécurité supérieur à une connexion série ou HTTP. Toutefois, pour utiliser le protocole HTTPS, vous devez d'abord installer un certificat sur l'ordinateur client qui accède à l'appareil. Vous pouvez télécharger le certificat d'accès à distance à partir de la page de configuration de l'appareil du service StorSimple Manager. En cas de perte du certificat pour l'accès à distance, vous devez en télécharger un nouveau et le transmettre à tous les clients autorisés à utiliser la gestion à distance.

Après avoir utilisé Windows PowerShell pour StorSimple pour vous connecter à l'appareil, vous devez fournir le mot de passe Administrateur de ce dernier pour vous y connecter.

### Mots de passe initiateur et cible de protocole CHAP (Challenge Handshake Authentication Protocol)

CHAP est un schéma d'authentification utilisé par l'appareil StorSimple pour valider l'identité des clients distants. La vérification repose sur un mot de passe partagé. Le protocole CHAP peut être à sens unique (unidirectionnel) ou mutuel (bidirectionnel). Dans le cas de l'authentification CHAP unidirectionnelle, la cible (l'appareil StorSimple) authentifie un initiateur (l'hôte). L'authentification CHAP mutuelle nécessite que la cible authentifie l'initiateur, puis que l'initiateur authentifie la cible. Azure StorSimple peut être configuré pour utiliser l'une ou l'autre de ces méthodes.

> [AZURE.IMPORTANT] 
> 
> * Le nom d'utilisateur CHAP doit contenir moins de 233 caractères.
> * Le mot de passe CHAP doit comprendre entre 12 et 16 caractères. L'utilisation d'un nom d'utilisateur ou d'un mot de passe plus long entraîne un échec d'authentification sur l'hôte Windows.
> * Vous ne pouvez pas utiliser le même mot de passe pour l'initiateur CHAP et la cible CHAP.
> * Une fois le mot de passe défini, il peut être modifié, mais il ne peut pas être récupéré. En cas de modification du mot de passe, veillez à informer tous les utilisateurs de l'accès à distance afin qu'ils puissent se connecter à l'appareil StorSimple.

### Mot de passe de gestionnaire d'instantanés StorSimple

Le gestionnaire d'instantanés StorSimple est un composant logiciel enfichable MMC (Microsoft Management Console) qui utilise des groupes de volumes et Windows Volume Shadow Copy Service pour générer des sauvegardes cohérentes avec les applications. En outre, vous pouvez utiliser le gestionnaire d'instantanés StorSimple pour créer des planifications de sauvegarde et cloner ou restaurer des volumes.

Lorsque vous configurez une unité pour utiliser le gestionnaire d'instantanés StorSimple, vous devez fournir le mot de passe du gestionnaire d'instantanés StorSimple. Ce mot de passe est d'abord défini dans Windows PowerShell pour StorSimple lors de l'inscription. Le mot de passe peut également être défini et modifié à partir du service StorSimple Manager. Ce mot de passe authentifie l'appareil auprès du gestionnaire d'instantanés StorSimple.

> [AZURE.IMPORTANT] <ul><li>Ce mot de passe doit comporter 14 ou 15 caractères et contenir une combinaison d'au moins 3 caractères en majuscules, minuscules, numériques et spéciaux.</li><li>Une fois le mot de passe du gestionnaire d'instantanés Storsimple défini, il peut être modifié, mais il ne peut pas être récupéré. Si vous modifiez le mot de passe, veillez à informer tous les utilisateurs distants.</li></ul>


### Meilleures pratiques relatives aux mots de passe

Nous vous recommandons d'utiliser les instructions suivantes afin de garantir que les mots de passe Azure StorSimple sont forts et bien protégés :

- Modifiez votre mot de passe tous les trois mois.
- Utilisez des mots de passe forts. Pour plus d'informations, consultez Conseils pour créer un mot de passe fort.
- Utilisez toujours des mots de passe différents pour les différents mécanismes d'accès : chacun des mots de passe que vous spécifiez doit être unique.
- Ne partagez pas les mots de passe avec les personnes non autorisées à accéder à l'appareil StorSimple.
- Ne parlez pas d'un mot de passe devant d'autres personnes et ne faites pas allusion à son format.
- Si vous pensez qu'un compte ou un mot de passe a été compromis, signalez l'incident à votre service de sécurité.
- Traitez tous les mots de passe comme des informations sensibles et confidentielles. 

## Protection des données StorSimple

Cette section décrit les fonctionnalités de sécurité Azure StorSimple visant à protéger les données transmises et stockées.

Comme mentionné dans les sections précédentes, les mots de passe sont utilisés pour autoriser et authentifier les utilisateurs et leur permettre d'accéder à votre solution Microsoft Azure StorSimple. La protection des données contre les utilisateurs non autorisés pendant leur transfert entre les systèmes de stockage et pendant leur stockage est également primordiale. Les sections suivantes décrivent les fonctionnalités de protection des données fournies par Azure StorSimple.

> [AZURE.NOTE] La déduplication offre une protection supplémentaire pour les données stockées sur l'appareil StorSimple et dans le stockage Microsoft Azure. Lorsque les données sont dédupliquées, les objets de données sont stockés séparément des métadonnées utilisées pour les mapper et y accéder : aucun contexte au niveau du stockage n'est disponible pour reconstruire les données en fonction de la structure d'un volume, du système de fichiers ou du nom de fichier.

## Protection des données transitant par le service

L'objectif principal du service StorSimple Manager est de gérer et de configurer l'appareil StorSimple. Le service StorSimple Manager s'exécute dans Microsoft Azure. Le portail de gestion Azure vous permet d'entrer des données de configuration d'appareil, puis Microsoft Azure utilise le service StorSimple Manager pour envoyer ces données vers l'appareil. Le service StorSimple Manager utilise un système de paires de clés asymétriques pour garantir que la compromission du service Azure ne compromette pas les informations stockées. Le système de clés asymétriques permet de protéger les données qui transitent par le service de la manière suivante :

1. Un certificat de chiffrement de données qui utilise une paire de clés publiques et privées asymétrique est généré sur l'appareil, puis il est utilisé pour protéger les données. Les clés sont générées lors de l'inscription du premier appareil. 
2. Les clés du certificat de chiffrement de données sont exportées dans un fichier d'échange d'informations personnelles (.pfx), protégé par la clé de chiffrement des données de service, qui est une clé forte de 128 bits, générée de façon aléatoire par l'appareil pendant l'inscription.
3. La clé publique du certificat est accessible en toute sécurité par le service StorSimple Manager et la clé privée est conservée dans l'appareil.
4. Les données entrant dans le service sont chiffrées à l'aide de la clé publique et déchiffrées à l'aide de la clé privée stockée sur l'appareil, afin de garantir que le service Azure ne peut pas déchiffrer les données envoyées vers l'appareil.

> [AZURE.IMPORTANT]
> 
> * La clé de chiffrement des données de service est générée uniquement sur le premier appareil inscrit auprès du service. Tous les autres appareils inscrits auprès du service doivent utiliser la même clé de chiffrement. Il est très important de faire une copie de cette clé et de l'enregistrer dans un emplacement sécurisé. La méthode de stockage de la copie de la clé de chiffrement des données de service doit permettre à toute personne autorisée d'y accéder et de la communiquer facilement à l'administrateur de l'appareil.
> * Vous pouvez modifier la clé de chiffrement des données de service et le certificat de chiffrement de données correspondant en sélectionnant l'option Modification de la clé de chiffrement des données de service du tableau de bord des services. La modification des clés de chiffrement requiert que tous les appareils soient mis à jour avec la nouvelle clé. Par conséquent, nous vous recommandons de modifier la clé lorsque tous les appareils sont en ligne. Si des appareils sont hors connexion, leurs clés peuvent être modifiées plus tard. Les appareils avec des clés obsolètes seront toujours en mesure d'exécuter des sauvegardes, mais ils ne seront pas en mesure de restaurer les données, et ce tant que leur clé n'aura pas été mise à jour. Pour plus d'informations, consultez la page [Tableau de bord des services](https://msdn.microsoft.com/library/azure/dn772326.aspx).
> * Pour vous assurer que la sécurité des données n'est pas compromise, vous devez utiliser un appareil physique StorSimple pour modifier la clé de chiffrement des données de service.
> * Si la clé de chiffrement est perdue, une personne du support technique Microsoft peut vous aider à la récupérer, à condition qu'au moins un appareil soit en état en ligne. Nous vous recommandons de modifier la clé de chiffrement des données de service après sa récupération. Pour obtenir des instructions, consultez la page [Modification de la clé de chiffrement des données de service](https://msdn.microsoft.com/library/azure/8158cbe9-1f26-4513-a031-49f88bb3d481#sec01).
> * La clé de chiffrement des données de service et le certificat de chiffrement de données n'expirent pas. Toutefois, nous vous recommandons de modifier la clé de chiffrement des données de service régulièrement pour des raisons de sécurité.</li></ul>


## Protection des données au repos

L'appareil StorSimple gère les données en les stockant dans des couches locales et dans le cloud, en fonction de leur fréquence d'utilisation. Tous les ordinateurs hôtes connectés à l'appareil envoient des données à l'appareil, qui les déplace ensuite vers le cloud, si nécessaire. Les données sont transférées de l'appareil vers le cloud via le protocole iSCSI. Chaque périphérique possède une cible iSCSI qui couvre tous les volumes partagés sur cet appareil. Toutes les données sont chiffrées avant d'être envoyées vers le stockage cloud. Pour garantir la sécurité et l'intégrité des données déplacées vers le cloud, Azure StorSimple vous permet de définir des clés de chiffrement de stockage cloud comme suit :

- Vous spécifiez la clé de chiffrement de stockage cloud lorsque vous créez un conteneur de volume. La clé ne peut pas être modifiée ou ajoutée ultérieurement. 
- Tous les volumes d'un conteneur de volumes partagent la même clé de chiffrement. Si vous souhaitez utiliser une autre forme de cryptage pour un volume spécifique, nous vous recommandons de créer un nouveau conteneur de volumes pour héberger ce volume.
- Lorsque vous entrez la clé de chiffrement de stockage cloud dans le service StorSimple Manager, la clé est chiffrée à l'aide de la partie publique de la clé de chiffrement des données de service, puis envoyée à l'appareil.
- La clé de chiffrement de stockage cloud n'est pas stockée dans le service et est connue uniquement de l'appareil.
- La spécification d'une clé de chiffrement de stockage cloud est facultative. Vous pouvez envoyer des données chiffrées à l'hôte et à l'appareil.

## Protection des données par les comptes de stockage

Chaque abonnement Microsoft Azure peut créer un ou plusieurs comptes de stockage. (Un compte de stockage fournit un espace de noms unique pour utiliser les données stockées dans le cloud Azure.) L'accès à un compte de stockage est contrôlé par l'abonnement et les clés d'accès associées à ce compte de stockage. 

Lorsque vous créez un compte de stockage, Microsoft Azure génère deux clés d'accès de stockage 512 bits : l'une d'entre elles est utilisée pour l'authentification lorsque l'appareil StorSimple accède au compte de stockage. Notez que seule une des clés est utilisée. L'autre clé est gardée en réserve, ce qui permet une rotation régulière des clés. Pour ce faire, activez la clé secondaire, puis supprimez la clé primaire. Vous pouvez ensuite créer une nouvelle clé, à utiliser lors de la rotation suivante. (De nombreux centres de données ont recours à la rotation des clés pour des raisons de sécurité). 

> [AZURE.IMPORTANT] 
> 
> Nous vous recommandons de suivre ces méthodes recommandées pour la rotation des clés :
> 
> * Utilisez la rotation des clés de compte de stockage régulièrement pour vous assurer que votre compte de stockage n'est pas accessible aux utilisateurs non autorisés.
> * Votre administrateur Azure doit changer ou régénérer régulièrement la clé primaire ou secondaire à l'aide de la section Stockage du portail de gestion pour accéder directement au compte de stockage.


## Protection des données par chiffrement

StorSimple utilise les algorithmes de chiffrement suivants pour protéger les données stockées sur les composants ou en transit dans votre solution StorSimple.

| Algorithme | Longueur de la clé | Protocoles/applications/commentaires |
| --------- | ---------- | ------------------------------- |
| RSA       | 2048       | RSA PKCS 1 v1.5 est utilisé par le portail de gestion pour chiffrer les données de configuration envoyées à l'appareil : par exemple, les informations d'identification du compte de stockage, la configuration de l'appareil StorSimple et les clés de chiffrement du stockage cloud. |
| AES       | 256        | AES avec CBC est utilisé pour chiffrer la partie publique de la clé de chiffrement des données de service avant leur envoi au portail de gestion par l'appareil StorSimple. Il est également utilisé par l'appareil StorSimple pour chiffrer les données avant qu'elles ne soient envoyées au compte de stockage cloud. |
| RSA       | 2048       | RSA PKCS 1 v1.7 est utilisé par le portail de gestion pour chiffrer la partie publique de la clé de chiffrement des données de service avant de la conserver dans le stockage local du portail. |


## Forum Aux Questions (FAQ)

Voici quelques questions et réponses relatives à la sécurité et à Microsoft Azure StorSimple.

**Q :** mon service est compromis. Quelle est la marche à suivre ?

**R :** vous devez immédiatement changer la clé de chiffrement des données de service et les clés du compte de stockage utilisé pour hiérarchiser les données. Pour obtenir des instructions, consultez :

- [Modification de la clé de chiffrement des données de service](https://msdn.microsoft.com/library/azure/8158cbe9-1f26-4513-a031-49f88bb3d481#sec01)
- [Rotation des clés de comptes de stockage](https://msdn.microsoft.com/library/azure/1747f56e-858a-4cfe-a020-949d7db23b8b#rotate)

**Q :** j'ai un nouveau périphérique StorSimple demandant la clé d'inscription. Comment puis-je la récupérer ?

**R :** cette clé a été créée lors de la création du service StorSimple Manager. Lorsque vous utilisez le service StorSimple Manager pour vous connecter à l'appareil, vous pouvez utiliser la page de démarrage rapide du service pour afficher ou régénérer la clé d'inscription. La génération d'une nouvelle clé de service d'inscription n'a pas d'incidence sur les appareils déjà enregistrés. Pour obtenir des instructions, consultez :

- [Affichage ou régénération de la clé d'inscription](https://msdn.microsoft.com/library/azure/8158cbe9-1f26-4513-a031-49f88bb3d481#BKMK_viewsrk)

**Q :** j'ai perdu ma clé de chiffrement des données de service. Que faire ?

**R :** Contactez le support technique de Microsoft. Ils peuvent se connecter à une session de support sur votre appareil et vous aider à récupérer la clé. Vous devez modifier la clé de chiffrement des données de service immédiatement après l'avoir obtenue, afin de vous assurer que vous êtes le seul à connaître la nouvelle clé. Pour obtenir des instructions, consultez :

- [Modification de la clé de chiffrement des données de service](https://msdn.microsoft.com/library/azure/8158cbe9-1f26-4513-a031-49f88bb3d481#sec01)

**Q :** j'ai autorisé un appareil pour la modification de la clé de chiffrement des données de service, mais n'ai pas démarré le processus de modification de la clé. Que dois-je faire ?

**R :** si le délai d'expiration est dépassé, vous devez autoriser l'appareil à nouveau pour la modification clé de chiffrement des données de service et redémarrer le processus.

**Q :** j'ai modifié la clé de chiffrement des données de service, mais je n'ai pas pu mettre à jour les autres appareils dans un délai de 4 heures. Dois-je recommencer ?

**R :** la période de 4 heures est uniquement valide pour le lancement de la modification. Une fois que vous démarrez le processus de mise à jour sur l'appareil StorSimple autorisé, l'autorisation est valide jusqu'à ce que tous les appareils aient été mis à jour.

**Q :** notre administrateur StorSimple a quitté l'entreprise. Que dois-je faire ?

**R :** modifiez et réinitialisez les mots de passe qui permettent l'accès à l'appareil StorSimple et modifiez la clé de chiffrement des données de service, afin de vous assurer que les nouvelles informations ne sont pas accessibles au personnel non autorisé. Pour obtenir des instructions, consultez :

- [Configuration du gestionnaire d'instantanés StorSimple](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec08)
- [Configuration du mot de passe Administrateur de l'appareil](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec09)
- [Configuration de la gestion à distance](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec10)
- [Configuration de l'initiateur CHAP](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec06)
- [Configuration de la cible CHAP](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec07)

**Q :** je souhaite fournir le mot de passe du gestionnaire d'instantanés StorSimple à un hôte qui se connecte à l'appareil StorSimple, mais le mot de passe n'est pas disponible. Que puis-je faire ?

**R :** si vous avez oublié le mot de passe, vous devez en créer un nouveau. Ensuite, informez tous les utilisateurs existants que le mot de passe a été modifié et qu'ils doivent mettre à jour leurs clients pour utiliser le nouveau mot de passe. Pour obtenir des instructions, consultez :

- [Configuration du gestionnaire d'instantanés](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec08)
- [Authentification d'un appareil](https://msdn.microsoft.com/library/azure/dn790537.aspx)

**Q :** le certificat pour l'accès à distance à Windows PowerShell pour StorSimple a été modifié sur l'appareil. Comment mettre à jour mes clients d'accès à distance ?

**R :** vous pouvez télécharger le nouveau certificat à partir du service StorSimple Manager. Il doit ensuite être installé dans le magasin de certificats de vos clients d'accès à distance. Pour obtenir des instructions, consultez :

- [cmdlet Import-Certificate](https://technet.microsoft.com/library/hh848630.aspx)

**Q :** mes données sont-elles protégées si le service StorSimple Manager est compromis ?

**R :** les données de configuration de service sont toujours chiffrées avec votre clé publique lorsque vous les affichez dans un navigateur Web. Étant donné que le service n'a pas accès à la clé privée, il ne sera pas en mesure d'accéder aux données. Si le service StorSimple Manager est compromis, cela n'a aucune incidence, car aucune clé n'est stockée dans le service StorSimple Manager.

**Q :** si quelqu'un a accès au certificat de chiffrement de données, mes données seront-elles compromises ?

**R :** Microsoft Azure stocke la clé de chiffrement des données du client (le fichier .pfx) dans un format chiffré. Étant donné que le fichier .pfx est chiffré et que le service StorSimple n'a pas la clé de chiffrement nécessaire pour déchiffrer le fichier .pfx, l'accès au fichier .pfx uniquement ne met pas vos données en danger.

**Q :** que se passe-t-il si une entité gouvernementale demande mes données à Microsoft ?

**R :** étant donné que toutes les données sont chiffrées sur le service et que la clé privée est conservée dans l'appareil, l'entité gouvernementale doit demander les données au client.

## Étapes suivantes

[Prise en main de l'appareil physique](https://msdn.microsoft.com/library/azure/dn772410.aspx)

<!--HONumber=52-->