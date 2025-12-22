# Quick Start Guide

## Using This Template

### Prerequisites
- Node.js v22.16.0 or equivalent
- npm or yarn package manager
- GitHub Personal Access Token (for @RoyalAholdDelhaize packages)

### Step 1: Start a New Project

1. **Optional pre-requisite for Ahold GitHub registry hosted Component Library**
   If this project requires installation of component library that is hosted in Ahold GitHub registry, then a PAT token will be required to install it.
   Please create a PAT token in GitHub, and make sure to assign it read: package permission.
   Then follow the guidelines below:
      - In the home directory (of your computer), create a file named ``` .npmrc ```
      - Add the following content to the file:
         ```bash
         @RoyalAholdDelhaize:registry=https://npm.pkg.github.com
         //npm.pkg.github.com/:_authToken={YOUR_TOKEN}
         ```
      - Replace the ``` {YOUR_TOKEN} ``` placeholder with the PAT created earlier.
      - Save and exit the file.

2. **Copy this template** to your new project location:
   ```bash
   cp -r adusa-ai-vuejs-app-template your-new-project
   cd your-new-project
   ```

3. **Remove the existing git repository** (so the agent can initialize a fresh one):
   ```bash
   rm -rf .git
   ```

4. **Update application parameters** in `docs/requirements/application-parameters.md`:
   ```markdown
   - application_name: `your-app-name`
   - default_port: `8090`
   (etc.)
   ```

### Step 2: Generate Project Structure

Use the app-starter agent to generate the complete project:

1. Open the project in VS Code or GitHub Copilot CLI
2. Invoke the `@app-starter` agent with a command like:
   ```
   @app-starter generate a new Vue 3 application
   ```
3. The agent will prompt you for required application parameters:
   - Application name (kebab-case)
   - NPM scope/organization
   - Router base path
   - API base path for proxying
   - Development server port
   - Build format (system or es)

The agent will:
- Collect all required parameters through interactive prompts
- Execute all skills in the correct order
- Generate all configuration files, source files, and tests
- Create a runnable application

**Note**: The agent does not cache parameters between sessions. Each time you invoke `@app-starter`, you'll be prompted for the necessary parameters.

### Step 3: Install Dependencies

```bash
npm install
```

### Step 4: Configure Environment

```bash
# Copy example environment file
cp .env.local.example .env.local

# Edit .env.local with your actual values
# - VITE_ACCESS_MANAGEMENT_BASE_URL
# - VITE_LAUNCHER_APP_URL
# - VITE_DATADOG_APPLICATION_ID
# - VITE_DATADOG_CLIENT_TOKEN
```

### Step 5: Run the Application

```bash
# Development mode (integrated with single-spa)
npm run serve

# Standalone mode (without launcher)
npm run serve:standalone

# Production build
npm run build
```

### Step 6: Verify Setup

After generation is complete, verify the application was created correctly:

```bash
# 1. Check TypeScript compilation (should have no errors)
npx tsc --noEmit

# 2. Run linting (should pass with no errors)
npm run lint

# 3. Run tests (should all pass)
npm run test:unit

# 4. Check test coverage (should meet thresholds: >80% for services/stores, >70% for components)
npm run test:unit -- --coverage

# 5. Start dev server and verify it runs without errors
npm run serve
# Open browser to http://localhost:{your_port} and verify app loads

# 6. Verify build succeeds
npm run build
# Check that dist/ directory is created with output files

# 7. Verify all required files exist
ls -la src/main.ts src/App.vue src/router/index.ts src/store/index.ts vite.config.ts
```

**Expected Results:**
- ✅ No TypeScript errors
- ✅ All linting rules pass
- ✅ All tests pass with adequate coverage
- ✅ Dev server starts on configured port
- ✅ Application renders in browser
- ✅ Production build completes successfully
- ✅ All configuration files are present

## Defining Your Application

### 1. System Requirements

Edit `docs/requirements/system-requirements.md`:
- Describe your application's purpose
- Define business entities
- List external dependencies

### 2. System Architecture

Edit `docs/requirements/system-architecture.md`:
- Update architecture decisions
- Modify technology stack if needed
- Define your routing structure
- Customize store modules

### 3. Create Feature Specifications

Use the template in `docs/templates/feature-spec-template.md`:

```bash
# Create a new feature spec
cp docs/templates/feature-spec-template.md \
   docs/requirements/your-feature-name-rev1-feature-spec.md

# Edit the feature spec with your requirements
```

### 4. Generate Implementation Plans

For each ready feature spec, create an implementation plan using the agent or template.

### 5. Implement Features

Use GitHub Copilot with the implementation plan to generate:
- View components
- Reusable components
- Services for API integration
- Vuex store modules
- Tests

## Common Tasks

### Add a New View

1. Create view component in `src/views/YourView/YourView.vue`
2. Add route in `src/router/index.ts`
3. Create tests in `src/views/YourView/YourView.spec.ts`

### Add a New API Service

1. Create interface in `src/interfaces/yourEntity.ts`
2. Create service in `src/services/yourEntityService.ts`
3. Create tests in `src/services/yourEntityService.spec.ts`

### Add a Vuex Module

1. Create module in `src/store/modules/yourModule.ts`
2. Register in `src/store/index.ts`
3. Create tests in `src/store/modules/yourModule.spec.ts`

### Add a Reusable Component

1. Create component in `src/components/yourFeature/YourComponent.vue`
2. Create tests in `src/components/yourFeature/YourComponent.spec.ts`
3. Document props and emits with TypeScript interfaces

## Development Workflow

### Daily Development

1. Pull latest changes: `git pull`
2. Install dependencies: `npm install`
3. Start dev server: `npm run serve`
4. Make changes
5. Run tests: `npm run test:unit`
6. Commit: Pre-commit hooks will run linting and formatting

### Feature Development

1. Create feature spec from template
2. Review and mark as "Ready"
3. Generate implementation plan
4. Implement following the plan
5. Write tests (>80% coverage for services/stores)
6. Update documentation
7. Create pull request

### Code Quality

```bash
# Lint and fix
npm run lint

# Format code
npm run prettier

# Run tests with coverage
npm run test:unit

# Watch mode for development
npm run test:unit:watch
```

## Docker Deployment

```bash
# Build Docker image
docker build -t your-app-name .

# Run container
docker run -p 80:80 your-app-name

# Health check
curl http://localhost/health
```

## Troubleshooting

### Module not found errors
```bash
npm install
```

### TypeScript errors
```bash
npm run lint
npx tsc --noEmit
```

### Build fails
1. Check environment variables are set
2. Verify `src/shared/constants/global.ts` exports `appId`
3. Check Vite configuration

### Tests failing
1. Check test setup in `tests/unit/testSetup.ts`
2. Verify mocks in `tests/unit/`
3. Run specific test: `npm run test:unit -- yourTest.spec.ts`

## Getting Help

1. **Documentation**: Check `docs/` directory
2. **Examples**: Review skill files in `.github/agents/agents-context/app-starter/skills/`
3. **Reference**: Look at `adusa-digital-fulfillment_ex-facilitator_sup-omni-inventory-manager-web`
4. **AI Assistant**: Use GitHub Copilot with the agents
5. **Team**: Contact the platform team

## Best Practices

- **Follow the agent skills** for consistent structure
- **Write tests** for all new code
- **Document features** before implementing
- **Use TypeScript** for type safety
- **Follow naming conventions** from copilot-instructions.md
- **Keep components small** and focused
- **Use Vuex** for shared state
- **Use theme variables** for styling
- **Enable scoped styles** in components
- **Lazy load routes** for code splitting

## Next Steps

After initial setup:

1. ✅ Define your system requirements
2. ✅ Update system architecture
3. ✅ Create your first feature spec
4. ✅ Generate implementation plan
5. ✅ Start implementing features
6. ✅ Write comprehensive tests
7. ✅ Set up CI/CD pipeline
8. ✅ Deploy to development environment

Happy coding! 🚀
