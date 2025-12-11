# SonarQube Properties Skill

## Purpose
Generate SonarQube project configuration file for code quality analysis and coverage reporting.

## Output
Create the file: `sonar-project.properties`


## Template
See: `examples.md` in this directory for complete template and detailed examples.

## Notes
- Project key and name should follow the format: `adusa-digital-fulfillment_ex-facilitator_sup-{application-name}`
- Sources are set to `src` directory
- Language is TypeScript (`ts`)
- Quality gate waits for analysis results
- Coverage reports expected in `coverage/` directory
- Test files (*.spec.ts) are excluded from coverage calculations
- Entry points, services, and router files are excluded from coverage metrics
