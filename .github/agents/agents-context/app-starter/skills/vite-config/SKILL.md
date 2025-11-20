# Vite Config Skill

## Purpose
Generate the `vite.config.ts` file for building and serving the Vue 3 application.

## Input Parameters
Read from `copilot-instructions.md` and `docs/requirements/application-parameters.md`:
- `application_id`: The application ID for style tag
- `default_port`: The development server port
- `api_base_path`: The base path for API proxy
- `vite_build_format`: The build output format (system or es)

## Output
Create the file: `vite.config.ts`

## Template

```typescript
import { defineConfig } from 'vite';
import path from 'path';
import vue from '@vitejs/plugin-vue';
import cssInjectedByJsPlugin from 'vite-plugin-css-injected-by-js';
import svgLoader from 'vite-svg-loader';
import { appId } from './src/shared/constants/global';

export default defineConfig(({ mode, command }) => {
  const isBuild = command === 'build';

  return {
    define: {
      'process.env': {},
    },

    plugins: [
      vue(),
      isBuild &&
        cssInjectedByJsPlugin({
          styleId: appId,
        }),
      svgLoader({
        svgo: true,
        svgoConfig: {
          plugins: [
            {
              name: 'preset-default',
              params: {
                overrides: {
                  removeViewBox: false,
                },
              },
            },
          ],
        },
      }),
    ].filter(Boolean),

    resolve: {
      alias: {
        '@': path.resolve(__dirname, 'src'),
        vue: path.resolve(__dirname, 'node_modules/vue'),
      },
      extensions: ['.mjs', '.js', '.ts', '.jsx', '.tsx', '.json', '.vue'],
    },

    base: mode === 'development' ? '/' : './',

    server: {
      port: {{default_port}},
      proxy: {
        '^{{api_base_path}}': {
          target: 'https://your-backend-service-url.com',
          changeOrigin: true,
          secure: false,
          prependPath: true,
        },
      },
    },

    build: {
      target: 'esnext',
      lib: {
        entry: path.resolve(__dirname, 'src/main.ts'),
        formats: ['{{vite_build_format}}'],
        fileName: () => 'js/app.js',
      },
      rollupOptions: {
        external: [],
        output: {
          format: '{{vite_build_format}}',
          entryFileNames: 'js/app.js',
          exports: 'auto',
        },
      },
      sourcemap: true,
      emptyOutDir: true,
    },

    css: {
      preprocessorOptions: {
        scss: {
          additionalData: `@use "@/theme/" as *;`,
        },
      },
    },
  };
});
```

## Notes
- The proxy configuration should be updated with the actual backend service URL
- The `appId` is imported from the global constants file
- CSS is injected by JavaScript in build mode for single-spa compatibility
- SVG loader is configured to preserve viewBox for proper scaling
