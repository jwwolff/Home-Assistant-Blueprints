# Repository Guidelines

## Project Structure

This repository contains Home Assistant automation blueprints for motion-activated lighting. All blueprints are YAML files at the root level:

- `Motion_Light.yaml` — Original motion light blueprint
- `Motion_Light_Dimmer.yaml` — Dimmable light automation with time-based brightness
- `Motion_Light_NonDimmable.yaml` — Multi-group non-dimmable light automation

Each blueprint is a standalone, importable Home Assistant automation definition.

## Blueprint Structure

All blueprints follow this structure:

```yaml
blueprint:
  name: Blueprint Name
  description: >
    Short description of functionality.
  domain: automation
  input:
    # Input definitions
mode: restart
max_exceeded: silent
trigger:
  # Trigger definitions
variables:
  # Template variables
condition:
  # Condition definitions
action:
  # Action definitions
```

## Input Conventions

- Use descriptive names with clear labels
- Include `description` for all inputs explaining purpose and usage
- Mark optional inputs with "(optional)" in the name
- Use appropriate selector types:
  - `entity` with `domain` for specific entity types
  - `number` with `min`, `max`, `step`, `unit_of_measurement`
  - `select` with `options` for predefined choices
  - `time` for time values

## Naming Patterns

- Blueprint files: `Motion_Light_*.yaml` (PascalCase, descriptive)
- Input names: `snake_case` (e.g., `motion_sensor`, `wait_period`)
- Variable names: `snake_case` matching input names

## Validation

Validate blueprints by:
1. Checking YAML syntax with a linter (e.g., )
2. Verifying all inputs have proper names and descriptions
3. Ensuring entity selectors use correct domains
4. Reviewing template logic for edge cases (unknown/unavailable states)

## Commit Guidelines

Commit messages should be descriptive and explain the change:
- "Added [feature] to [blueprint]"
- "Fixed [issue] in [blueprint]"
- "Updated [input/action] for [blueprint]"

Avoid vague messages like "fixed bug" or "updated file".

## Contributing

When modifying blueprints:
1. Verify template syntax with a YAML linter
2. Ensure all inputs have proper descriptions
3. Check that entity selectors use correct domains
4. Review template logic for edge cases
5. Validate the blueprint imports without errors

## Common Errors to Avoid

### Invalid Keys in Blueprint Inputs

- **`visible`** — Not a valid key in HA blueprint inputs. Do not use to conditionally show/hide fields.
- **`required: false`** — Not supported for blueprint inputs. Entity selectors always require a value. Use "(optional)" in the name and handle empty cases in templates.

### Invalid Keys in Selectors

- **`description`** in `select` options — Not a valid key. Use `label` and `value` only.
- **`required: false`** on entity selectors — Does not make the field optional in the UI.

### Template and Logic Errors

- Always handle `unknown`, `unavailable`, and `none` states when reading sensor values in templates.
- Use `states(entity)` not `state` to get entity state values.
- When using `!input` in conditions, ensure the input exists and is not empty before referencing it.

### YAML Structure

- Maintain consistent indentation (2 spaces).
- Use `>` for multi-line descriptions to keep YAML readable.
- Test blueprint syntax with a YAML linter before importing.

## YAML Style Guide

Follow the Home Assistant YAML style standards for consistency:

### Indentation
- Use 2 spaces for indentation
- Block-style sequences (lists) must be indented under their key

### Booleans
- Use lowercase `true` and `false` only (not `True`, `on`, `yes`)

### Strings
- Prefer double quotes for strings
- Entity IDs, platform types, and condition types do not need quoting
- Use folded style (`>`) for multi-line descriptions

### Sequences and Mappings
- Use block style for lists and mappings
- Avoid flow style (`[1, 2, 3]` or `{key: value}`)

### Null Values
- Use implicit null (empty value) instead of `~` or `null`

### Templates
- Quote templates with double quotes: `"{{ template }}"`
- Use single quotes inside templates for string literals
- Use helper functions: `states('entity.id')` not `states.entity.id.state`
- Use spacing around filter pipes: `{{ value | float }}`
- Prefer short condition syntax: `conditions: "{{ condition }}"`
- Avoid long single-line templates; split across multiple lines

### Service Actions
- Use `target` with `entity_id` for service calls
- Always use list style for actions, conditions, and sequences (even single items)

### Default Values
- Do not include default values in examples unless specifically documenting them

## Making Entity Inputs Optional

To make an entity selector input optional (allow users to leave it blank):

```yaml
input_name:
  name: Input Name
  description: Description text.
  selector:
    entity:
      domain: sensor
      multiple: true  # This makes the field optional
```

**Important:** `required: false` does NOT work for blueprint inputs. The `multiple: true` attribute on the entity selector is the correct way to make an entity input optional.

When handling optional entity inputs in templates, always check for empty values:

```jinja2
{% if inputs.input_name and states(inputs.input_name) not in ['unknown', 'unavailable', 'none'] %}
  {{ states(inputs.input_name) }}
{% else %}
  default_value
{% endif %}
```
