---
title: "Outil Microsoft de modélisation des menaces - Azure | Microsoft Docs"
description: "Page principale de l’outil Microsoft de modélisation des menaces, contenant des mesures de correction pour les menaces générées les plus exposées"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: rodsan
translationtype: Human Translation
ms.sourcegitcommit: 2ad59f9b463671e2b74708871a9a9faae9bd24eb
ms.openlocfilehash: b75de472508bbf44c6e02e0d751054f37b0e1b8c
ms.lasthandoff: 02/21/2017


---

# <a name="microsoft-threat-modeling-tool"></a>Outil Microsoft de modélisation des menaces 
L’outil de modélisation des menaces est un élément essentiel de Microsoft Security Development Lifecycle (SDL). Il permet aux architectes logiciels d’identifier et de corriger les problèmes de sécurité potentiels au plus tôt, lorsqu’ils sont relativement simples et peu coûteux à résoudre. Par conséquent, il réduit considérablement le coût total de développement. En outre, nous avons conçu cet outil en pensant aux personnes qui ne sont pas expertes en sécurité et nous avons facilité la modélisation des menaces pour tous les développeurs en fournissant des conseils éclairés sur la création et l’analyse des modèles de menace. 

Cet outil permet à quiconque de :
* Communiquer sur la conception de la sécurité de ses systèmes
* Analyser ces conceptions afin de détecter d’éventuels problèmes de sécurité par le biais d’une méthodologie éprouvée
* Proposer et gérer des mesures de correction pour les problèmes de sécurité

Voici quelques-unes des fonctionnalités et innovations de l’outil :
* **Automatisation :** conseils et commentaires sur le dessin d’un modèle
* **STRIDE per Element :** analyse guidée des menaces et des mesures de correction
* **Création de rapports :** activités de sécurité et test pendant la phase de vérification
* **Méthodologie unique :** permet aux utilisateurs de mieux visualiser et comprendre les menaces
* **Conçu pour les développeurs et centré sur le logiciel :** de nombreuses approches sont centrées sur les ressources ou les personnes malveillantes. Nous sommes centrés sur le logiciel. Nous nous appuyons sur les activités que connaissent tous les architectes et développeurs de logiciels, par exemple le dessin d’images pour l’architecture logicielle
* **Axé sur l’analyse de la conception :** le terme « modélisation des menaces » peut faire référence à des spécifications ou à une technique d’analyse de la conception. Parfois, il fait référence à un mélange complexe des deux concepts. L’approche Microsoft SDL de modélisation des menaces est une technique d’analyse de conception ciblée

## <a name="threats"></a>Menaces

L’outil de modélisation des menaces vous aide à répondre à certaines questions, par exemple :

* Comment une personne malveillante peut-elle modifier les données d’authentification ?
* Quelles sont les répercussions lorsqu’une personne malveillante est en mesure de lire les données de profil utilisateur ?
* Que se passe-t-il si l’accès à la base de données de profils utilisateur est refusé ?

Pour vous aider à formuler ces questions spécifiques, Microsoft utilise le modèle STRIDE qui classifie les différents types de menaces et simplifie les conversations de sécurité globale.

| Catégorie | Description |
| -------- | ----------- |
| Usurpation d’identité | Implique l’accès illégal aux informations d’authentification d’un autre utilisateur, comme le nom d’utilisateur et le mot de passe, ainsi que leur utilisation |
| Falsification | Implique la modification malveillante de données. On peut citer par exemple des modifications non autorisées apportées aux données persistantes, comme celles contenues dans une base de données, et l’altération de données circulant entre deux ordinateurs sur un réseau ouvert, tel qu’Internet |
| Répudiation | Associée aux utilisateurs refusant d’effectuer une action sans que d’autres parties aient un moyen de prouver le contraire : par exemple, un utilisateur effectue une opération illégale dans un système qui ne permet pas de tracer les opérations interdites. La non-répudiation fait référence à la capacité d’un système à contrer les menaces de répudiation. Par exemple, un utilisateur qui achète un article peut être obligé de donner sa signature à la réception de l’article. Le fournisseur peut ensuite utiliser l’accusé de réception signé comme preuve de réception du colis par l’utilisateur |
| Divulgation d’informations | Implique l’exposition d’informations à des personnes qui ne devraient pas pour avoir accès à ce service, par exemple, la capacité des utilisateurs à lire un fichier auxquels ils n’ont pas été autorisés à accéder, ou la capacité d’un intrus à lire les données en transit entre deux ordinateurs |
| Déni de service | Les attaques par déni de service (DoS) refusent un service aux utilisateurs valides, par exemple en rendant un serveur web temporairement indisponible ou inutilisable. Vous devez vous protéger contre certains types de menaces DoS, simplement pour améliorer la fiabilité et la disponibilité du système |
| Élévation de privilège | Un utilisateur sans privilège obtient un accès privilégié et donc suffisant pour compromettre ou détruire tout le système. Les menaces d’élévation de privilège incluent les situations dans lesquelles une personne malveillante a percé toutes les défenses du système et fait désormais partie intégrante du système de confiance proprement dit : cette situation est très dangereuse |

## <a name="mitigations"></a>Corrections

Les corrections de l’outil de modélisation des menaces sont classifiées en fonction de l’infrastructure de sécurité des applications web, comportant les éléments suivants :

| Catégorie | Description |
| -------- | ----------- |
| [Audit et journalisation](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-auditing-and-logging) | Qui a fait quoi et quand ? L’audit et la journalisation font référence à la manière dont votre application enregistre les événements liés à la sécurité |
| [Authentification](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-authentication) | Qui êtes-vous ? L’authentification désigne le processus dans lequel une entité prouve l’identité d’une autre entité, généralement par le biais d’informations d’identification, comme le nom d’utilisateur et le mot de passe |
| [Autorisation](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-authorization) | Que pouvez-vous faire ? L’autorisation désigne la manière dont votre application fournit des contrôles d’accès pour les ressources et les opérations | 
| [Sécurité des communications](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-communication-security) | À qui parlez-vous ? La sécurité des communications garantit que toutes les communications effectuées sont aussi sécurisées que possible |
| [Gestion des configurations](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-configuration-management) | Pour qui votre application s’exécute-t-elle ? À quelles bases de données se connecte-t-elle ? Comment votre application est-elle administrée ? Comment ces paramètres sont-ils sécurisés ? La gestion de la configuration fait référence à la façon dont votre application gère ces problèmes opérationnels | 
| [Cryptographie](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-cryptography) | Comment conservez-vous les secrets (confidentialité) ? Comment protégez-vous vos données ou vos bibliothèques de la falsification (intégrité) ? Comment obtenez-vous des valeurs aléatoires solides d’un point de vue cryptographique ? La cryptographie fait référence à la façon dont votre application applique les principes de confidentialité et d’intégrité | 
| [Gestion des exceptions](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-exception-management) | En cas d’échec d’un appel de méthode dans votre application, que fait votre application ? Dans quelle mesure révélez-vous vos informations ? Renvoyez-vous des messages d’erreur conviviaux aux utilisateurs finaux ? Transmettez-vous des informations d’exception utiles à l’appelant ? Votre application échoue-t-elle de manière appropriée ? |
| [Validation des entrées](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-input-validation) | Comment savez-vous que l’entrée reçue par votre application est valide et sécurisée ? La validation des entrées fait référence à la manière dont votre application filtre, nettoie ou refuse les entrées avant tout traitement supplémentaire. Pensez à limiter les entrées par le biais des points d’entrée et à encoder les sorties par le biais des points de sortie. Faites-vous confiance aux données provenant de sources telles que les bases de données et les partages de fichiers ? |
| [Données sensibles](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-sensitive-data) | Comment votre application gère-t-elle les données sensibles ? Les données sensibles font référence à la manière dont votre application gère toutes les données devant être protégées en mémoire, sur le réseau, ou dans les magasins persistants | 
| [Gestion des sessions](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-session-management) | Comment votre application gère-t-elle et protège-t-elle les sessions utilisateur ? Une session fait référence à une série d’interactions associées entre un utilisateur et votre application web | 

Cela vous permet d’identifier : 

* Où les erreurs les plus courantes se produisent
* Où les améliorations les plus exploitables sont effectuées

Par conséquent, vous utilisez ces catégories pour cibler et hiérarchiser votre travail de sécurité : si vous connaissez les problèmes de sécurité les plus courants dans les catégories de validation, d’authentification et d’autorisation des entrées, vous pouvez démarrer par là. Pour plus d’informations, cliquez [ici](https://www.google.com/patents/US7818788)

## <a name="next-steps"></a>Étapes suivantes
Consultez ces liens utiles pour commencer dès aujourd’hui :
* [Lien de téléchargement](https://www.microsoft.com/download/details.aspx?id=49168)
* [Prise en main](https://msdn.microsoft.com/magazine/dd347831.aspx)
* [Formation de base](https://www.microsoft.com/download/details.aspx?id=16420)

Lorsque vous êtes prêt, découvrez le travail de quelques experts de l’outil de modélisation des menaces :
* [Threats Manager (Gestionnaire de menaces)](https://simoneonsecurity.com/threatsmanagersetup-v1-5-10/)
* [Blog sur la sécurité de Simone Curzi](https://simoneonsecurity.com/)
