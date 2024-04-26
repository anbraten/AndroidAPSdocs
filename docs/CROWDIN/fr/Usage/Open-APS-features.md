# Fonctions OpenAPS

(Open-APS-features-autosens)=

## Autosens

* Autosens est un algorithme qui examine les écarts de glycémie (positives/négatives/neutres).
* Il va essayer de déterminer à quel point vous êtes sensible/résistant en fonction de ces écarts.
* L'implémentation oref dans **OpenAPS** utilise une combinaison de 24 et 8 heures de données. Il utilise celui qui le est plus sensible.
* Dans les versions antérieures à AAPS 2.7, l'utilisateur devait choisir manuellement entre 8 heures ou 24 heures.
* A partir de la version 2.7 d'AAPS, l'Autosens basculera entre une fenêtre de 24 heures et 8 heures pour calculer la sensibilité. Il choisira celle qui est le plus sensible. 
* Les utilisateurs qui utilisaient oref1 remarqueront probablement que le système peut être moins dynamique en raison de la variation de sensibilité entre 24 heures et 8 heures.
* Changer une canule ou modifier un profil réinitialisera l'autosens à 100% (un changement de profil avec durée ne réinitialisera pas l'autosens).
* Autosens ajuste votre basal et votre SI (c.-à-d. qu'il imite ce que fait un changement de profil).
* Si vous mangez continuellement des glucides sur une période prolongée, l'Autosens sera moins efficace pendant cette période car les glucides sont exclus les calculs des écarts de glycémie.

(Open-APS-features-super-micro-bolus-smb)=

## Super Micro Bolus (SMB)

SMB, la version courte de 'Super Micro Bolus', est la dernière fonctionnalité de OpenAPS (depuis 2018) inclue dans l'algorithme Oref1. Contrairement à OpenAPS AMA, le SMB n'utilise pas les débits de base temporaires pour contrôler la glycémie, mais surtout les **microbolus de toute petite taille**. dans les cas où AMA ajouterait 1.0 UI d'insuline à l'aide d'un débit de base temporaire, SMB délivre plusieurs Super Micro Bolus en petites étapes à **5 minutes d'intervalle**, par ex. 0.4 UI, 0.3 UI, 0.2 UI and 0.1 UI. Dans le même temps (pour des raisons de sécurité) le véritable taux basal est mis à 0 UI/h pour une certaine durée afin d'éviter un surdosage (**'zéro-temp'**). Cela permet au système d'ajuster la glycémie plus rapidement qu'avec l'augmentation du débit de base temporaire de l'AMA.

Thanks to SMB, it may be sufficient for meals containing only "slow" carbs to inform the system of the planned amount of carbohydrate and leave the rest to AAPS. However, this may lead to higher postprandial (post-meal) peaks because pre-bolusing isn’t possible. Or you can give, if necessary with pre-bolusing, a **start bolus**, which **only partly** covers the carbohydrates (e.g. 2/3 of the estimated amount) and let SMB provide the rest.

La fonctionnalité SMB contient des mécanismes de sécurité:

1. La plus grande dose de SMB ne peut être que la plus petite valeur entre :
    
    * la valeur correspondant au débit de base actuel (ajusté par autosens) pour la durée définie dans "Max minutes de base pour limiter le SMB", par ex. la quantité de basale pour les 30 prochaines minutes, ou
    * la moitié de la quantité d'insuline actuellement requise, ou
    * la partie restante de votre maxIA renseignée dans les paramètres.

2. Vous remarquerez probablement souvent de faibles débits de base temporaires (appelées 'faibles temp') ou des DBT à 0 U/h (appélés 'zéro-temp'). This is by design for safety reasons and has no negative effects if the profile is set correctly. La courbe d'IA est plus significative que les débits de base temporaires.

3. Des calculs supplémentaires sont effectués pour prédire l'évolution de la glycémie, par ex. RNS (ou Repas Non Signalés). Même si aucun glucide n'est renseigné par l'utilisateur, RNS peut détecter automatiquement une augmentation significative des niveaux de glycémie liés à des repas, l'adrénaline ou d'autres facteurs et essaiera de les ajuster avec des SMB. Pour être en sécurité, cela marche aussi dans l'autre sens et peut arrêter les SMB plus tôt si une chute rapide inattendue de la glycémie survient. C'est pourquoi RNS doit toujours être activé avec les SMB.

**Vous devez avoir commencé l'[Objectif 9](Objectives-objective-9-enabling-additional-oref1-features-for-daytime-use-such-as-super-micro-bolus-smb) pour utiliser les SMB.**

Voir aussi : [Documentation OpenAPS pour oref1 SMB](https://openaps.readthedocs.io/en/latest/docs/Customize-Iterate/oref1.html) et [les infos de Tim sur les SMB](https://www.diabettech.com/artificial-pancreas/understanding-smb-and-oref1/).

(Open-APS-features-max-u-h-a-temp-basal-can-be-set-to-openaps-max-basal)=

### Max. U/h pour le débit temp Basal (OpenAPS "max-basal")

Ce paramètre de sécurité détermine le débit de base temporaire maximal que la pompe à insuline peut délivrer. La valeur doit être la même dans la pompe et dans les AAPS et doit être au moins égale à 3 fois le débit de base le plus élevé.

Par exemple :

Le débit de base le plus élevé de votre profil au cours de la journée est de 1,00 U/h. Alors la valeur de max-basal recommandée est d'au moins 3 U/h.

AAPS limits the value as a 'hard limit' according to the patients age you have selected under settings.

AAPS limits the value as follows:

* Enfant : 2
* Adolescent : 5
* Adulte : 10
* Adulte résistant à l'insuline : 12
* Grossesse : 25

*Voir aussi [l'aperçu des limites codées en dur](Open-APS-features-overview-of-hard-coded-limits).*

(Open-APS-features-maximum-total-iob-openaps-cant-go-over-openaps-max-iob)=

### IA totale maximale pour OpenAPS \[U\] (OpenAPS "max-IA")

This value determines the maxIOB that AAPS will remain under while running in closed loop mode. Si l'IA en cours (par exemple après un bolus de repas) est supérieure à la valeur définie, la boucle arrêtera d'administrer de l'insuline jusqu'à ce que la l'IA soit inférieure à la valeur limite renseignée.

En utilisant OpenAPS SMB, maxIA est calculé différemment de OpenAPS AMA. Dans AMA, maxIA était juste un paramètre de sécurité pour l'IA de la basal, alors qu'en mode SMB, il inclut également l'IA des bolus. Un bon départ est

    maxIA = moyenne bolus repas + 3 x max basal quotidien
    

Soyez prudent et patient et modifiez les paramètres petit à petit. It is different for everyone and can also depend on the average total daily dose (TDD). Pour des raisons de sécurité, il y a une limite, qui dépend de l'âge du patient. La 'limite en dur' pour maxIA est supérieure à la limite [AMA](Open-APS-features-max-u-hr-a-temp-basal-can-be-set-to-openaps-max-basal).

* Enfant : 3
* Adolescent : 7
* Adulte : 12
* Adulte résistant à l'insuline : 25
* Grossesse : 40

*Voir aussi [l'aperçu des limites codées en dur](Open-APS-features-overview-of-hard-coded-limits).*

Voir aussi la [documentation OpenAPS pour SMB](https://openaps.readthedocs.io/en/latest/docs/Customize-Iterate/oref1.html#understanding-super-micro-bolus-smb).

### Activer AMA Autosens

Ici, vous pouvez choisir si vous voulez utiliser la [détection de sensibilité](../Configuration/Sensitivity-detection-and-COB.md) 'autosens' ou non.

(Open-APS-features-enable-smb)=

### Activer SMB

Enable this to use SMB functionality. If disabled, no SMBs will be given.

### Enable SMB with high temp targets

If this setting is enabled, SMB will be allowed, but not necessarily enabled, when there is a high temporary target active (defined as anything above 100mg/dl regardless of profile target). This option is intended to be used to disable SMBs when the setting is disabled. For example, if this option is disabled, SMBs can be disabled by setting a temp target above 100mg/dl. This option will also disable SMB regardless of what other condition is trying to enable SMB.

If this setting is enabled, SMB will only be enabled with a high temp target if Enable SMB with temp targets is also enabled.

(Open-APS-features-enable-smb-always)=

### Enable SMB always

If this setting is enabled, SMB is enabled always (independent of COB, temp targets or boluses). If this setting is enabled, the rest of the enable settings below will have no effect. However, if “Enable SMB with high temp targets” is disabled and a high temp target is set SMBs will be disabled. For safety reasons, this option is only available for BG sources with a good filtering system for noisy data. Currently it is only an available option with a Dexcom G5 or G6, if using the ['Build your own Dexcom App'](DexcomG6-if-using-g6-with-build-your-own-dexcom-app) or “native mode” in xDrip+. If a BG value has too large of a deviation, the G5/G6 doesn’t send it and waits for the next value in 5 minutes.

For other CGM/FGM like Freestyle Libre, ‘SMB always’ is deactivated until xDrip+ has a better noise smoothing plugin. You can find more [here](../Usage/Smoothing-Blood-Glucose-Data-in-xDrip.md).

### Enable SMB with COB

If this setting is enabled, SMB is enabled when the COB is greater than 0.

### Enable SMB with temp targets

If this setting is enabled, SMB is enabled when there is any temp target set (eating soon, activity, hypo, custom). If this setting is enabled but "Enable SMB with high temp targets" is disabled, SMB will be enabled when a low temp target is set (below 100mg/dl) but disabled when a high temp target is set.

### Activer SMB après ingestion de glucides

If enabled, SMB is enabled for 6h after carbohydrates are announced, even if COB has reached 0. For safety reasons, this option is only available for BG sources with a nice filtering system for noisy data. Currently it is only an available option with a Dexcom G5 or G6 if using the ['Build your own Dexcom App'](DexcomG6-if-using-g6-with-build-your-own-dexcom-app) or “native mode” in xDrip+. If a BG value has too large of a deviation, the G5/G6 doesn’t send it and waits for the next value in 5 minutes.

For other CGM/FGM like Freestyle Libre, 'Enable SMB after carbs' is deactivated until xDrip+ has a better noise smoothing plugin. You can find [more information here](../Usage/Smoothing-Blood-Glucose-Data-in-xDrip.md).

### How frequently SMBs will be given in min

This feature limits the frequency of SMBs. This value determines the minimum time between SMBs. Note that the loop runs every time a glucose value comes in (generally 5 minutes). Subtract 2 minute to give loop additional time to complete. E.g if you want SMB to be given every loop run, set this to 3 minutes.

Default value: 3 min.

(Open-APS-features-max-minutes-of-basal-to-limit-smb-to)=

### Max. minutes de basal pour limiter le SMB

This is an important safety setting. This value determines how much SMB can be given based on the amount of basal insulin in a given time, when it is covered by COBs.

Making this value larger allows the SMB to be more aggressive. You should start with the default value of 30 minutes. After some experience, increase the value in 15 minutes increments and observe the effects over multiple meals.

It is recommended not to set the value higher than 90 minutes, as this would lead to a point where the algorithm might not be able to accommodate a decreasing BG with 0 U/h basal ('zero-temp'). You should also set alarms, especially if you are still testing new settings, which will warn you well before a hypo.

Default value: 30 min.

### Activer RNS

With this option enabled, the SMB algorithm can recognize unannounced meals. This is helpful if you forget to tell AAPS about your carbs or estimate your carbs wrong and the amount of entered carbs is wrong or if a meal with lots of fat and protein has a longer duration than expected. Without any carb entry, UAM can recognize fast glucose increasements caused by carbs, adrenaline, etc., and tries to adjust it with SMBs. This also works the opposite way: if there is a fast glucose decrease, it can stop SMBs earlier.

**Therefore, UAM should always be activated when using SMB.**

### Sensitivity raises target

If this option is enabled, the sensitivity detection (autosens) can raise the target when sensitivity is detected (below 100%). In this case your target will be raised by the percentage of the detected sensitivity.

### Resistance lowers target

If this option is enabled, the sensitivity detection (autosens) can lower the target when resistance is detected (above 100%). In this case your target will be lowered by the percentage of the detected resistance.

### Cible temp. haute élève la sensibilité

If you have this option enabled, the insulin sensitivity will be increased while having a temporary target above 100 mg/dl or 5.6 mmol/l. This means, the ISF will rise while IC and basal will decrease. This will effectively make AAPS less aggressive when you set a high temp target.

### Cible temp. basse abaisse la sensibilité

If you have this option enabled, the insulin sensitivity will be decreased while having a temporary target lower than 100 mg/dl or 5.6 mmol/l. This means, the ISF will decrease while IC and basal will rise. This will effectively make AAPS more aggressive when you set a low temp target.

### Paramètres Avancés

**Always use short average delta instead of simple data** If you enable this feature, AAPS uses the short average delta/blood glucose from the last 15 minutes, which is usually the average of the last three values. This helps AAPS to be steadier with noisy data sources like xDrip+ and Libre.

**Max daily safety multiplier** This is an important safety limit. The default setting (which is unlikely to need adjusting) is 3. This means that AAPS will never be allowed to set a temporary basal rate that is more than 3x the highest hourly basal rate programmed in a user’s pump and/or profile. Example: if your highest basal rate is 1.0 U/h and max daily safety multiplier is 3, then AAPS can set a maximum temporary basal rate of 3.0 U/h (= 3 x 1.0 U/h).

Default value: 3 (shouldn’t be changed unless you really need to and know what you are doing)

**Current Basal safety multiplier** This is another important safety limit. The default setting (which is also unlikely to need adjusting) is 4. This means that AAPS will never be allowed to set a temporary basal rate that is more than 4x the current hourly basal rate programmed in a user’s pump and/or profile.

Default value: 4 (shouldn’t be changed unless you really need to and know what you are doing)

* * *

(Open-APS-features-advanced-meal-assist-ama)=

## Assistance Améliorée Repas (AAR)

AMA, the short form of "advanced meal assist" is an OpenAPS feature from 2017 (oref0). OpenAPS Advanced Meal Assist (AMA) allows the system to high-temp more quickly after a meal bolus if you enter carbs reliably.

You can find more information in the [OpenAPS documentation](https://newer-docs.readthedocs.io/en/latest/docs/walkthrough/phase-4/advanced-features.html#advanced-meal-assist-or-ama).

(Open-APS-features-max-u-hr-a-temp-basal-can-be-set-to-openaps-max-basal)=

### Max. U/h pour le débit temp Basal (OpenAPS "max-basal")

This safety setting helps AAPS from ever being capable of giving a dangerously high basal rate and limits the temp basal rate to x U/h. It is advised to set this to something sensible. A good recommendation is to take the highest basal rate in your profile and multiply it by 4 and at least 3. For example, if the highest basal rate in your profile is 1.0 U/h you could multiply that by 4 to get a value of 4 U/h and set the 4 as your safety parameter.

You cannot chose any value: For safety reason, there is a 'hard limit', which depends on the patient age. The 'hard limit' for maxIOB is lower in AMA than in SMB. For children, the value is the lowest while for insulin resistant adults, it is the biggest.

The hardcoded parameters in AAPS are:

* Enfant : 2
* Adolescent : 5
* Adulte : 10
* Adulte résistant à l'insuline : 12
* Grossesse : 25

*Voir aussi [l'aperçu des limites codées en dur](Open-APS-features-overview-of-hard-coded-limits).*

### IA basale max que OpenAPS pourra délivrer \[U\] (OpenAPS "max-iob")

This parameter limits the maximum of basal IOB where AAPS still works. If the IOB is higher, it stops giving additional basal insulin until the basal IOB is under the limit.

The default value is 2, but you should be rise this parameter slowly to see how much it affects you and which value fits best. It is different for anyone and also depends on the average total daily dose (TDD). Pour des raisons de sécurité, il y a une limite, qui dépend de l'âge du patient. The 'hard limit' for maxIOB is lower in AMA than in SMB.

* Enfant : 3
* Adolescent : 5
* Adulte : 7
* Adulte résistant à l'insuline : 12
* Grossesse : 25

*Voir aussi [l'aperçu des limites codées en dur](Open-APS-features-overview-of-hard-coded-limits).*

### Activer AMA Autosens

Here, you can chose, if you want to use the [sensitivity detection](../Configuration/Sensitivity-detection-and-COB.md) autosens or not.

### Autosens ajuste aussi les cibles temp

If you have this option enabled, autosens can adjust targets (next to basal and ISF), too. This lets AAPS work more 'aggressive' or not. The actual target might be reached faster with this.

### Paramètres Avancés

**Always use short average delta instead of simple data** If you enable this feature, AAPS uses the short average delta/blood glucose from the last 15 minutes, which is usually the average of the last three values. This helps AAPS to work more steady with noisy data sources like xDrip+ and Libre.

**Max daily safety multiplier** This is an important safety limit. The default setting (which is unlikely to need adjusting) is 3. This means that AAPS will never be allowed to set a temporary basal rate that is more than 3x the highest hourly basal rate programmed in a user’s pump. Example: if your highest basal rate is 1.0 U/h and max daily safety multiplier is 3, then AAPS can set a maximum temporary basal rate of 3.0 U/h (= 3 x 1.0 U/h).

Default value: 3 (shouldn’t be changed unless you really need to and know, what you are doing)

**Current Basal safety multiplier** This is another important safety limit. The default setting (which is also unlikely to need adjusting) is 4. This means that AAPS will never be allowed to set a temporary basal rate that is more than 4x the current hourly basal rate programmed in a user’s pump.

Default value: 4 (shouldn’t be changed unless you really need to and know, what you are doing)

**Bolus snooze dia divisor** The feature “bolus snooze” works after a meal bolus. AAPS doesn’t set low temporary basal rates after a meal in the period of the DIA divided by the “bolus snooze”-parameter. The default value is 2. That means with a DIA of 5h, the “bolus snooze” would be 5h : 2 = 2.5h long.

Default value: 2

* * *

(Open-APS-features-overview-of-hard-coded-limits)=

## Overview of hard-coded limits

<table border="1">
  
<thead>
  <tr>
    <th width="200"></th>
    <th width="75"> Enfant</th>
    <th width="75">Adolescent</th>
    <th width="75">Adulte</th>
    <th width="75">Adulte résistant à l'insuline</th>
    <th width="75">Femme enceinte</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>BOLUS MAX</td>
    <td>5,0</td>
    <td>10,0</td>
    <td>17,0</td>
    <td>25,0</td>
    <td>60,0</td>
  </tr>
  <tr>
    <td>DAI MIN</td>
    <td>5,0</td>
    <td>5,0</td>
    <td>5,0</td>
    <td>5,0</td>
    <td>5,0</td>
  </tr>
  <tr>
    <td>DAI MAX</td>
    <td>9,0</td>
    <td>9,0</td>
    <td>9,0</td>
    <td>9,0</td>
    <td>10,0</td>
  </tr>
  <tr>
    <td>G/I MIN</td>
    <td>2,0</td>
    <td>2,0</td>
    <td>2,0</td>
    <td>2,0</td>
    <td>0,3</td>
  </tr>
  <tr>
    <td>G/I MAX</td>
    <td>100,0</td>
    <td>100,0</td>
    <td>100,0</td>
    <td>100,0</td>
    <td>100,0</td>
  </tr>
  <tr>
    <td>IA MAX AMA</td>
    <td>3,0</td>
    <td>5,0</td>
    <td>7,0</td>
    <td>12,0</td>
    <td>25,0</td>
  </tr>
  <tr>
    <td>IA MAX SMB</td>
    <td>7,0</td>
    <td>13,0</td>
    <td>22,0</td>
    <td>30,0</td>
    <td>70,0</td>
  </tr>
  <tr>
    <td>BASAL MAX</td>
    <td>2,0</td>
    <td>5,0</td>
    <td>10,0</td>
    <td>12,0</td>
    <td>25,0</td>
  </tr>
</tbody>
</table>