<p align="center">
  <img src="https://github.com/nirus/useDynamicImport/assets/2213751/391c3b5e-be23-4bb6-a198-97a483623e04" width="300">
  <h2 align="center">useDynamicImport Hook</h2>
  <h4 align="center">Import modules dynamically & support webpack code splitting in Next.js and React compatible apps</h4>    
</p>
<br />

`<Suspense />` equivalent react hook to dynamically import modules for code splitting on client side only. 

Typescript compatible for module type inference for easy development.

> Note: Once the module is imported it is cached and subsequent import will always serve the previously import cache.

### The Hook - Copy the code. Free to use :)
```typescript
import { useEffect, useRef, useState } from "react";

const DEFAULT_VALUE: never[] = [];

export const useDynamicImport = <T>(modules: () => T[]) => {
  const [isMounted, setIsMounted] = useState(false);
  const modulesFnRef = useRef(modules);
  const [resolvedValues, setResolvedValues] = useState<
    [Awaited<T>[], { loading: boolean, isErrored: boolean, error?: unknown }]
  >([DEFAULT_VALUE, { loading: true, isErrored: false }]);

  useEffect(() => {
    /* Execute once only */
    if (isMounted) return;
    (async function importModule() {
      try {
        const result = await Promise.all(modulesFnRef.current());
        setResolvedValues([result, { loading: false, isErrored: false }]);
      } catch (e) {
        setResolvedValues([DEFAULT_VALUE, { loading: false, isErrored: true, error: e }]);
      }
      setIsMounted(true);
    })();
  }, [isMounted]);
  return resolvedValues;
};

```

### Hook Usage:

```typescript
import useDynamicImport from 'hook/useDynamicImport'

export default function Home() {
  const [[module1], { isErrored, loading }] = useDynamicImport(() => [import("./test")]);

  return <div>HOME PAGE {module1.pageName()}</div>
}
```
