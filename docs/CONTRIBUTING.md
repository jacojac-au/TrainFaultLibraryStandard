# Contributing to the Train Fault Library Standard (TFLS)

Thank you for your interest in contributing to the Train Fault Library Standard! This document provides guidelines and instructions for contributing to this project.

## Ways to Contribute

There are several ways you can contribute to TFLS:

1. **Improving the schema**: Suggest enhancements to the core TFLS schema
2. **Documentation improvements**: Help make the standard more accessible and easy to understand
3. **Implementation tools**: Create validation tools or other utilities that help implement the standard
4. **Example files**: Provide generic examples (not actual operator data) that demonstrate the standard

## Important Note on Operator Data

**Operator-specific fault reference .tfls files are confidential and should NOT be submitted to this public repository.**

The TFLS is designed as a schema standard that operators implement privately within their own systems. This repository contains only:

- The core schema definition
- Documentation on how to use the standard
- Generic examples for illustration purposes

## Contribution Process

### For Schema or Documentation Changes

1. Open an issue first to discuss the proposed changes
2. Fork the repository
3. Create a new branch for your feature
4. Implement the changes
5. Submit a pull request referencing the issue

## Quality Standards for Example Files

When adding example fault entries:

1. Follow the schema structure exactly
2. Use clear, technical language in descriptions
3. Provide comprehensive rectification steps
4. Use generic or fictional operator names and train types
5. Do not include proprietary or sensitive information

Example of a well-formatted example fault entry:

```json
{
  "faultName": "Descriptive Fault Name",
  "system": "System Category",
  "category": "Critical",
  "description": "Clear technical description of the fault including its impact on operations.",
  "rectificationSteps": [
    "Step 1 of the rectification process",
    "Step 2 with clear, actionable instructions",
    "Final verification step"
  ],
  "notes": "Important contextual information for maintenance staff."
}
```

## File Extension

All TFLS files should use the `.tfls` file extension rather than `.json`, even though the file content is in JSON format. This helps to:

1. Clearly identify TFLS-compliant files
2. Enable specialized handling in development environments
3. Distinguish fault library files from other JSON data files

While the file format is standard JSON, the `.tfls` extension signifies conformity to the Train Fault Library Standard schema and expected content structure.

## Operator-Specific Implementation Considerations

While operator files remain confidential, we recognize that different operators may use:

- Different terminology for similar components
- Various categorisation systems for faults
- Different severity classifications
- Operator-specific maintenance procedures

This diversity is expected and accommodated in the standard, which is why:

- The schema provides flexibility while maintaining a consistent structure
- Operators can use their own terminology and classification systems
- The standard focuses on format consistency rather than content standardisation

## Documentation Contributions

When contributing to documentation:

1. Use clear, technical language
2. Include practical examples where appropriate
3. Explain the "why" behind recommendations
4. Consider readers with varying levels of rail industry experience

## Schema Evolution

The TFLS schema will evolve over time. When proposing schema changes:

1. Maintain backward compatibility where possible
2. Provide clear justification for new fields or structural changes
3. Consider the impact on existing implementations
4. Update documentation to reflect the changes

## License and Attribution

By contributing to this project, you agree that your contributions will be licensed under the same license as the main project. See the LICENSE file for details.

All contributors will be acknowledged in the project unless they specifically request not to be.

## Questions?

If you have any questions about contributing, please open an issue with your question or reach out to the repository maintainers.

Thank you for helping improve the Train Fault Library Standard!
