<properties linkid="manage-services-biztalk-services-throttling" urlDisplayName="Throttling" pageTitle="Throttling thresholds in BizTalk Services | Azure" metaKeywords="BizTalk Services, throttling, Azure" description="Learn about throttling thresholds and resulting runtime behaviors for BizTalk Services. Throttling is based on memory usage and number of simultaneous messages." metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Throttling" authors="mandia" solutions="" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia"></tags>

# BizTalk Services : limitation

Azure BizTalk Services implémente la limitation de service selon deux conditions : l'utilisation de la mémoire et le nombre de traitements simultanés des messages. Cette rubrique répertorie les seuils de limitation et décrit le comportement d'exécution lorsque les conditions de limitation sont réunies.

## Seuils de limitation

Le tableau suivant répertorie les sources et seuils de limitation :

<table border="1">
<tr bgcolor="FAF9F9">
<th>
</th>
<td>
<strong>Description</strong>

</td>
<td>
<strong>Seuil minimum</strong>

</td>
<td>
<strong>Seuil maximum</strong>

</td>
</tr>
<tr>
<td>
Mémoire

</td>
<td>
Pourcentage de la mémoire système totale disponible/PageFileBytes (octets de fichier de page).
<br/><br/>
 Le total de PageFileBytes disponible correspond environ à 2 fois la mémoire RAM du système.

</td>
<td>
60%

</td>
<td>
70%

</td>
</tr>
<tr>
<td>
Traitement de message

</td>
<td>
Nombre de messages traités simultanément

</td>
<td>
40 \* nombre de cœurs

</td>
<td>
100 \* nombre de cœurs

</td>
</tr>
</table>
Lorsqu'un seuil maximal est atteint, Azure BizTalk Services active les limitations. La limitation s'arrête une fois le seuil minimal atteint. Par exemple, votre service utilise 65 % de la mémoire système. Dans ce cas, le service n'utilise pas de limitation. Votre service monte à 70 % d'utilisation de mémoire système. Dans ce cas, le service lance une limitation et continue de l'appliquer jusqu'à ce que le service utilise 60 % (seuil minimal) de la mémoire système.

Azure BizTalk Services assure le suivi du statut de la limitation (état normal contre état limité) et de la durée de la limitation.

## Comportement d'exécution

Lorsque Azure BizTalk Services passe en mode limitation, les actions suivantes se produisent :

-   La limitation s'effectue par instance de rôle. Par exemple :
    L'instance RoleInstanceA est limitée. L'instance RoleInstanceB ne l'est pas. Dans ce cas, les messages dans RoleInstanceB sont traités normalement. Les messages dans RoleInstanceA sont ignorés et le message d'erreur suivant est affiché :<br/><br/>
    Le serveur est occupé. Réessayez.<br/><br/>
-   Les sources d'extraction cessent d'interroger et de télécharger les messages. Par exemple :<br/>
    Un pipeline extrait les messages depuis une source FTP externe. L'instance de rôle responsable de l'extraction passe en mode limité. Dans cette situation, le pipeline cesse de télécharger de nouveaux messages tant que l'instance de rôle est limitée.
-   Une réponse est envoyée au client afin qu'il puisse soumettre le message de nouveau.
-   Vous devez patienter jusqu'à la fin de la limitation. Plus précisément, vous devez attendre que le seuil minimal soit atteint.

## Remarques importantes

-   Il n'est pas possible de désactiver la limitation.
-   Il n'est pas possible de modifier les seuils de limitation.
-   La limitation est déployée sur l'ensemble du système.
-   Le serveur de base de données SQL d'Azure comporte également un dispositif de limitation intégré.

## Autres rubriques Azure BizTalk Services

-   [Installation du Kit de développement logiciel (SDK) Azure BizTalk Services][]
-   [Didacticiels : Azure BizTalk Services][]
-   [Utilisation du Kit de développement logiciel (SDK) Azure BizTalk Services][]
-   [Azure BizTalk Services][]

## Voir aussi

-   [BizTalk Services : Tableau comparatif des éditions Développeur, De base, Standard, et Premium de BizTalk Services][]
-   [BizTalk Services : Approvisionnement à l'aide du portail de gestion Azure][]
-   [BizTalk Services : Tableau comparatif des états d'approvisionnement BizTalk Services][]
-   [BizTalk Services : Tableau de bord, Surveiller et Mettre à l'échelle dans BizTalk Services][]
-   [BizTalk Services : Sauvegarde et restauration][]
-   [BizTalk Services : Nom et clé de l'émetteur][]

  [Installation du Kit de développement logiciel (SDK) Azure BizTalk Services]: http://go.microsoft.com/fwlink/p/?LinkID=241589
  [Didacticiels : Azure BizTalk Services]: http://go.microsoft.com/fwlink/p/?LinkID=236944
  [Utilisation du Kit de développement logiciel (SDK) Azure BizTalk Services]: http://go.microsoft.com/fwlink/p/?LinkID=302335
  [Azure BizTalk Services]: http://go.microsoft.com/fwlink/p/?LinkID=303664
  [BizTalk Services : Tableau comparatif des éditions Développeur, De base, Standard, et Premium de BizTalk Services]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [BizTalk Services : Approvisionnement à l'aide du portail de gestion Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [BizTalk Services : Tableau comparatif des états d'approvisionnement BizTalk Services]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [BizTalk Services : Tableau de bord, Surveiller et Mettre à l'échelle dans BizTalk Services]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [BizTalk Services : Sauvegarde et restauration]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [BizTalk Services : Nom et clé de l'émetteur]: http://go.microsoft.com/fwlink/p/?LinkID=303941
