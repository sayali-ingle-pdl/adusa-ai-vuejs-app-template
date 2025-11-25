# SonarQube Properties Skill

## Purpose
Generate SonarQube project configuration file for code quality analysis and coverage reporting.

## Output
Create the file: `sonar-project.properties`

## Example File
See: `examples.md` in this directory for complete examples and detailed explanations.

## Template

```properties
sonar.projectKey=adusa-digital-fulfillment_ex-facilitator_sup-omni-inventory-manager-web
sonar.projectName=adusa-digital-fulfillment_ex-facilitator_sup-omni-inventory-manager-web
sonar.projectDescription=A Vue.js Micro Frontend Application for Inventory Manager
sonar.sources=src
sonar.language=ts
sonar.sourceEncoding=UTF-8
sonar.scm.provider=git
sonar.qualitygate.wait=true
sonar.javascript.lcov.reportPaths=./coverage/lcov.info
sonar.clover.reportPaths=./coverage/clover.xml
sonar.tests=src
sonar.test.inclusions=src/**/*.spec.ts
sonar.exclusions=src/**/*.spec.ts
sonar.coverage.exclusions=src/main.ts, src/services/**/*.ts, src/**/router/*.ts
```

## Notes
- Project key and name should follow the format: `adusa-digital-fulfillment_ex-facilitator_sup-{application-name}`
- Sources are set to `src` directory
- Language is TypeScript (`ts`)
- Quality gate waits for analysis results
- Coverage reports expected in `coverage/` directory
- Test files (*.spec.ts) are excluded from coverage calculations
- Entry points, services, and router files are excluded from coverage metrics
