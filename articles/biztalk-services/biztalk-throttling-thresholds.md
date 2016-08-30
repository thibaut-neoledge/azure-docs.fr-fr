<properties 
	pageTitle="En savoir plus sur la limitation dans BizTalk Services | Microsoft Azure" 
	description="En savoir plus sur les seuils de limitation et les comportements qui s'ensuivent lors de l'exécution pour BizTalk Services. La limitation est basée sur l'utilisation de la mémoire et le nombre de messages. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/15/2016" 
	ms.author="mandia"/>





# Limitation BizTalk Services

Azure BizTalk Services met en place un service de limitation basé sur deux conditions : l’utilisation de la mémoire et le nombre de messages traités simultanément. Cette rubrique répertorie les seuils de limitation et décrit le comportement d’exécution lorsque les conditions de limitation sont réunies.

## Seuils de limitation

Le tableau suivant répertorie les sources et seuils de limitation :

||Description|Seuil minimum|Seuil maximum|
|---|---|---|---|
|Mémoire|Pourcentage de la mémoire système totale disponible/PageFileBytes (octets de fichier de page). <p><p>Le total de PageFileBytes disponible correspond environ à 2 fois la mémoire RAM du système.|60 %|70 %|
|Traitement de message|Nombre de messages traités simultanément|40 * nombre de cœurs|100 * nombre de cœurs|

Lorsqu'un seuil maximal est atteint, Azure BizTalk Services active les limitations. La limitation s'arrête une fois le seuil minimal atteint. Par exemple, votre service utilise 65 % de la mémoire système. Dans ce cas, le service n'utilise pas de limitation. Votre service monte à 70 % d'utilisation de mémoire système. Dans ce cas, le service lance une limitation et continue de l'appliquer jusqu'à ce que le service utilise 60 % (seuil minimal) de la mémoire système.

Azure BizTalk Services assure le suivi du statut de la limitation (état normal contre état limité) et de la durée de la limitation.


## Comportement d'exécution

Lorsque Azure BizTalk Services passe en mode limitation, les actions suivantes se produisent :

- La limitation s'effectue par instance de rôle. Par exemple : <br/> l'instance RoleInstanceA est limitée. L'instance RoleInstanceB ne l'est pas. Dans ce cas, les messages dans RoleInstanceB sont traités normalement. Les messages dans RoleInstanceA sont ignorés et échouent avec le message d'erreur suivant :<br/><br/> **Le serveur est occupé. Veuillez réessayez.**<br/><br/>
- Les sources d'extraction cessent d'interroger et de télécharger les messages. Par exemple : <br/> Un pipeline extrait les messages depuis une source FTP externe. L'instance de rôle responsable de l'extraction passe en mode limité. Dans cette situation, le pipeline cesse de télécharger de nouveaux messages tant que l'instance de rôle est limitée.
- Une réponse est envoyée au client afin qu'il puisse soumettre le message de nouveau.
- Vous devez patienter jusqu'à la fin de la limitation. Plus précisément, vous devez attendre que le seuil minimal soit atteint.

## Remarques importantes
- Il n'est pas possible de désactiver la limitation.
- Il n'est pas possible de modifier les seuils de limitation.
- La limitation est déployée sur l'ensemble du système.
- Le serveur de base de données SQL d'Azure comporte également un dispositif de limitation intégré.

## Autres rubriques Azure BizTalk Services

-  [Installation du Kit de développement logiciel (SDK) Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Didacticiels : Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Utilisation du Kit de développement logiciel (SDK) Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## Voir aussi
- [Tableau comparatif des éditions Développeur, De base, Standard et Premium de BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk Services : approvisionnement avec le portail Azure Classic](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Tableau comparatif des états d'approvisionnement BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Onglets Tableau de bord, Surveiller et Mettre à l'échelle dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Sauvegarde et restauration de BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Nom et clé de l'émetteur dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
 

<!---HONumber=AcomDC_0817_2016-->