# TheSaiyans-MiniGuidesToModding

Creating your own Super Saiyan or power up form.



## Step 0 : Rimworld and Defs a very quick primer.
You can think of Defs in Rimworld as data stored in XML files, these often contain configuration information - information that does not change behaviour - such as you can set which SoundDef is played when the ability triggers but you can not change when that sound is played, that requires c#.

You can find all Definitions in the game within the `Data/Core/Defs/` or  `Data/DLC/Defs/`folder - note it does not matter where you place these files inside the Defs folder, you can place every single def of every single type into one file if you wished, or break them down into folders, all that matter is the XML is properly opened and closed and the XML is valid - that is there are no errors in the formatting or closing of tags and the like. Ludeon place Defs of the same type inside a folder following the def types name (eg AbilityDefs in `Data/Core/AbilityDefs/`), this is just for organisational sake but it is always worth taking note when the developers of the game you are modding follow a certain practice.


## Define the AbilityDef 

This is not a base game AbilityDef this is from the TaranMagicFramework and as such we need to specify our AbilityDef comes from that namespace we do that in the opening tag `<TaranMagicFramework.AbilityDef`.

Below is the XML that defines the Ability itself.

Most importantly if you are creating a 'power up' type form, then you will need to use the `SaiyanMod.KIAbility_SuperSaiyan` as the `<abilityClass>` this is what allows for the toggle behaviour and hair changes.


```xml
<!-- Main ability definition. ParentName inherits base settings from another ability -->
<TaranMagicFramework.AbilityDef ParentName="SR_SuperSaiyanKISkill">
    <defName>SR_SuperSaiyan2</defName>          <!-- Unique identifier for the ability -->
    <label>Super Saiyan 2</label>               <!-- Display name in UI -->
    <uiSkillIcon>UI/Icons/SuperSaiyan</uiSkillIcon>  <!-- Path to the icon texture -->
    <abilityClass>SaiyanMod.KIAbility_SuperSaiyan</abilityClass>  <!-- C# class that handles the ability behavior -->
    <abilityRank>SR_SuperSaiyan2</abilityRank>  <!-- Used for ability ranking/progression -->
    <hiddenUntilUnlocked>true</hiddenUntilUnlocked>  <!-- If true, ability is invisible until requirements are met -->
    
    <!-- List of tiers/levels for this ability -->
    <abilityTiers>
        <li Class="SaiyanMod.AbilityTierKIDef">SR_SuperSaiyan2_Tier1</li>
        <li Class="SaiyanMod.AbilityTierKIDef">SR_SuperSaiyan2_Tier2</li>
        <li Class="SaiyanMod.AbilityTierKIDef">SR_SuperSaiyan2_Tier3</li>
    </abilityTiers>
    
    <!-- Which ability trees can access this ability -->
    <abilityTrees>
        <li>SR_RegularSaiyanAbilityTree</li>
        <li>SR_HalfSaiyanAbilityTree</li>
        <li>SR_LegendarySaiyanAbilityTree</li>
    </abilityTrees>
    
    <!-- Abilities that will be deactivated when this one is used -->
    <endAbilitiesWhenActive>
        <li>SR_SuperSaiyan</li>
    </endAbilitiesWhenActive>
    
    <!-- Abilities that must be mastered to unlock this one -->
    <unlockedWhenMastered>
        <li>SR_SuperSaiyan</li>
    </unlockedWhenMastered>
</TaranMagicFramework.AbilityDef>
```


Secondly your ability will generally have 'tiers' to it, these are the levels you can spend your points on in any particular skill, you must also define these tiers and reference them in your `<TaranMagicFramework.AbilityDef>` 

Each ability tier allows you to override things like the energy regen rate, how long the cooldown is, and which hediff is applied by this form, allowing you to specify a different hediff for each tier, allowing a form to get stronger the higher your level with it is, or more cost efficient, or even a different form entirely. 


```xml
<!-- Base tier definition that other tiers will inherit from -->
<SaiyanMod.AbilityTierKIDef Name="SR_SuperSaiyan2_Tier">
    <defName>SR_SuperSaiyan2_Tier1</defName>      <!-- Unique identifier for this tier -->
    <label>Super Saiyan 2</label>                 <!-- Display name in UI -->
    <description>An ascended form beyond Super Saiyan...</description>  <!-- Tooltip description -->
    
    <!-- Visual effects - defines a glow around the character -->
    <glowProps>
        <glowColor>255, 230, 70</glowColor>      <!-- RGB color values -->
        <glowRadius>4</glowRadius>               <!-- How far the glow extends -->
    </glowProps>
    
    <!-- Visual overlay effects on the character -->
    <overlayProps>
        <overlay>SR_PowerUpOverlaySuperSaiyanII</overlay>  <!-- Texture to overlay -->
        <scale>1</scale>                                   <!-- Size of overlay -->
        <duration>900</duration>                           <!-- How long it displays -->
    </overlayProps>
    
    <!-- Experience gain settings while ability is active -->
    <xpGainWhileActive>
        <masteryGain>1.5</masteryGain>           <!-- XP multiplier -->
        <ticksInterval>2500</ticksInterval>       <!-- Ticks between XP gains -->
    </xpGainWhileActive>
    
    <!-- Status effect applied while active -->
    <hediffProps>
        <hediff>SR_SuperSaiyan2Hediff</hediff>
    </hediffProps>
    
    <isLearnable>false</isLearnable>              <!-- If true, can be learned naturally -->
    <soundOnCast>SR_SuperSaiyan</soundOnCast>     <!-- Sound effect when activated -->
    <energyCost>70</energyCost>                   <!-- Energy cost to activate -->
    <cooldownTicks>48000</cooldownTicks>          <!-- Cooldown before can be used again -->
    <regenRate>-0.06324</regenRate>              <!-- Energy regeneration modifier -->
    <iconTexPath>UI/Buttons/SuperSaiyan</iconTexPath>  <!-- Button icon path -->
</SaiyanMod.AbilityTierKIDef>
```
You may notice we have given this def tag the name `SaiyanMod.AbilityTierKIDef Name="SR_SuperSaiyan2_Tier">` which the later tiers will reference, you can use this to set any details that wont change between tiers on the base tier, making your later level tiers much less text to read and write, you can still override any property you need to in any future tiers.

As you can see the second level of Super Saiyan 2 has a shorter cooldown, uses less energy and applies a different hediff than tier 1, otherwise all details are identical to tier 1 if not explicity specified, agian yoou will note ` ParentName="SR_SuperSaiyan2_Tier"` which is us 'extending' the xml definition from the base tier.

```xml
<!-- Second tier inherits from base tier and overrides specific values -->
<SaiyanMod.AbilityTierKIDef ParentName="SR_SuperSaiyan2_Tier">
    <defName>SR_SuperSaiyan2_Tier2</defName>
    <label>Super Saiyan 2 Enhanced</label>
    <energyCost>55</energyCost>                   <!-- Lower energy cost than Tier 1 -->
    <cooldownTicks>24000</cooldownTicks>          <!-- Shorter cooldown than Tier 1 -->
    <regenRate>-0.04216</regenRate>              <!-- Better energy regen than Tier 1 -->
    <hediffProps>
        <hediff>SR_SuperSaiyan2IIHediff</hediff>  <!-- Different status effect than Tier 1 -->
    </hediffProps>
    
    <!-- Requirements to unlock this tier -->
    <acquireRequirement>
        <masteryPointsToUnlock>250</masteryPointsToUnlock>
    </acquireRequirement>
    
    <!-- Notification keys when tier is gained -->
    <letterTitleKeyGained>SR.PawnLeveledUpSuperSaiyan</letterTitleKeyGained>
    <letterDescKeysGained>SR.PawnLeveledUpSuperSaiyanDesc</letterDescKeysGained>
</SaiyanMod.AbilityTierKIDef>

```


And for completeness sake here is the final level of Super Saiyan 2, again you see it uses its own hediff `SR_SuperSaiyan2MasteredHediff` which applies a stronger version of the buff.

```xml
<!-- Third tier follows same pattern as second tier -->
<SaiyanMod.AbilityTierKIDef ParentName="SR_SuperSaiyan2_Tier">
    <defName>SR_SuperSaiyan2_Tier3</defName>
    <label>Super Saiyan 2 Mastered</label>
    <energyCost>40</energyCost>
    <cooldownTicks>12000</cooldownTicks>
    <regenRate>-0.02108</regenRate>
    <hediffProps>
        <hediff>SR_SuperSaiyan2MasteredHediff</hediff>
    </hediffProps>
    <acquireRequirement>
        <masteryPointsToUnlock>350</masteryPointsToUnlock>
    </acquireRequirement>
    <letterTitleKeyGained>SR.PawnMasteredSuperSaiyan</letterTitleKeyGained>
    <letterDescKeysGained>SR.PawnMasteredSuperSaiyanDesc</letterDescKeysGained>
</SaiyanMod.AbilityTierKIDef>
```


Now here is a complete listing of each hediff to give clear context as to what each tier is applying when learnt.

```xml
<HediffDef>
  <defName>SR_SuperSaiyan2Hediff</defName>
  <label>Super Saiyan 2</label>
  <description>A transformation into the ultimate Saiyan warrior.</description>
  <isBad>false</isBad>
  <stages>
    <li>
      <hungerRateFactor>1.60</hungerRateFactor>
      <restFallFactor>1.60</restFallFactor>
      <capMods>
        <li>
          <capacity>Manipulation</capacity>
          <offset>0.6</offset>
        </li>
        <li>
          <capacity>Consciousness</capacity>
          <offset>0.4</offset>
        </li>
        <li>
          <capacity>Moving</capacity>
          <offset>3.5</offset>
        </li>
      </capMods>
      <statFactors>
          <StaggerDurationFactor>0.4</StaggerDurationFactor>
          <IncomingDamageFactor>0.55</IncomingDamageFactor>
      </statFactors>
      <statOffsets>
          <PainShockThreshold>0.25</PainShockThreshold>
          <MeleeDodgeChance>0.15</MeleeDodgeChance>
          <MeleeHitChance>12</MeleeHitChance>
          <SR_KIDamageMultiplier>1.50</SR_KIDamageMultiplier>
      </statOffsets>
    </li>
  </stages>
</HediffDef>

<HediffDef>
  <defName>SR_SuperSaiyan2IIHediff</defName>
  <label>Super Saiyan II</label>
  <description>A transformation into the ultimate Saiyan warrior.</description>
  <isBad>false</isBad>
  <stages>
    <li>
      <hungerRateFactor>1.50</hungerRateFactor>
      <restFallFactor>1.50</restFallFactor>
      <capMods>
        <li>
          <capacity>Manipulation</capacity>
          <offset>0.6</offset>
        </li>
        <li>
          <capacity>Consciousness</capacity>
          <offset>0.4</offset>
        </li>
        <li>
          <capacity>Moving</capacity>
          <offset>3.5</offset>
        </li>
      </capMods>
      <statFactors>
          <StaggerDurationFactor>0.3</StaggerDurationFactor>
          <IncomingDamageFactor>0.55</IncomingDamageFactor>
      </statFactors>
      <statOffsets>
          <PainShockThreshold>0.25</PainShockThreshold>
          <MeleeDodgeChance>0.15</MeleeDodgeChance>
          <MeleeHitChance>12</MeleeHitChance>
          <SR_KIDamageMultiplier>1.60</SR_KIDamageMultiplier>
      </statOffsets>
    </li>
  </stages>
</HediffDef>

<HediffDef>
  <defName>SR_SuperSaiyan2IIIHediff</defName>
  <label>Super Saiyan 2</label>
  <description>A transformation into the ultimate Saiyan warrior.</description>
  <isBad>false</isBad>
  <stages>
    <li>
      <hungerRateFactor>1.5</hungerRateFactor>
      <restFallFactor>1.5</restFallFactor>
      <capMods>
        <li>
          <capacity>Manipulation</capacity>
          <offset>0.6</offset>
        </li>
        <li>
          <capacity>Consciousness</capacity>
          <offset>0.4</offset>
        </li>
        <li>
          <capacity>Moving</capacity>
          <offset>3.5</offset>
        </li>
      </capMods>
      <statFactors>
          <StaggerDurationFactor>0.25</StaggerDurationFactor>
          <IncomingDamageFactor>0.55</IncomingDamageFactor>
      </statFactors>
      <statOffsets>
          <PainShockThreshold>0.25</PainShockThreshold>
          <MeleeDodgeChance>0.15</MeleeDodgeChance>
          <MeleeHitChance>12</MeleeHitChance>
          <SR_KIDamageMultiplier>1.70</SR_KIDamageMultiplier>
      </statOffsets>
    </li>
  </stages>
</HediffDef>

<HediffDef>
  <defName>SR_SuperSaiyan2MasteredHediff</defName>
  <label>Super Saiyan 2 Mastered</label>
  <description>A transformation into the ultimate Saiyan warrior.</description>
  <isBad>false</isBad>
  <stages>
    <li>
      <hungerRateFactor>1.25</hungerRateFactor>
      <restFallFactor>1.25</restFallFactor>
      <capMods>
        <li>
          <capacity>Manipulation</capacity>
          <offset>1</offset>
        </li>
        <li>
          <capacity>Consciousness</capacity>
          <offset>0.4</offset>
        </li>
        <li>
          <capacity>Moving</capacity>
          <offset>4</offset>
        </li>
      </capMods>
      <statFactors>
          <StaggerDurationFactor>0.15</StaggerDurationFactor>
          <IncomingDamageFactor>0.45</IncomingDamageFactor>
      </statFactors>
      <statOffsets>
          <PainShockThreshold>0.25</PainShockThreshold>
          <MeleeDodgeChance>0.18</MeleeDodgeChance>
          <MeleeHitChance>12</MeleeHitChance>
          <SR_KIDamageMultiplier>2</SR_KIDamageMultiplier>
      </statOffsets>
    </li>
  </stages>
</HediffDef>
```

