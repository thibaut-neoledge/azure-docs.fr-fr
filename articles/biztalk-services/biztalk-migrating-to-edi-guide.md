---
title: "Guide technique de la migration des solutions EDI BizTalk Server vers BizTalk Services | Microsoft Docs"
description: "Migration EDI vers MABS ; Microsoft Azure BizTalk Services"
services: biztalk-services
documentationcenter: na
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 61c179fa-3f37-495b-8016-dee7474fd3a6
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 1b70fc3d199d7f1521acb534dafec8fb3e69500e
ms.contentlocale: fr-fr
ms.lasthandoff: 05/31/2017

---
# <a name="migrating-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>Migration des solutions EDI BizTalk Server vers BizTalk Services : guide technique

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Auteurs : Tim Wieman et Nitin Mehrotra

Relecteurs : Karthik Bharthy

Écrit avec : Microsoft Azure BizTalk Services : version de février 2014.

## <a name="introduction"></a>Introduction
L'échange de données informatisé (EDI) est un des moyens les plus courants d’échange de données par voie électronique pour les entreprises, également appelé transactions entreprise-entreprise ou B2B. BizTalk Server prend en charge EDI depuis plus de dix ans, depuis la version initiale de BizTalk Server. Avec BizTalk Services, Microsoft maintient la prise en charge des solutions EDI sur la plateforme Microsoft Azure. Les transactions B2B sont principalement externes à une organisation : par conséquent, elles sont plus faciles à effectuer si elles sont implémentées sur une plateforme cloud. Microsoft Azure fournit cette fonctionnalité via BizTalk Services.

Si certains clients considèrent BizTalk Services comme une plateforme « vierge » pour les nouvelles solutions EDI, de nombreux clients ont des solutions EDI BizTalk Server existantes à faire migrer vers Azure. Étant donné que l'architecture d’EDI BizTalk Services repose sur les mêmes entités clés que l'architecture EDI BizTalk Server (partenaires commerciaux, entités, accords), il est possible de migrer des artefacts EDI BizTalk Server vers BizTalk Services.

Ce document décrit certaines des différences liées à la migration des artefacts EDI BizTalk Server vers BizTalk Services. Ce document suppose une connaissance pratique du traitement EDI BizTalk Server et des accords de partenariat commercial. Pour plus d'informations sur l'EDI BizTalk Server, consultez [Gestion des partenaires commerciaux à l'aide de BizTalk Server](https://msdn.microsoft.com/library/bb259970.aspx).

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>Quelle version d'artefacts EDI BizTalk Server peut faire l’objet d’une migration vers BizTalk Services ?
Le module EDI BizTalk Server a été considérablement amélioré pour BizTalk Server 2010. Il a été remodelé pour inclure les partenaires, les profils et les accords. BizTalk Services utilise le même modèle pour organiser les partenaires commerciaux et les départements au sein de ces partenaires commerciaux. Par conséquent, la migration des artefacts EDI de BizTalk Server 2010 et versions ultérieures vers BizTalk Services est un processus beaucoup plus simple. Pour migrer des artefacts EDI associés à des versions antérieures à BizTalk Server 2010, vous devez tout d'abord mettre à niveau vers BizTalk Server 2010, puis migrer vos artefacts EDI vers BizTalk Services.

## <a name="scenariosmessage-flow"></a>Flux de messages/scénarios
Comme avec BizTalk Server, le traitement EDI dans BizTalk Services repose sur une solution de gestion des partenaires commerciaux (TPM). La solution TPM comprend les composants clés suivants :

* les partenaires commerciaux, qui représentent l'organisation dans une transaction B2B ;
* les profils, qui représentent les divisions d'un partenaire commercial ;
* les accords de partenariat commercial (ou accords), qui représentent les accords commerciaux entre deux partenaires/profils.

L'illustration suivante présente les similitudes, ainsi que les différences, entre une solution EDI BizTalk Server et EDI BizTalk Services :

![][EDImessageflow]

Les principales différences et similitudes, entre un flux de solution EDI dans BizTalk Server et BizTalk Services sont :

* Tout comme BizTalk Server qui utilise un pipeline EDIReceive pour recevoir un message EDI et un pipeline EDISend pour envoyer un message EDI, BizTalk Services utilise un pont de réception EDI pour recevoir des messages EDI et un pont d'envoi EDI pour envoyer des messages EDI. Dans BizTalk Server, les pipelines sont associés à un accord à l'aide de ports d’envoi ou de réception. Dans BizTalk Services, l'accord proprement dit indique le pont d’envoi ou de réception.
* Dans BizTalk Server, une fois que le pipeline EDIReceive traite le message EDI, ce dernier est exporté vers une base de données SQL Server. Le pipeline EdiSend récupère ensuite le message à partir de la base de données SQL Server, le traite, puis l'envoie au partenaire commercial.
  
    Dans BizTalk Services, après traitement du message EDI par le pont de réception EDI, ce dernier achemine le message vers un processus externe. Le processus externe peut s'exécuter sur Microsoft Azure ou sur site. Le processus externe doit acheminer le message vers le pont d'envoi EDI : le pont d'envoi n'extrait pas le message intrinsèquement. Après avoir traité le message, le pont d'envoi EDI achemine le message vers le partenaire commercial.

BizTalk Services fournit une expérience de configuration facile à utiliser pour créer et déployer rapidement un accord B2B entre partenaires commerciaux sans configurer une instance Microsoft Azure Compute (rôles Web ou de travail), une base de données SQL Microsoft Azure ou un compte de stockage Microsoft Azure. Des scénarios plus complexes de déploiement nécessitent de lier des flux de travail ou d'autres traitements de service « en bordure » d'un accord de partenariat commercial, autrement dit, avant ou après le traitement du pont EDI d'accord de partenariat commercial. Plus précisément, les séquences d'événements suivantes se produisent au cours du traitement d'un message EDI dans BizTalk Services.

1. Un message EDI est reçu du partenaire commercial Fabrikam.  Pour recevoir les messages EDI des partenaires commerciaux, BizTalk Services prend en charge les protocoles de transport comme FTP, SFTP, AS2 et HTTP/S.
2. Le traitement côté réception de l’accord de partenariat commercial désassemble le message EDI au format XML.  Vous pouvez acheminer le message EDI désassemblé (au format XML) vers les points de terminaison Service Bus comme un point de terminaison Service Bus Relay, une rubrique Service Bus, une file d’attente Service Bus ou un pont BizTalk Services.
3. Les messages XML désassemblés peuvent ensuite être reçus à partir du point de terminaison pour un traitement supplémentaire personnalisé.  Ces points de terminaison peuvent être traités par un composant local ou une instance de calcul Microsoft Azure pour poursuivre le traitement du message dans un service Windows Workflow (WF) ou Windows Communication Foundation (WCF), par exemple.
4. Le « traitement côté envoi » de l’accord de partenariat commercial assemble alors le message XML au format EDI et l'envoie au partenaire commercial Contoso.  Pour envoyer des messages EDI aux partenaires commerciaux, BizTalk Services prend en charge les mêmes protocoles que ceux utilisés pour la réception des messages EDI.

Ce document fournit des instructions relatives à la migration de quelques-uns des différents artefacts EDI BizTalk Server vers BizTalk Services.

## <a name="sendreceive-ports-to-trading-partners"></a>Ports d'envoi/réception des partenaires commerciaux
Dans BizTalk Server, vous définissez les emplacements de réception et les ports de réception pour recevoir des messages EDI/XML de partenaires commerciaux, et vous configurez des ports d'envoi pour envoyer des messages EDI/XML à un partenaire commercial. Vous liez ensuite ces ports à un accord de partenariat commercial à l'aide de la console Administration de BizTalk Server. Dans BizTalk Services, les emplacements de réception des messages des partenaires commerciaux et d’envoi des messages aux partenaires commerciaux sont configurés dans le cadre de l'accord de partenariat commercial proprement dit (dans le cadre des paramètres de transport) dans le portail BizTalk Services.  Les concepts de « ports d'envoi » et « emplacements de réception » n’existent donc pas à proprement dit dans BizTalk Services. Pour plus d’informations, consultez la page [Création d’accords](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx).

## <a name="pipelines-bridges"></a>Pipelines (ponts)
Dans l'EDI BizTalk Server, les pipelines sont des entités de traitement de message qui peuvent également inclure une logique personnalisée pour des fonctionnalités de traitement spécifiques, selon les besoins de l'application. Pour BizTalk Services, l'équivalent serait un pont EDI. Toutefois, dans BizTalk Services, les ponts EDI sont « fermés », pour le moment.  Autrement dit, vous ne pouvez pas ajouter vos propres activités personnalisées à un pont EDI. Tout traitement personnalisé doit être effectué en dehors du pont EDI dans votre application, avant ou après que le message n'atteigne le pont configuré dans le cadre de l'accord de partenariat commercial. Les ponts IAE ont la possibilité d'effectuer un traitement personnalisé. Si vous souhaitez un traitement personnalisé, vous pouvez utiliser des ponts IAE avant ou après le traitement du message par le pont EDI. Pour plus d'informations, consultez [Intégration d'un code personnalisé dans les ponts](https://msdn.microsoft.com/library/azure/dn232389.aspx).

Vous pouvez insérer un flux de publication/abonnement avec du code personnalisé et/ou à l’aide de files d’attente et rubriques de messagerie Service Bus avant réception du message par l’accord de partenariat commercial, ou après le traitement du message par l’accord et son acheminement vers un point de terminaison Service Bus.

Voir la section **Flux de messages/scénarios** dans cette rubrique pour le modèle de flux des messages.

## <a name="agreements"></a>Accords
Si vous connaissez les accords de partenariat commercial de BizTalk Server 2010 utilisés pour le traitement EDI, les accords de partenariat commercial de BizTalk Services vous sembleront très familiers. La plupart des paramètres de l'accord sont identiques et utilisent la même terminologie. Dans certains cas, ils sont beaucoup plus simples que les mêmes paramètres dans BizTalk Server. Microsoft Azure BizTalk Services prend en charge le transport X12, EDIFACT et AS2.

Microsoft Azure BizTalk Services fournit également un outil **de migration de données TPM** pour migrer des partenaires commerciaux et les accords du module de partenaire commercial BizTalk Server vers le portail BizTalk Services. L'outil de migration de données TPM est disponible dans un package d'outils, qui peut être téléchargé à partir du [Kit de développement logiciel (SDK) MABS](http://go.microsoft.com/fwlink/p/?LinkId=235057). Le package comprend également un fichier Lisezmoi qui fournit des instructions sur l'utilisation de l'outil et des informations de dépannage de base pour l'outil.

## <a name="schemas"></a>Schémas
BizTalk Services fournit des schémas EDI qui peuvent être utilisés dans des solutions BizTalk Services.  En outre, les schémas EDI BizTalk Server peuvent également être utilisés avec BizTalk Services, étant donné que le nœud racine du schéma EDI est identique sur BizTalk Server et BizTalk Services. Vous pouvez ainsi prendre directement vos schémas EDI BizTalk Server et les utiliser dans les solutions EDI que vous développez à l'aide de BizTalk Services. Vous pouvez également télécharger les schémas à partir du [Kit de développement logiciel (SDK) MABS](http://go.microsoft.com/fwlink/p/?LinkId=235057).

## <a name="maps-transforms"></a>Mappages (transformations)
Les mappages de BizTalk Server sont appelés transformations dans BizTalk Services. La migration des mappages de BizTalk Server vers BizTalk Services peut être une des tâches les plus complexes à réaliser (en fonction de la complexité du mappage). L'outil de mappage utilisé pour BizTalk Services est différent de l’outil de mappage BizTalk. Même si l’apparence de l’outil est essentiellement la même, le format de mappage sous-jacent est différent. Les fonctoids (appelés **opérations de mappage** dans BizTalk Services) disponibles pour les utilisateurs sont également différents.  En effet, vous ne pouvez pas utiliser directement un mappage BizTalk dans BizTalk Services. En outre, tous les fonctoids disponibles dans BizTalk Server ne sont pas disponibles en tant qu'opérations de mappage dans BizTalk Services.

### <a name="new-transform-operations"></a>Nouvelles opérations de transformation
Si la liste des opérations de mappage de transformation peut paraître très différente de l’outil de mappage de BizTalk Server, les transformations BizTalk Services accomplissent les mêmes tâches, mais de manière différente. Par exemple, les transformations BizTalk Services ont des **opérations de liste** . Cela n'était pas disponible dans l’outil de mappage BizTalk.  Les **opérations de liste** vous permettent de créer et de gérer une « liste », qui est un ensemble d'éléments (également appelés « lignes ») : chaque élément peut avoir plusieurs membres (également appelés « colonnes »).  Vous pouvez trier la liste, sélectionner les éléments en fonction d'une condition, etc.

Les **opérations de boucle**sont un autre exemple de nouvelles fonctionnalités de transformations BizTalk Services.  Il est difficile de créer des boucles imbriquées dans l’outil de mappage BizTalk Server.  Par conséquent, les opérations de mappage de boucle sont ajoutées pour les transformations BizTalk Services.

L’opération de mappage avec l’expression **If-Then-Else** est un autre exemple.  Une opération if-then-else était possible dans l’outil de mappage BizTalk, mais nécessitait plusieurs fonctoids pour accomplir une tâche apparemment simple.

### <a name="migrating-biztalk-server-maps"></a>Migration des mappages BizTalk Server
Microsoft Azure BizTalk Services fournit un outil de migration des mappages BizTalk Server vers les transformations BizTalk Services. **BTMMigrationTool** est disponible dans le package **Outils** fourni avec le [téléchargement du Kit de développement logiciel (SDK) BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkId=235057). Pour plus d'informations sur cet outil, consultez [Conversion d'un mappage BizTalk en transformation BizTalk Services](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx).

Vous pouvez également consulter un exemple de Sandro Pereira, BizTalk MVP, sur la façon de [migrer des mappages BizTalk Server vers les transformations BizTalk Services](http://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx).

## <a name="orchestrations"></a>Orchestrations
Si vous avez besoin de migrer le traitement d'orchestration BizTalk Server vers Microsoft Azure, les orchestrations doivent être réécrites, car Microsoft Azure ne prend pas en charge les orchestrations BizTalk Server.  Vous pouvez réécrire la fonctionnalité d'orchestration dans un service Windows Workflow Foundation 4.0 (WF4).  C’est une réécriture complète, car il n'existe actuellement aucune migration des orchestrations BizTalk Server vers WF4. Voici quelques ressources pour Windows Workflow :

* [*How to integrate a WCF Workflow Service with Service Bus Queues and Topics (Intégration d’un service de workflow WCF aux files d’attente et aux rubriques Service Bus)*](https://msdn.microsoft.com/library/azure/hh709041.aspx) par Paolo Salvatori. 
* [Session *Building apps with Windows Workflow Foundation and Azure (Création d’applications à l’aide de Windows Workflow Foundation et d’Azure)*](http://go.microsoft.com/fwlink/p/?LinkId=237314) de la conférence Build 2011.
* [*Centre de développement Windows Workflow Foundation*](http://go.microsoft.com/fwlink/p/?LinkId=237315) sur MSDN.
* [*Documentation de Windows Workflow Foundation 4 (WF4)*](https://msdn.microsoft.com/library/dd489441.aspx) sur MSDN.

## <a name="other-considerations"></a>Autres considérations
Voici quelques éléments à prendre en compte lors de l'utilisation de BizTalk Services.

### <a name="fallback-agreements"></a>Accords de secours
Le traitement EDI BizTalk Server comprend le concept d’« accords de secours ».  BizTalk Services ne dispose **pas** d’accord de secours pour l’instant.  Pour plus d’informations sur l’utilisation des accords de secours dans BizTalk Server, consultez les rubriques de la documentation BizTalk [Rôle des accords dans le traitement EDI](http://go.microsoft.com/fwlink/p/?LinkId=237317) et [Configuration des propriétés d’un accord global ou de secours](https://msdn.microsoft.com/library/bb245981.aspx).

### <a name="routing-to-multiple-destinations"></a>Acheminement vers plusieurs destinations
Les ponts de BizTalk Services, dans leur état actuel, ne prennent pas en charge le routage des messages vers plusieurs destinations à l'aide du modèle de publication-abonnement. Vous pouvez acheminer les messages d’un pont BizTalk Services vers une rubrique Service Bus, qui peut ensuite avoir plusieurs abonnements pour recevoir le message sur plus d’un point de terminaison.

## <a name="conclusion"></a>Conclusion
Microsoft Azure BizTalk Services est mis à jour régulièrement afin d'ajouter plus de fonctionnalités. Avec chaque mise à jour, nous voulons prendre en charge des fonctionnalités améliorées pour faciliter la création de solutions de bout en bout à l'aide de BizTalk Services et d’autres technologies Azure.

## <a name="see-also"></a>Voir aussi
[Développement d'applications d'entreprise avec Azure](https://msdn.microsoft.com/library/azure/hh674490.aspx)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png

