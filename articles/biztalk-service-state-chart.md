<properties linkid="manage-services-biztalk-state-chart" urlDisplayName="BizTalk Services: Service state chart" pageTitle="BizTalk Services: Service state chart | Azure" metaKeywords="" description="" metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Service state chart" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

Tableau comparatif des états BizTalk Services
=============================================

Selon l'état actuel du service BizTalk, vous pouvez effectuer ou non certaines opérations sur celui-ci.

Par exemple, vous approvisionnez un nouveau service BizTalk dans le portail de gestion Azure. Lorsqu'il se termine correctement, le service BizTalk possède l'état Actif. Dans cet état, vous pouvez arrêter le service BizTalk. Si l'arrêt s'effectue correctement, le service BizTalk passe à l'état Arrêté. Si l'arrêt échoue, le service BizTalk passe à l'état StopFailed. Dans cet état, vous pouvez redémarrer le service BizTalk. Si vous tentez une opération non autorisée, telle que la reprise du service BizTalk, l'erreur suivante se produit :

**Opération non autorisée**

Pour plus d'informations sur l'approvisionnement d'un service BizTalk, consultez la rubrique [BizTalk Services : Approvisionnement à l'aide du portail de gestion Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280).

Les tableaux ci-dessous répertorient les opérations qui peuvent être effectuées selon l'état du service BizTalk. Une coche signifie que l'opération peut être effectuée dans cet état. Une case vide signifie que l'opération ne peut pas être effectuée dans cet état.

#### Opérations Démarrer, Arrêter, Redémarrer, Interrompre, Reprendre et Supprimer

<table data-morhtml="true" border="1">
<tr data-morhtml="true">
        <th data-morhtml="true" colspan="15">Op&eacute;ration</th>
</tr>

<tr data-morhtml="true">
        <th data-morhtml="true" rowspan="18">&Eacute;tat du service BizTalk</th>
</tr>
<tr data-morhtml="true" bgcolor="FAF9F9">
        <th data-morhtml="true"> </th>
        <th data-morhtml="true">D&eacute;marrer</th>
        <th data-morhtml="true">Arr&ecirc;ter</th>
        <th data-morhtml="true">Red&eacute;marrer</th>
        <th data-morhtml="true">Interrompre</th>
        <th data-morhtml="true">Reprendre</th>
        <th data-morhtml="true">Supprimer</th>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Actif</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">D&eacute;sactiv&eacute;</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Interrompu</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Arr&ecirc;t&eacute;</b></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">&Eacute;chec de mise &agrave; jour du service</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">DisableFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">EnableFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">StartFailed<br data-morhtml="true" /> StopFailed<br data-morhtml="true" /> RestartFailed</b></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">SuspendedFailed<br data-morhtml="true" /> ResumeFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">CreatedFailed<br data-morhtml="true" /> RestoreFailed<br data-morhtml="true" /></b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">ConfigUpdateFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">ScaleFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
</table>

#### Opérations Mettre à l'échelle, Mettre à jour la configuration et Sauvegarder

<table data-morhtml="true" border="1">
<tr data-morhtml="true">
        <th data-morhtml="true" colspan="15">Op&eacute;ration</th>
</tr>

<tr data-morhtml="true">
        <th data-morhtml="true" rowspan="18">&Eacute;tat du service BizTalk</th>
</tr>
<tr data-morhtml="true" bgcolor="FAF9F9">
        <th data-morhtml="true"> </th>
        <th data-morhtml="true">Mettre &agrave; l'&eacute;chelle</th>
        <th data-morhtml="true">Mettre &agrave; jour la configuration</th>
        <th data-morhtml="true">Sauvegarder</th>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Actif</b></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">D&eacute;sactiv&eacute;</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Interrompu</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Arr&ecirc;t&eacute;</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">&Eacute;chec de mise &agrave; jour du service</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">DisableFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">EnableFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">StartFailed<br data-morhtml="true" /> StopFailed<br data-morhtml="true" /> RestartFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">SuspendedFailed<br data-morhtml="true" /> ResumeFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">CreatedFailed<br data-morhtml="true" /> RestoreFailed<br data-morhtml="true" /></b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">ConfigUpdateFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">ScaleFailed</b></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
</table>

Voir aussi
----------

-   [BizTalk Services : Approvisionnement à l'aide du portail de gestion Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
-   [Tableau de bord, Surveiller et Mettre à l'échelle dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302281)
-   [Tableau comparatif des éditions Développeur, De base, Standard, et Premium de BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [BizTalk Services : Sauvegarde et restauration](http://go.microsoft.com/fwlink/p/?LinkID=329873)
-   [BizTalk Services : Limitation](http://go.microsoft.com/fwlink/p/?LinkID=302282)
-   [BizTalk Services : Nom et clé de l'émetteur](http://go.microsoft.com/fwlink/p/?LinkID=303941)
-   [Utilisation du Kit de développement logiciel (SDK) Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)

