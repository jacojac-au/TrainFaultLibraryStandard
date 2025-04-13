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
│   │   └── example-train.tfls
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
