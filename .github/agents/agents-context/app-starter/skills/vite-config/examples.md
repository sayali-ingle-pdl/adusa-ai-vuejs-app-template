# Vite Config Skill - Examples

> **Note**: See `SKILL.md` for skill instructions. This file contains detailed examples for different configurations.

## Basic Template

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
          target: 'https://aksc-pdlspectrum-nonprd-cu-01.az.dev.peapod.com/dev',
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

## Example 1: Standard Vite Configuration

**User Request:**
```
Generate vite config for my application
```

**Parameters:**
- `application_id`: `sup-omni-inventory-manager-web`
- `default_port`: `8089`
- `api_base_path`: `/omni-access-manager`
- `vite_build_format`: `system`

**Generated File:** `vite.config.ts`

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
      port: 8089,
      proxy: {
        '^/omni-access-manager': {
          target: 'https://aksc-pdlspectrum-nonprd-cu-01.az.dev.peapod.com/dev',
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
        formats: ['system'],
        fileName: () => 'js/app.js',
      },
      rollupOptions: {
        external: [],
        output: {
          format: 'system',
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

- The configuration includes Vue 3 plugin for Single File Component support
- CSS injection plugin is used only in build mode for single-spa micro frontend compatibility
- SVG loader is configured with SVGO optimization while preserving viewBox
- Proxy configuration routes API calls to the backend development server
- Build output is in SystemJS format for single-spa integration
- SCSS global theme imports are automatically available in all components
