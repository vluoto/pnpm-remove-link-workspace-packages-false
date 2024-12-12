# pnpm-remove-link-workspace-packages-false

The workspace is configured with `link-workspace-packages=false` and contains
two packages: `foo` and `@vluoto/bar`. The `foo` package depends on
`@vluoto/bar` and installs it from the GitHub Packages registry. Both `foo` and
`@vluoto/bar` list `react` as a dependency.

When running `pnpm --filter foo remove react`, the command behaves as expected,
successfully removing `react` from `foo` without any undesired side-effects.
However, executing the equivalent command after navigating to `foo` (`cd
packages/foo`) and running `pnpm remove react` produces unexpected results. In
this scenario, `@vluoto/bar` is incorrectly `link:`ed to `../bar`, despite
`link-workspace-packages` being explicitly set to `false`.

## Reproduction steps

1. `nvm use`
2. `corepack enable`
3. `pnpm install`
4. `cd packages/foo`
5. `pnpm remove react`

```diff
diff --git a/packages/foo/package.json b/packages/foo/package.json
index 3882cbc..fcbdd54 100644
--- a/packages/foo/package.json
+++ b/packages/foo/package.json
@@ -8,8 +8,7 @@
     "test": "echo \"Error: no test specified\" && exit 1"
   },
   "dependencies": {
-    "@vluoto/bar": "1.0.1",
-    "react": "^19.0.0"
+    "@vluoto/bar": "1.0.1"
   },
   "keywords": [],
   "author": "",
diff --git a/pnpm-lock.yaml b/pnpm-lock.yaml
index 672f2f3..a3c6df4 100644
--- a/pnpm-lock.yaml
+++ b/pnpm-lock.yaml
@@ -22,24 +22,14 @@ importers:
     dependencies:
       '@vluoto/bar':
         specifier: 1.0.1
-        version: 1.0.1
-      react:
-        specifier: ^19.0.0
-        version: 19.0.0
+        version: link:../bar

 packages:

-  '@vluoto/bar@1.0.1':
-    resolution: {integrity: sha512-vuNnIrx4xzf//gnbKEKE3zechtDSB2MKxMNyDHjM75EWQIxfSTeaMFJuAiw7fG4/762z3dM/jLD+/MPa/LUVLQ==, tarball: https://npm.pkg.github.com/download/@vluoto/bar/1.0.1/f2bb7680d1e538f67e1789e6b9e9ccb7eb46bf80}
-
   react@19.0.0:
     resolution: {integrity: sha512-V8AVnmPIICiWpGfm6GLzCR/W5FXLchHop40W4nXBmdlEceh16rCN8O8LNWm5bh5XUX91fh7KpA+W0TgMKmgTpQ==}
     engines: {node: '>=0.10.0'}

 snapshots:

-  '@vluoto/bar@1.0.1':
-    dependencies:
-      react: 19.0.0
-
   react@19.0.0: {}

```

:monocle_face:

```diff
diff --git a/pnpm-lock.yaml b/pnpm-lock.yaml
index 672f2f3..a3c6df4 100644
--- a/pnpm-lock.yaml
+++ b/pnpm-lock.yaml
@@ -22,24 +22,14 @@ importers:
     dependencies:
       '@vluoto/bar':
         specifier: 1.0.1
-        version: 1.0.1
-      react:
-        specifier: ^19.0.0
-        version: 19.0.0
+        version: link:../bar
```
