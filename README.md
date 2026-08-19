![](https://raw.githubusercontent.com/appsmithorg/appsmith/release/static/appsmith_logo_primary.png)

This app is built using Appsmith. Turn any datasource into an internal app in minutes. Appsmith lets you drag-and-drop components to build dashboards, write logic with JavaScript objects and connect to any API, database or GraphQL source.

![](https://raw.githubusercontent.com/appsmithorg/appsmith/release/static/images/integrations.png)

### [Github](https://github.com/appsmithorg/appsmith) • [Docs](https://docs.appsmith.com/?utm_source=github&utm_medium=social&utm_content=appsmith_docs&utm_campaign=null&utm_term=appsmith_docs) • [Community](https://community.appsmith.com/) • [Tutorials](https://github.com/appsmithorg/appsmith/tree/update/readme#tutorials) • [Youtube](https://www.youtube.com/appsmith) • [Discord](https://discord.gg/rBTTVJp)

##### You can visit the application using the below link

###### [![](https://assets.appsmith.com/git-sync/Buttons.svg) ](https://mkt-admin-app.production.heyjobs.de/applications/6a61dba4885aa57bcbe583a0/pages/6a61dba4885aa57bcbe583a3) [![](https://assets.appsmith.com/git-sync/Buttons2.svg)](https://mkt-admin-app.production.heyjobs.de/applications/6a61dba4885aa57bcbe583a0/pages/6a61dba4885aa57bcbe583a3/edit)

---

## Queries, die in Appsmith von Hand angelegt werden

Diese beiden Queries werden bewusst NICHT über Git synchronisiert: sie wurden direkt in
Appsmith erstellt, und ein zusätzlicher Ordner hier würde beim Pull einen Add/Add-Konflikt
gegen die Appsmith-eigene Version erzeugen. Der SOQL steht hier als Referenz.

Beide auf der Datasource **SFDC2 - API**, Command *Custom Action*, Methode *GET*,
„Encode query params" an, **Run on page load: an**. Der Pfad ist jeweils
`/query?q={{encodeURIComponent("<SOQL>")}}`.

### OldOffering  (~520 Zeilen)

Gegenstück zu `NewOffering`: gewonnene Opportunities seit dem Rollout (01.05.2026) auf allen
Produkten, die KEIN Neues-Offering-Line-Item haben. Aggregiert je CloseDate x GTM-Motion x
Purchase Type, damit die Zeilenzahl nicht mit dem Dealvolumen wächst. Agency und Owner ohne
Motion sind bereits im SOQL ausgeschlossen. Model-Key: `oldOffering`.

```sql
SELECT CloseDate d, Owner.GTM_Motion_User__c m, Purchase_Type__c pt, COUNT(Id) c, SUM(Amount) amt, COUNT_DISTINCT(AccountId) a FROM Opportunity WHERE IsWon = true AND CloseDate >= 2026-05-01 AND Owner.GTM_Motion_User__c != null AND Owner.GTM_Motion_User__c != 'Agency Sales' AND Id NOT IN (SELECT OpportunityId FROM OpportunityLineItem WHERE Product2.Name IN ('BASIC Listing','HIRE Budget - Neue Preise')) GROUP BY CloseDate, Owner.GTM_Motion_User__c, Purchase_Type__c
```

### PilotDetail  (~100 Zeilen)

Gewonnene New-Business-Pilots des laufenden Quartals mit NCO-Flag — Datenbasis für den
Drill-down unter der Kachel „Pilot-Anteil · neues Offering". Ob ein Deal ein neues
Produkt-Line-Item hat, ermittelt das Widget aus dem `newOffering`-Modell, dafür braucht es
keine eigene Spalte. Model-Key: `pilotDetail`.

```sql
SELECT Id, Account.Name, Account.New_Commercial_Offering__c, Owner.Name, Owner.GTM_Motion_User__c, Amount, CloseDate, Product_Type__c, HeyPaket_Type__c, Purchase_Type__c FROM Opportunity WHERE IsWon = true AND Purchase_Type__c IN ('Pilot','Re-win Pilot') AND Owner.GTM_Motion_User__c IN ('Inside Sales','Subscription New Business','Subscription Prospecting') AND CloseDate = THIS_QUARTER ORDER BY Amount DESC
```
