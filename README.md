## Inicio de visita
### Información técnica
- Obtener ubicación: `GET /openmrs/ws/rest/v1/location`
- Obtener tipo de visita: `GET /openmrs/ws/rest/v1/visittype`
- Crear visita: `POST /openmrs/ws/rest/v1/visit`
    - body:
    s
    ```json
    {
    "visitType": "b1f0e8a1-9c5d-4f0e-8892-81f3140fbc09",
    "location": "35d2234e-129a-4c40-abb2-1ae0b2400001",
    "startDatetime": "2026-03-16T15:03:00.000Z",
    "stopDatetime": null,
    "patient": "6686b5b6-b8df-4596-90c3-d0321cc0e728"
    }
    ```   
    - Respuesta:
    ```json
    {
        "uuid":      "3a47fad7-c95b-4ea1-b19a-f9fffdd22879",  ← 🔑 ID de la visita
        "patient":   { "uuid": "6686b5b6-b8df-4596-90c3-d0321cc0e728" }, ← 🔑 ID del paciente
        "visitType": { "uuid": "b1f0e8a1-9c5d-4f0e-8892-81f3140fbc09" }, ← Tipo de consulta
        "location":  { "uuid": "35d2234e-129a-4c40-abb2-1ae0b2400001" }, ← Establecimiento
        "startDatetime": "2026-03-16T15:03:00.000+0000",                 ← Inicio
        "stopDatetime":  null,                                            ← Aún activa
        "encounters":    []                                               ← Se llenarán
    }
    ```
### Datos de prueba
- La consulta es: `En curso`
- Fecha de inicio:
    - Fecha: `automático` (fecha y hora actual)
    - Hora: `automático` (fecha y hora actual)
- Ubicación de la consulta: `UPSS - CONSULTA EXTERNA`
- Tipo de consulta: `Consulta ambulatoria`
- Payment details: `Non paying`

## Formularios clínicos: (Página 34, 35 y 36) TEPSI
### Información técnica:
- uuid del formulario: `1fe2f9e8-a9b1-34d2-94ce-b897ca2e16cc`
- petición: `GET /openmrs/ws/rest/v1/o3/forms/${uuid}`
- Envió de los datos: `POST /openmrs/ws/rest/v1/encounter`
    - Cuerpo:
    ```json
    {
        "patient":            "6686b5b6-...",     ← ID del paciente
        "location":           "35d2234e-...",     ← Ubicación
        "encounterType":      "a990eabc-...",     ← Tipo de encuentro
        "encounterProviders": [...],              ← Médico responsable
        "obs":                [...],             ← Observaciones del formulario
        "form":               { "uuid": "..." }, ← Formulario clínico
        "visit":              "3a47fad7-...",     ← Visita activa
        "orders":             [],                ← Órdenes (vacío aquí)
        "diagnoses":          []                 ← Diagnósticos (vacío aquí)
    }
    ```
    - Respuesta:
    ```json
    {
        "uuid":              "8f4299f0-5791-4d5c-b7c7-ca0146c07efb",  ← 🔑 encounter_uuid
        "encounterDatetime": "2026-03-16T15:50:51.000+0000",          ← Timestamp del servidor
        "encounterType": {
            "uuid": "a990eabc-...",
            "name": "Control de Niño Sano",                              ← Nombre legible confirmado
            "description": "Evaluaciones periódicas..."
        },
        "patient":  { "uuid": "6686b5b6-..." },
        "location": { "uuid": "35d2234e-..." },
        "orders":    [],
        "diagnoses": []
    }
    ```
### Datos de pruebas:
- Subtests Coordinación: `Traslada agua de un vaso a otro sin derramar (Dos vasos)`
- Reconoce grande y chico (Lam. 6): `Chico`
- Reconoce grande y chico (Lam. 7): `Menos`
- Nombra animales (Lam. 8): `Perro, Gato, Paloma`
- Responsable
    - Responsable atención: `admin - Super User` 
    - Rol del responsable: `Médico Responsable`

### Envío de datos:
- uuid del formulario: `1fe2f9e8-a9b1-34d2-94ce-b897ca2e16cc`
- petición: `POST /openmrs/ws/rest/v1/encounter`

## Nota de Visita

### Información técnica
- Nota de visita: `POST /openmrs/ws/rest/v1/encounter`
    - body
    ```json
    {
        "form":     "c75f120a-04ec-11e3-8780-2b40bef9a44b",
        "patient":  "6686b5b6-...",
        "location": "35d2234e-...",
        "encounterType": "d7151f82-c1f3-4152-a605-2f9ea7414a79",
        "encounterProviders": [
            {
            "encounterRole": "240b26f9-...",
            "provider":      "fc23b972-..."        ← distinto al de formularios anteriores
            }
        ],
        "obs": [
            {
            "concept": { "uuid": "162169AAAAAAAAAAAAAAAAAAAAAAAAAAAAAA" },
            "value":   "Paciente con diagnóstico de SIDA..."   ← texto libre
            }
        ]
    }
    ```
    - response
    ```json
            {
        "uuid": "c27c9e70-c8ce-4606-a63a-b513260bd92e",    ← 🔑 encounter_uuid de esta nota
        "display": "Notas de Atención 16/03/2026",
        "encounterDatetime": "2026-03-16T16:08:44.000+0000",

        "visit": {
            "uuid": "3a47fad7-..."                            ← 🔑 confirma la visita vinculada
        },

        "obs": [
            {
            "uuid": "775bf00a-d915-4211-afb2-74e6883ab7e4", ← 🔑 obs_uuid (para editar la nota)
            "display": "Texto de la nota de encuentro: ..."
            }
        ],

        "encounterProviders": [
            {
            "uuid": "0bba7a06-...",                          ← UUID del vínculo provider-encounter
            "display": "PEDRO MATIAS VARGAS AP2 : Médico Responsable"
            }
        ]
        }
    ```
- Diagnóstico principal: `POST /openmrs/ws/rest/v1/patientdiagnoses`
    - body:
    ```json
    {
        "encounter": "c27c9e70-c8ce-4606-a63a-b513260bd92e",
        "patient": "6686b5b6-b8df-4596-90c3-d0321cc0e728",
        "condition": null,
        "diagnosis": {
            "coded": "326345a0-c79e-45cf-9e7b-4f3ab3cc7d32"
        },
        "certainty": "PROVISIONAL",
        "rank": 1
    }
    ```
- Diagnóstico secundario: `POST /openmrs/ws/rest/v1/patientdiagnoses`
    - body:
    ```json
    {
        "encounter": "c27c9e70-c8ce-4606-a63a-b513260bd92e",
        "patient": "6686b5b6-b8df-4596-90c3-d0321cc0e728",
        "condition": null,
        "diagnosis": {
            "coded": "3c379474-2e22-4176-b9c0-92ebf5070712"
        },
        "certainty": "PROVISIONAL",
        "rank": 2
    }
    ```



### Datos de prueba
- Fecha: `automático` (fecha de la visita)
- Diagnóstico principal: `Enfermedades indicadoras de SIDA` 
- Diagnóstico secundario: `TRAUMATISMO DE NERVIOSA CERVICAL`
- Nota: `Paciente con diagnóstico de SIDA, presenta traumatismo de nerviosa cervical, se recomienda evaluación neurológica urgente y manejo adecuado del dolor.`


## Canasta de órdenes - Órdenes de medicaments:
### Información técnica
- Crear orden de medicamento: `POST /openmrs/ws/rest/v1/order`
    
    - body:
    ```json
    {
        "patient": "6686b5b6-b8df-4596-90c3-d0321cc0e728",
        "location": "35d2234e-129a-4c40-abb2-1ae0b2400001",
        "encounterType": "39da3525-afe4-45ff-8977-c53b7b359158",
        "encounterDatetime": "2026-03-13T16:30:05.000Z",
        "visit": "adb0e803-8c43-4eaa-b841-67e1625ecf09",
        "obs": [],
        "orders": [
            {
            "action": "NEW",
            "patient": "6686b5b6-b8df-4596-90c3-d0321cc0e728",
            "type": "drugorder",
            "careSetting": "6f0c9a92-6f24-11e3-af88-005056821db0",
            "orderer": "fc23b972-88bd-4b18-9de7-2249cc0ad3f5",
            "encounter": null,
            "drug": "fea1180e-c138-4d37-a5c0-4b964cb00808",
            "dose": 1,
            "doseUnits": "1bf39dc2-bf7e-40b8-931c-9b634b8db71c",
            "route": "bde472ba-b68c-49fb-a294-ea5ff387998e",
            "frequency": "136ebdb7-e989-47cf-8ec2-4e8b2ffe0ab3",
            "asNeeded": false,
            "asNeededCondition": "",
            "numRefills": 0,
            "quantity": 10,
            "quantityUnits": "1bf39dc2-bf7e-40b8-931c-9b634b8db71c",
            "duration": 1,
            "durationUnits": "1072AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA",
            "dosingType": "org.openmrs.SimpleDosingInstructions",
            "dosingInstructions": "",
            "concept": "de7e14ab-8a52-47b6-9a91-1df36b16322d",
            "orderReasonNonCoded": "Tomar una cápsula cada doce horas durante cinco días para tratar la infección."
            }
        ]
    }
    ```
    
    - response:
    ```json
    {
        "uuid": "368177ca-ad25-40ba-be1f-86d51e5b1d77",
        "display": "Ordenes Medicas 13/03/2026",
        "encounterDatetime": "2026-03-13T16:30:05.000+0000",
        "patient": {
            "uuid": "6686b5b6-b8df-4596-90c3-d0321cc0e728",
            "display": "70336320 - GONZALO ALONSO  GALVEZ CORTEZ  ",
            "links": [
            {
                "rel": "self",
                "uri": "http://hii1sc-dev.inf.pucp.edu.pe/openmrs/ws/rest/v1/patient/6686b5b6-b8df-4596-90c3-d0321cc0e728",
                "resourceAlias": "patient"
            }
            ]
        },
        "location": {
            "uuid": "35d2234e-129a-4c40-abb2-1ae0b2400001",
            "display": "UPSS - CONSULTA EXTERNA",
            "links": [
            {
                "rel": "self",
                "uri": "http://hii1sc-dev.inf.pucp.edu.pe/openmrs/ws/rest/v1/location/35d2234e-129a-4c40-abb2-1ae0b2400001",
                "resourceAlias": "location"
            }
            ]
        },
        "form": null,
        "encounterType": {
            "uuid": "39da3525-afe4-45ff-8977-c53b7b359158",
            "display": "Ordenes Medicas",
            "links": [
            {
                "rel": "self",
                "uri": "http://hii1sc-dev.inf.pucp.edu.pe/openmrs/ws/rest/v1/encountertype/39da3525-afe4-45ff-8977-c53b7b359158",
                "resourceAlias": "encountertype"
            }
            ]
        },
        "obs": [],
        "orders": [
            {
            "uuid": "4f046ae1-3a28-413b-98a2-f5dc8768153e",
            "display": "(NEW) 00808 - AMOXICILINA 500 mg: 1.0 Cápsula Vía Oral Una vez diario 1 Días",
            "links": [
                {
                "rel": "self",
                "uri": "http://hii1sc-dev.inf.pucp.edu.pe/openmrs/ws/rest/v1/order/4f046ae1-3a28-413b-98a2-f5dc8768153e",
                "resourceAlias": "order"
                }
            ],
            "type": "drugorder"
            }
        ],
        "voided": false,
        "visit": {
            "uuid": "adb0e803-8c43-4eaa-b841-67e1625ecf09",
            "display": "Consulta Ambulatoria @ UPSS - HOSPITALIZACIÓN - 13/03/2026 16:30",
            "links": [
            {
                "rel": "self",
                "uri": "http://hii1sc-dev.inf.pucp.edu.pe/openmrs/ws/rest/v1/visit/adb0e803-8c43-4eaa-b841-67e1625ecf09",
                "resourceAlias": "visit"
            }
            ]
        },
        "encounterProviders": [],
        "diagnoses": [],
        "links": [
            {
            "rel": "self",
            "uri": "http://hii1sc-dev.inf.pucp.edu.pe/openmrs/ws/rest/v1/encounter/368177ca-ad25-40ba-be1f-86d51e5b1d77",
            "resourceAlias": "encounter"
            },
            {
            "rel": "full",
            "uri": "http://hii1sc-dev.inf.pucp.edu.pe/openmrs/ws/rest/v1/encounter/368177ca-ad25-40ba-be1f-86d51e5b1d77?v=full",
            "resourceAlias": "encounter"
            }
        ],
        "resourceVersion": "2.2"
    }
    ```

### Datos de prueba
- Medicamento: `00807 - AMOXICILINA 250 mg (250 mg)`
- Intruscciones de dosificación:
    - Dosis: `1`
    - Unidad de dosis: `Cápsula`
    - Ruta: `Via oral`
    - Frecuencia: `Cada doce horas`
- Duración de la prescripción:
    - Fecha de inicio: `automático` (fecha de la visita)
    - Duración: `5`
    - Unidad de duración: `Días`
- Instrucciones de dispensación:
    - Cantidad a dispensar: `10`
    - Unidad de dispensación: `Cápsula`
    - Indicación: `Tomar una cápsula cada doce horas durante cinco días para tratar la infección.`

## Finalizar la visita

### Información técnica
- uuid de la visita: `automático` (uuid de la visita creada)
- petición: `POST /openmrs/ws/rest/v1/visit/3a47fad7-c95b-4ea1-b19a-f9fffdd22879`
- body:
```json
{
  "stopDatetime": "automático" (fecha y hora actual)
}
```

## Descontinuar orden
### Información técnica
- uuid de la orden: `automático` (uuid de la orden creada)
- petición: `POST /openmrs/ws/rest/v1/encounter`
    - body:
    ```json
    {
        "patient": "6686b5b6-b8df-4596-90c3-d0321cc0e728",
        "location": "35d2234e-129a-4c40-abb2-1ae0b2400001",
        "encounterType": "39da3525-afe4-45ff-8977-c53b7b359158",
        "encounterDatetime": "2026-03-13T16:30:05.000Z",
        "visit": "adb0e803-8c43-4eaa-b841-67e1625ecf09",
        "obs": [],
        "orders": [
            {
            "action": "DISCONTINUE",
            "type": "drugorder",
            "previousOrder": "63320ab0-f531-4309-b62c-35d9537a37e1",
            "patient": "6686b5b6-b8df-4596-90c3-d0321cc0e728",
            "careSetting": "6f0c9a92-6f24-11e3-af88-005056821db0",
            "encounter": null,
            "orderer": "fc23b972-88bd-4b18-9de7-2249cc0ad3f5",
            "concept": "a8a2e2cc-cf60-4c03-ab31-c90917b91f16",
            "drug": "5219bdad-dfb2-4079-b6a2-1dcce2304058",
            "orderReasonNonCoded": null
            }
        ]
    }
    ```
