# Modèle Conceptuel de Données DiaLog - regulation only

```mermaid

classDiagram

class Organization {
  uuid*: uuid
  siret: char[14]
  name: varchar[255]
}

class Establishment {
  uuid*: uuid
  organization_uuid: uuid
  address: varchar[255]
  zipCode: varchar[6]
  city: varchar[100]
  addressComplement?: varchar[100]
}

class MailingList {
  uuid*: uuid
  organization_uuid: uuid
  name: varchar[100]
  email: varchar
  role: varchar[150]
}

class SigningAuthority {
  uuid*: uuid
  signatoryName: varchar[100]
  name: varchar[100]
  role: varchar[100]
  organization_uuid?: uuid
}

class RegulationOrder {
  uuid*: uuid
  category: varchar[50]
  other_category_text: varchar[100]
  description: text
  issuing_authority: varchar[255]
  start_date?: date
  end_date?: date
}

class RegulationOrderRecord {
  uuid*: uuid
  regulation_order_uuid: uuid
  created_at: datetime
  status: enum["draft", "published"]
  organization_uuid: uuid
}

class RegulationOrderTemplate {
  uuid*: uuid
  organization_uuid?: uuid
  name: varchar[150]
  title: varchar[150]
  visaContent: text
  consideringContent: text
  articleContent: text
  createdAt: timez
}

class StorageRegulationOrder {
  uuid*: uuid
  regulation_order_uuid: uuid
  path?: varchar[255]
  url?: varchar[255]
  title: varchar[255]
}

class Measure {
  uuid*: uuid
  type: enum["noEntry", "speedLimitation"]
  regulation_order_uuid: uuid
  vehicle_set_uuid?: uuid
}

class VehicleSet {
  uuid*: uuid
  restricted_types: array
  other_restricted_type_text?: varchar[100]
  exempted_types: array
  other_exempted_type_text?: varchar[100]
}

class Period {
  uuid*: uuid
  recurrenceType: enum["everyDay","certainDays"]
  start_datetime: datetimez
  end_datetime?: datetimez
  measure_uuid: uuid
  relation_type: enum["valid","exception"]
}

class DailyRange {
  uuid*: uuid
  days: ARRAY[enum["monday",...,"sunday"]]
  period_uuid: uuid
}

class TimeSlot {
  uuid*: uuid
  start_time: timez
  end_time: timez
  period_uuid: uuid
}

class Location {
  uuid*: uuid
  road_type: enum["lane","departmentalRoad","nationalRoad","rawGeoJSON"]
  geometry?: geometry
  measure_uuid: uuid
}

class NamedStreet {
  uuid*: uuid
  direction: enum["BOTH","A_TO_B","B_TO_A"]
  city_code: varchar[5]
  city_label: varchar[255]
  road_ban_id: varchar[20]
  road_name: varchar[255]
  from_house_number: varchar[8]
  from_road_ban_id: varchar[20]
  from_road_name: varchar[255]
  to_house_number: varchar[8]
  to_road_ban_id: varchar[20]
  to_road_name: varchar[255]
  location_uuid: uuid
}

class NumberedRoad {
  uuid*: uuid
  administrator: varchar[255]
  road_number: varchar[50]
  from_side: enum["U","G","D"]
  from_abscissa: varchar[5]
  from_point_number: integer
  to_side: enum["U","G","D"]
  to_abscissa: varchar[5]
  to_point_number: integer
  location_uuid: uuid
}

class RawGeoJSON {
  uuid*: uuid
  geometry: geometry
  label: text
  location_uuid: uuid
}

class StorageArea {
  uuid*: uuid
  sourceId: varchar[64]
  description: varchar[255]
  administrator: varchar[64]
  roadNumber: varchar[16]
  fromPointNumber: varchar[5]
  fromSide: varchar[1]
  fromAbscissa: integer
  toPointNumber: varchar[5]
  toSide: varchar[1]
  toAbscissa: integer
  geometry: geometry
  location_uuid: uuid
}

class City {
  insee_code*: varchar[5]
  name: varchar[255]
  departement: varchar[3]
}

%% Relationships
Organization "0..1" -- "1..1" Establishment : "peut avoir"
Organization "1..1" -- "0..N" MailingList : "possède"
SigningAuthority "1..1" -- "0..1" Organization : "est lié à"
Organization "0..N" -- "1..1" RegulationOrderRecord : "peut créer"
RegulationOrderRecord "0..1" -- "1..1" RegulationOrder : "contient"
RegulationOrder "1..N" -- "1..1" Measure : "définit"
RegulationOrder "0..1" -- "1..1" StorageRegulationOrder : "peut avoir"
Organization "0..N" -- "0..1" RegulationOrderTemplate : "peut avoir"
Measure "1..N" -- "1..1" Location : "s'applique à"
Location "0..1" -- "1..1" NamedStreet : "peut être"
Location "0..1" -- "1..1" NumberedRoad : "peut être"
Location "0..1" -- "1..1" RawGeoJSON : "peut être"
Location "0..1" -- "1..1" StorageArea : "peut être"
Measure "0..1" -- "1..1" VehicleSet : "peut restreindre"
Measure "0..N" -- "1..1" Period : "exception_period"
Measure "0..N" -- "1..1" Period : "valid_period"
Period "0..1" -- "1..1" DailyRange : "peut avoir"
Period "0..N" -- "1..1" TimeSlot : "peut avoir"
