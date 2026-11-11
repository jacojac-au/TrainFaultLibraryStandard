# Train Fault Library Standard (TFLS)

## Overview

The **Train Fault Library Standard (TFLS)** defines a unified, schema-based framework for documenting all potential faults associated with specific train types. It is not a system for logging incidents — it is a **comprehensive fault reference** designed for use in maintenance planning, diagnostics, training, documentation, and integration across software systems.

Each rail operator maintains their own `.tfls` fault reference files for each train type in their fleet. These files conform to the TFLS schema, ensuring consistent structure across the industry while allowing flexibility in categorisation, terminology, and severity levels.

## Purpose

TFLS was created to:

- ✅ Standardise how train faults are described and structured
- ✅ Support operator-specific taxonomies while maintaining schema compatibility
- ✅ Enable integration with maintenance platforms, diagnostic tools, and training systems
- ✅ Preserve operational and engineering knowledge across generations
- ✅ Improve clarity, traceability, and interoperability across rail operators and vendors

## Key Principles

- Operators maintain **separate fault reference files** (with `.tfls` extension) per train type
- Each file documents **all potential faults**, not real-time incidents
- Files must conform to the **shared TFLS schema**
- Metadata such as `schemaVersion`, `lastUpdated`, and `maintainer` are required for traceability

## Repository Structure

```
train-fault-library-standard/
├── schema/                      # JSON Schema definition
│   └── tfls-faults-schema.json
├── examples/                    # Example implementations (not actual operator files)
│   ├── example-operator/
│   │   ├── example-train.tfls
│   │   └── manuals/             # Manuals referenced by example-train.tfls
│   │       └── drivers-manual.pdf
│   └── documentation/
├── docs/                        # Supporting documentation
└── README.md                    # You're here!
```

## File Format

Each operator maintains their own confidential `.tfls` file that follows this structure:

- A top-level `meta` section describing the operator, train type, schema version, and update details
- A `faults` array of structured fault entries

### Example

```json
{
  "meta": {
    "operator": "Example Operator",
    "trainType": {
      "name": "Example Train",
      "code": "EXT",
      "manufacturer": "Example Manufacturer"
    },
    "schemaVersion": "1.0.0",
    "lastUpdated": "2025-04-14",
    "maintainer": "engineering@example.com"
  },
  "faults": [
    {
      "faultName": "Brake Pressure Sensor Failure",
      "system": "Brake System",
      "category": "Major",
      "description": "Sensor fails to register correct air pressure in the brake line.",
      "rectificationSteps": [
        "Isolate brake circuit",
        "Replace pressure sensor unit",
        "Recalibrate brake system"
      ],
      "notes": "Occurs during heavy rainfall. Refer to maintenance manual."
    }
  ]
}
```

Although `.tfls` files are structured as JSON, we recommend the `.tfls` extension to clearly distinguish them as TFLS documents.

### Manuals and Manual References

Since schema version `1.1.0`, a file can list the manuals that cover its train type in `meta.manuals`, and each fault can point to the pages that describe it with `manualReferences`. Both are optional, so `1.0.0` files remain valid. Version `1.2.0` adds an optional `effectiveDate` and `group` to each manual, and makes a fault's `category` optional: a library built from manuals that don't classify their faults should leave it out, and applications then show no classification, rather than a placeholder.

```json
{
  "meta": {
    "operator": "Example Operator",
    "trainType": { "name": "Example Train", "code": "EXT", "manufacturer": "Example Manufacturer" },
    "schemaVersion": "1.2.0",
    "lastUpdated": "2025-04-14",
    "maintainer": "engineering@example.com",
    "manuals": [
      {
        "id": "drivers-manual",
        "title": "Driver's Manual",
        "documentNumber": "EXT-DM-001",
        "revision": "C",
        "effectiveDate": "2025-03-01",
        "group": "Driver's manuals",
        "file": "manuals/drivers-manual.pdf"
      }
    ]
  },
  "faults": [
    {
      "faultName": "Brake Pressure Sensor Failure",
      "system": "Brake System",
      "category": "Major",
      "manualReferences": [
        { "manual": "drivers-manual", "page": 12, "section": "4.2 Brake Pressure Faults" }
      ]
    }
  ]
}
```

- `id` must be unique within the file. Faults refer to a manual by its `id`.
- `page` is the page of the PDF to open, counting the first page of the file as 1. It is not the number printed on the page, which often differs because of covers and front matter.
- `section` is shown alongside the reference. If `page` is left out, applications may open the manual at the PDF bookmark or heading with this title, so copy it exactly.
- `file` is the manual's location inside a TFLS package (see below). Leave it out to cite a manual without distributing it.
- `documentNumber` should be written the way other manuals cite it (for example `TAN 152`), since applications may turn those citations into links between manuals.
- `effectiveDate` (since `1.2.0`) is the date from which the manual's `revision` applies, in `YYYY-MM-DD` form.
- `group` (since `1.2.0`) names the volume, chapter or system a manual belongs to, such as `Volume 6 – Faults and failures` or `Brakes`. Applications group manuals by it when browsing, in the order groups first appear in `manuals`.

Operators often publish a procedure per PDF, so a library may list dozens of manuals. Give each one a `group`, and applications should let readers browse groups and the sections within a manual, search manuals by title, and search the text of every manual at once.

### Structured Steps, Fault Identifiers and Category Source

Version `1.3.0` adds three optional fields. Files written for earlier versions remain valid, and applications written for earlier versions ignore the new fields.

```json
{
  "meta": {
    "schemaVersion": "1.3.0",
    "categorySource": "manual"
  },
  "faults": [
    {
      "id": "door-control-unit-failure",
      "faultName": "Door Control Unit Failure",
      "system": "Passenger Doors",
      "rectificationSteps": [
        "Driver: Isolate affected door system",
        "Guard: Secure the affected doors out of use and tell passengers"
      ],
      "procedure": [
        { "actor": "Driver", "context": "In service", "text": "Isolate affected door system" },
        { "actor": "Guard", "context": "In service", "text": "Secure the affected doors out of use and tell passengers" },
        { "actor": "Maintainer", "context": "At the depot", "text": "Replace door control unit if reset unsuccessful" }
      ]
    }
  ]
}
```

- `id` identifies an entry. It must be unique within the file and should stay the same from one version of the file to the next, because applications use it to remember bookmarked and recently viewed faults. `faultCode` remains the code the operator's own systems show, which many manuals don't give and which need not be unique.
- `procedure` is the structured form of `rectificationSteps`. Each step has its `text`, and may name the `actor` who performs it and the `context` it applies to, using the manual's own words. Applications use `procedure` when it is present, so they can show a Driver only their steps and can keep the steps for different situations apart. Keep `rectificationSteps` as well so older applications still show the steps.
- Steps that share a `context` belong together. The steps of one context are an alternative to the steps of another (what to do in service, as against at the depot), not a continuation, so applications show each context under its own heading. Leave `context` out for steps that apply whatever the situation.
- `meta.categorySource` says where the `category` values come from: `manual` when they are the operator's own classification as its manuals write it, `editorial` when the library's author chose them. Applications show categories as a severity only when it is `manual`. A library built from manuals that don't classify their faults should either leave `category` out or declare `editorial`.

## TFLS Packages (.tflsx)

A TFLS package bundles a `.tfls` file with the PDF manuals it references, so a fault library and its manuals can be distributed as a single file.

A package is a standard ZIP archive with the `.tflsx` extension:

```
example-train.tflsx
├── example-train.tfls
└── manuals/
    └── drivers-manual.pdf
```

- The archive must contain exactly one `.tfls` file, either at the root or inside a single top-level folder.
- Manual paths in `meta.manuals[].file` are relative to the `.tfls` file, use forward slashes, and must not start with `/` or contain `..`.
- Entries may be stored or Deflate-compressed. Encrypted archives are not supported.
- PDFs in the package that are not listed in `meta.manuals` may be shown by applications as additional manuals, but cannot be referenced by faults.

Any ZIP tool can create a package. For example, from the folder containing the `.tfls` file:

```bash
zip -r -X example-train.tflsx example-train.tfls manuals
```

Remember that manuals are often more sensitive than the fault library itself. Only package manuals you are permitted to distribute.

### File types

Applications that open TFLS files should recognise them by these types, so a file shared from Mail, a messaging app or a file browser is offered to the right apps:

| Extension | Uniform Type Identifier | Conforms to | MIME type |
| --- | --- | --- | --- |
| `.tfls` | `au.jacojac.tfls` | `public.json`, `public.data` | `application/vnd.tfls+json` |
| `.tflsx` | `au.jacojac.tflsx` | `public.data` | `application/vnd.tfls.package+zip` |

FaultBuddy exports these types. Other apps should declare them as imported types with the same identifiers.

## Usage

### 🔧 For Operators

1. Use the schema to create your own confidential `.tfls` files
2. Each operator maintains their own `.tfls` files privately within their systems
3. Create one `.tfls` file per train type using the shared schema
4. Fill in `meta` and define each potential fault in the `faults` array
5. Validate your file against the TFLS schema (see below)

**Note: Operator fault files are confidential and should not be shared in this public repository.**

### 🧑‍💻 For Developers

- Load `.tfls` files into front-end tools, maintenance platforms, or knowledge bases
- Support filtering by train type, fault category, or severity
- Provide UI-based rectification workflows based on standardised steps
- Integrate tags and categories for advanced diagnostics and linking
- Accept `.tflsx` packages and open a fault's manual references at the referenced page

## ✅ Schema Validation

Use the JSON Schema file to validate all operator `.tfls` files:

```bash
npx ajv-cli validate \
  -s schema/tfls-faults-schema.json \
  -d examples/example-operator/example-train.tfls
```

You can also integrate schema validation into your CI/CD workflow to enforce compliance.

## Contributing

- Contributions from operators, vendors, and developers are welcome
- Please follow the [Contributing Guidelines](docs/CONTRIBUTING.md) before submitting pull requests
- If you're implementing the schema internally, we'd love to hear how you're using it

## License

This project is provided under a custom license with the following conditions:

- The standard may be freely used for implementation in systems and applications
- Attribution must be given to the original creator in any implementation or derivative work
- The creator must be notified of any use or implementation of the standard
- See the [LICENSE](LICENSE) file for complete terms and conditions

© 2025 Jack Jacobs
