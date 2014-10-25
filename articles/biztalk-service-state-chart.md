<properties linkid="manage-services-biztalk-state-chart" urlDisplayName="BizTalk Services: Service state chart" pageTitle="BizTalk Services: Service state chart | Azure" metaKeywords="" description="" metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Service state chart" authors="mandia" solutions="integration" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia"></tags>

# BizTalk Services : des états BizTalk Services

Selon l'état actuel du service BizTalk, vous pouvez effectuer ou non certaines opérations sur celui-ci.

Par exemple, vous approvisionnez un nouveau service BizTalk dans le portail de gestion Azure. Lorsqu'il se termine correctement, le service BizTalk possède l'état Actif. Dans cet état, vous pouvez arrêter le service BizTalk. Si l'arrêt s'effectue correctement, le service BizTalk passe à l'état Arrêté. Si l'arrêt échoue, le service BizTalk passe à l'état StopFailed. Dans cet état, vous pouvez redémarrer le service BizTalk. Si vous tentez une opération non autorisée, telle que la reprise du service BizTalk, l'erreur suivante se produit :

**Opération non autorisée**

Pour plus d'informations sur l'approvisionnement d'un service BizTalk, consultez la rubrique [BizTalk Services : Approvisionnement à l'aide du portail de gestion Azure][].

Les tableaux ci-dessous répertorient les opérations qui peuvent être effectuées selon l'état du service BizTalk. Une coche signifie que l'opération peut être effectuée dans cet état. Une case vide signifie que l'opération ne peut pas être effectuée dans cet état.

#### Opérations Démarrer, Arrêter, Redémarrer, Interrompre, Reprendre et Supprimer

<table border="1">
<tr>
<th colspan="15">
Opération

</th>
</tr>
<tr>
<th rowspan="18">
État du service BizTalk

</th>
</tr>
<tr bgcolor="FAF9F9">
<th>
</th>
<th>
Démarrer

</th>
<th>
Arrêter

</th>
<th>
Redémarrer

</th>
<th>
Interrompre

</th>
<th>
Reprendre

</th>
<th>
Supprimer

</th>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>Active</b>

</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>Désactivé</b>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>Interrompu</b>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>Arrêté</b>

</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>Échec de mise à jour du service</b>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>DisableFailed</b>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>EnableFailed</b>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>StartFailed<br/>
 StopFailed<br/>
 RestartFailed</b>

</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>SuspendedFailed<br/>
 ResumeFailed</b>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>CreatedFailed<br/>
 RestoreFailed
</b>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>ConfigUpdateFailed</b>

</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>ScaleFailed</b>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
</table>
<br/>

#### Opérations Mettre à l'échelle, Mettre à jour la configuration et Sauvegarder

<table border="1">
<tr>
<th colspan="15">
Opération

</th>
</tr>
<tr>
<th rowspan="18">
État du service BizTalk

</th>
</tr>
<tr bgcolor="FAF9F9">
<th>
</th>
<th>
Mise à l’échelle

</th>
<th>
Mettre à jour la configuration

</th>
<th>
Sauvegarder

</th>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>Active</b>

</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>Désactivé</b>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>Interrompu</b>

</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>Arrêté</b>

</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>Échec de mise à jour du service</b>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>DisableFailed</b>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>EnableFailed</b>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>StartFailed<br/>
 StopFailed<br/>
 RestartFailed</b>

</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>SuspendedFailed<br/>
 ResumeFailed</b>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>CreatedFailed<br/>
 RestoreFailed
<b/>

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>ConfigUpdateFailed</b>

</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
<b>ScaleFailed</b>

</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
</tr>
</table>
## Voir aussi

-   [BizTalk Services : Approvisionnement à l'aide du portail de gestion Azure][]
-   [BizTalk Services : Tableau de bord, Surveiller et Mettre à l'échelle dans BizTalk Services][]
-   [BizTalk Services : Tableau comparatif des éditions Développeur, De base, Standard, et Premium de BizTalk Services][]
-   [BizTalk Services : Sauvegarde et restauration][]
-   [BizTalk Services : Limitation][]
-   [BizTalk Services : Nom et clé de l'émetteur][]
-   [Utilisation du Kit de développement logiciel (SDK) Azure BizTalk Services][]

  [BizTalk Services : Approvisionnement à l'aide du portail de gestion Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [BizTalk Services : Tableau de bord, Surveiller et Mettre à l'échelle dans BizTalk Services]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [BizTalk Services : Tableau comparatif des éditions Développeur, De base, Standard, et Premium de BizTalk Services]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [BizTalk Services : Sauvegarde et restauration]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [BizTalk Services : Limitation]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [BizTalk Services : Nom et clé de l'émetteur]: http://go.microsoft.com/fwlink/p/?LinkID=303941
  [Utilisation du Kit de développement logiciel (SDK) Azure BizTalk Services]: http://go.microsoft.com/fwlink/p/?LinkID=302335
