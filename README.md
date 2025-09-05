# **README — Données de réglementation de voirie (Datex II)**

**Objectif**  

Ce schéma de données sert à représenter les **arrêtés de circulation** (réglementations de voirie) dans un format compatible avec le standard **Datex II**. Il permet de décrire :  
- **Qui** émet la réglementation (autorité, organisation)  
- **Quoi** est réglementé (type de mesure)  
- **Où** cela s’applique (localisation)  
- **Quand** cela s’applique (périodes, créneaux horaires)  
- **À qui** cela s’applique (véhicules restreints ou exemptés)  

Ces informations peuvent ensuite être exportées vers un fichier **Datex II (XML)** et utilisées par des systèmes de cartographie comme **ArcGIS**.

---

**Entités principales à remplir**

**1. `Organization` / `Establishment` / `SigningAuthority`**  
- Identifie l’autorité publique ou l’établissement qui publie l’arrêté.  
- Champs clés :  
  - `Organization.name` → nom de la collectivité (ex. *Mairie de Lyon*)  
  - `SigningAuthority.signatoryName` → nom du signataire officiel  

**2. `RegulationOrder`**  
- Représente un **arrêté de circulation**.  
- Champs clés :  
  - `uuid` → identifiant unique de l’arrêté  
  - `category` → catégorie de la réglementation (ex. `interdiction de circulation`)  
  - `description` → texte libre décrivant la mesure  
  - `issuing_authority` → autorité émettrice  
  - `start_date` / `end_date` → période de validité globale  

**3. `Measure`**  
- Décrit chaque **mesure concrète** contenue dans l’arrêté.  
- Champs clés :  
  - `type` → type de restriction (ex. `noEntry`, `speedLimitation`)  
  - `regulation_order_uuid` → lien vers l’arrêté parent  
  - `vehicle_set_uuid` → lien vers l’ensemble de véhicules concernés  

**4. `VehicleSet`**  
- Définit les **catégories de véhicules restreintes ou exemptées**.  
- Champs clés :  
  - `restricted_types` → liste des types interdits (ex. PL, VUL, motos…)  
  - `exempted_types` → liste des exceptions (ex. bus, secours…)  

**5. `Location` et sous-classes**  
- Définit **où** la mesure s’applique.  
- Options :  
  - `NamedStreet` → rue identifiée par nom/code INSEE  
  - `NumberedRoad` → route départementale/nationale  
  - `RawGeoJSON` → géométrie libre en GeoJSON  
  - `StorageArea` → zone de stationnement  
- Champs clés :  
  - `geometry` (si disponible) → polygone/ligne/point  
  - `city_code` / `city_label` → rattachement administratif  

**6. `Period` / `DailyRange` / `TimeSlot`**  
- Définissent **quand** la mesure s’applique.  
- Champs clés :  
  - `recurrenceType` → `everyDay` ou `certainDays`  
  - `DailyRange.days` → jours concernés (`monday`…`sunday`)  
  - `TimeSlot.start_time` / `end_time` → plages horaires  

---

**Exemple simplifié**

Cas : interdiction de circulation pour poids lourds > 3,5t dans une rue, de 22h à 6h.

- **Organization** : `Ville de Paris`  
- **RegulationOrder** : `Arrêté n°2025-01 — Interdiction PL nuit`  
- **Measure** : `type = noEntry`  
- **VehicleSet** : `restricted_types = [poidsLourd]`  
- **Location** : `NamedStreet` → *Rue de Rivoli*, Paris (`city_code = 75056`)  
- **Period** : `recurrenceType = everyDay`  
- **TimeSlot** : `start_time = 22:00`, `end_time = 06:00`

---

**Bonnes pratiques**

- Utiliser des `uuid` uniques pour chaque objet (`RegulationOrder`, `Measure`, etc.).  
- Remplir les dates et heures au format **ISO** (`YYYY-MM-DD`, `HH:MM:SS`).  
- Définir les valeurs d’énumérations (`enum`) exactement comme prévu (`noEntry`, `speedLimitation`, etc.).  
- Pour la géométrie, privilégier le format **GeoJSON** ou les champs ArcGIS natifs (`Shape`).  
- Toujours lier :  
  - `Measure` → `RegulationOrder`  
  - `Measure` → `Location`  
  - `Measure` → `VehicleSet` (si restriction de véhicules)  
  - `Measure` → `Period` (si restriction temporelle)  

---

**Export en Datex II**

Une fois les tables remplies :  

```xml
<RegulationOrder uuid="1234">
  <Measure type="noEntry">
    <VehicleSet restricted_types="poidsLourd"/>
    <Location>
      <NamedStreet city_code="75056" road_name="Rue de Rivoli"/>
    </Location>
    <Period recurrenceType="everyDay">
      <TimeSlot start_time="22:00" end_time="06:00"/>
    </Period>
  </Measure>
</RegulationOrder>
```
---
**Crédit et licence**

Ce travail est une simplification du projet [DiaLog](https://github.com/MTES-MCT/dialog), 
publié sous licence **GNU Affero General Public License v3.0 (AGPL-3.0)**.

L'intégralité de ce modèle conceptuel de données (MCD) provient de DiaLog.  
Ce dépôt propose une version adaptée aux besoins de représentation dans ArcGIS / Datex II.

Licence de ce dépôt : [AGPL-3.0](./LICENSE)
