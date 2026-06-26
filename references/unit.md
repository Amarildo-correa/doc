Convenção do projeto: um 'describe' por função/módulo exportado, e o arquivo de teste espelha o caminho do módulo original. Cobre comportamento normal, edge cases e regressões nomeadas.

```js
import { describe, it, expect } from "vitest";
import { truncate } from "../../public/js/lib/truncate.js";

describe("truncate", () => {
    it("lida com string vazia", () => {
        expect(truncate("", 5)).toBe("");
    });
});
```
