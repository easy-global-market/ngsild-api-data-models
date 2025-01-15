# Use of unit codes in NGSI-LD 

When exchanging measurement values, it is required to use homogeneous units. For that purpose, it is required for any data platform aiming at interoperability to specify the units together with the measure. The NGSI-LD specification [1] recommends the use of UN/CEFACT - Common codes [2]  which provides an extensive list of unit codes which are represented through a 3 characters code.

As an example, UN/CEFACT common code for *centimetre* is *CMT*.

A NGSI-LD data platform may consume data coming from other units model (i.e. senML [3]) and a mapping has to be produced. This mapping is not always complete and some agreements have to be made.

Finally, despite the extensive list of unit codes provided in [2], some other specialised units may be needed and a definition needs to be produced. 

These aspects are described hereafter.

## UN/CEFACT common codes

The list of UN/CEFACT codes is too long to be included in this document but an illustrative example is provided below. 

| **Property**                        | **CEFACT code** |
| :---------------------------------- | --------------- |
| ppm (parts per million)             | 59              |
| milligrams per litre                | M1              |
| meter                               | MTR             |
| temperature                         | CEL             |
| percent                             | P1              |
| milli siemens per cm (conductivity) | H61             |
| pH                                  | Q30             |
| Volt                                | VLT             |
| Gram per litre                      | GL              |
| Micro gram per litre                | H29             |

a NGSI-LD payload describing a 7.58°C temperature measurement & 20m depth seaweed containment would thus be described as:

```json
{
  "id": "urn:ngsi-ld:SeaWeedContainment:01",
  "type": "SeaWeedContainment",
  "depth": {
    "type": "Property",
    "value": 20,
    "unitCode": "MTR"
  },
  "createdAt": "2018-10-26T21:32:52+02:00",
  "temperature": {
    "type": "Property",
    "value": 7.58,
    "unitCode": "CEL",
    "observedAt": "2020-01-16T13:44:38.000Z",
    "observedBy": {
      "type": "Relationship",
      "object": "urn:ngsi-ld:Sensor:AQUABOXSXJ10Temperature"
    }
  }
}
```

Being useful for logistic related applications, the counting of elements is done by prefixing an 'X' to the code element provided in [UNECE recommendation 21](https://www.unece.org/#jfmulticontent_c66412-10) [https://www.unece.org/#jfmulticontent_c66412-10]. A *number of pipes* would then have unit *XPI*.

## Mapping from senML measurements

The [8428 IETF RTF specification](https://tools.ietf.org/html/rfc8428) for sensor measurement list (senML) [3] is designed so that processors with very limited capabilities could easily encode a sensor measurement into the media type, while at the same time, a server parsing the data could collect a large number of sensor measurements in a relatively efficient manner. It provides a limited list of units. It is limited in the sense that only main units are provided and not their multiple (i.e. it describes meter but not centimetre) and some properties (i.e. concentration of a chemical specie in a liquid) are inexistent. On the opposite, some dimensions existing in senML do not have counterpart in CEFACT, such as the relative power unit decibel watt (dBW). 

The table below describes the mapping produced by the EGM senML to NGSI-LD payloads converter.

| **senML symbol** | **Described property**                            | **Corresponding CEFACT code** |
| ---------------- | ------------------------------------------------- | ----------------------------- |
| m                | meter                                             | MTR                           |
| kg               | kilogram                                          | KGM                           |
| g                | gram                                              | GRM                           |
| s                | second                                            | SEC                           |
| A                | ampere                                            | AMP                           |
| K                | kelvin                                            | KEL                           |
| cd               | candela                                           | CDL                           |
| mol              | mole                                              | C34                           |
| Hz               | hertz                                             | HTZ                           |
| rad              | radian                                            | C81                           |
| sr               | steradian                                         | D27                           |
| N                | newton                                            | NEW                           |
| Pa               | pascal                                            | PAL                           |
| J                | joule                                             | JOU                           |
| W                | watt                                              | WTT                           |
| C                | coulomb                                           | COU                           |
| V                | volt                                              | VLT                           |
| F                | farad                                             | FAR                           |
| Ohm              | ohm                                               | OHM                           |
| S                | siemens                                           | SIE                           |
| Wb               | weber                                             | WEB                           |
| T                | tesla                                             | D33                           |
| H                | henry                                             | 81                            |
| Cel              | degrees Celsius                                   | CEL                           |
| lm               | lumen                                             | LUM                           |
| lx               | lux                                               | LUX                           |
| Bq               | becquerel                                         | BQL                           |
| Gy               | gray                                              | A95                           |
| Sv               | sievert                                           | D13                           |
| kat              | katal                                             | KAT                           |
| m2               | square meter (area)                               | MTK                           |
| m3               | cubic meter (volume)                              | MTQ                           |
| l                | litre (volume)*                                   | LTR                           |
| m/s              | meter per second (velocity)                       | MTS                           |
| m/s2             | meter per square second (acceleration)            | MSK                           |
| m3/s             | cubic meter per second (flow rate)                | MQS                           |
| l/s              | litre per second (flow rate)*                     | G51                           |
| W/m2             | watt per square meter (irradiance)                | D54                           |
| cd/m2            | candela per square meter (luminance)              | A24                           |
| bit              | bit (information content)                         | A99                           |
| bit/s            | bit per second (data rate)                        | B10                           |
| lat              | degrees latitude                                  | DD                            |
| lon              | degrees longitude                                 | DD                            |
| pH               | pH value (acidity; logarithmic quantity)          | Q30                           |
| dB               | decibel (logarithmic quantity)                    | 2N                            |
| dBW              | decibel relative to 1 W (power level)             |                               |
| Bspl             | bel (sound pressure level; logarithmic quantity)* | M72                           |
| count            | 1 (counter value)                                 | E50                           |
| %                | ratio                                             | P1                            |
| %RH              | percentage (relative humidity)                    | P1                            |
| %EL              | percentage (remaining battery energy level)       | P1                            |
| EL               | seconds (remaining battery energy level)          | SEC                           |
| 1/s              | 1 per second (event rate)                         | C97                           |
| 1/min            | 1 per minute (event rate, "rpm")                  | C94                           |
| beat/min         | 1 per minute (heart rate in beats per minute)     |                               |
| beats            | 1 (cumulative number of heart beats)              |                               |
| S/m              | siemens per meter (conductivity)                  | D10                           |

Some senML units are specific to an application (i.e. percentage of remaining battery) whereas in NGSI-LD they should be rather described with the base *percentage* unit. The measured quantity (*battery level*) being described in the measurement type property.

A senML battery level of a device expressed as:

```json
  [
     {"n":"urn:dev:ow:1234","u":"%EL","v":71}
  ]
```

would turn to be expressed in NGSI-LD as:

```json
{
  "id": "urn:ngsi-ld:dev:ow:1234",
  "type": "battery",
  "energy level": {
    "type": "Property",
    "value": 71,
    "unitCode": "P1"
  }
}
```

## Defining new unit codes

Some units, despite being widely used by professional, appear to be inexistent in the UN/CEFACT list. Measurements done in such unit system can not always be simply converted to the base system as being dependant of a measurement method. 

As an example, this is the case for the *Nephelometric Turbidity Unit (NTU)* which provides a measure of water clarity and is measured by light scattered at 90° from the incidence of a single beam emitting in the 400-680nm wavelengths region. There is no simple conversion to a standard unit such as mg/l as the conversion factor would depend on size and type of suspended particles. 

For these reasons, we suggest some additional unit codes to be used. 

This table will be updated upon new needs identified for units and possible evolution of UN/CEFACT specifications to whom we made the below suggestions:

| **Proposed Code** | **Name**                                  | **Description**                                              | **Symbol** | Status                                              |
| ----------------- | ----------------------------------------- | ------------------------------------------------------------ | ---------- | --------------------------------------------------- |
| NTU               | Nephelometric Turbidity Unit              | Turbidity level of a fluid                                   | NTU        | Included upon our request in UN-CEFACT Rev. 15 2020 |
| FNU               | Formazin Nephelometric Unit               | Turbidity level of a fluid                                   | FNU        | Included upon our request in UN-CEFACT Rev. 15 2020 |
| DBM               | Power level                               | decibel relative to 1 mW                                     | dBm        | Included upon our request in UN-CEFACT Rev. 15 2020 |
| DBW               | Power level                               | decibel relative to 1 W                                      | dBW        | Included upon our request in UN-CEFACT Rev. 15 2020 |
| RRC               | Pipe wall reaction rate coefficient       | Wall reaction rate coefficient for zero-order reactions that  apply  to  a  water  quality  decay analysis in water network pipes | mg/m²/day  |                                                     |
| PFD               | Photosynthetic photon flux density (PPFD) | Flux density of photosynthetic photons (having wavelength between 400nm and 700nm). Said differently: number of photosynthetically active photons that fall on a given surface each second | μmol/s/m²  |                                                     |
| PPF               | Photosynthetic photon flux (PPF)          | Flux of photosynthetic photons (having wavelength between 400nm and 700nm) | μmol/s     |                                                     |

## References

[1] « NGSI-LD API », [GS CIM 009](https://portal.etsi.org/webapp/workprogram/Report_WorkItem.asp?WKI_ID=68619), ([version 1.8.1 from March 2024](https://www.etsi.org/deliver/etsi_gs/CIM/001_099/009/01.08.01_60/gs_CIM009v010801p.pdf))

[2] « UN/CEFACT Common Codes for Units of Measurement used in the International Trade », https://www.unece.org/cefact/codesfortrade/codes_index.html

[3] « Sensor Measurement Lists (SenML) », https://www.iana.org/assignments/senml/senml.xhtml

[4] « UN/CE Rec 21 – Codes for Passengers, Types of Cargo, Packages and Packaging Materials (with Complementary Codes for Package Names) », [Rec 21 – Codes for Passengers, Types of Cargo, Packages and Packaging Materials (with Complementary Codes for Package Names)](https://www.unece.org/#jfmulticontent_c66412-10

[5] « Turbidity -- Units of Measurement », https://or.water.usgs.gov/grapher/fnu.html
