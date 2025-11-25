# 📂 Modelos de Prompts para IA

![License](https://img.shields.io/badge/license-MIT-green.svg)
![Status](https://img.shields.io/badge/status-active-success.svg)
![Language](https://img.shields.io/badge/language-MD%20%7C%20JSON-blue.svg)

Repositório para armazenar e versionar **modelos de prompts** reutilizáveis para diferentes IAs (ChatGPT, Claude, etc.), em formatos adequados tanto para **leitura humana** (`.md`) quanto para **uso programático** (`.json`).

---

## 🎯 Objetivo

Este repositório tem como propósito:

- Centralizar e organizar modelos de prompts.
- Garantir **consistência** na escrita e formatação dos prompts.
- Facilitar **reuso** em scripts, APIs e aplicações (Node.js, Next.js, Python, etc.).
- Permitir evolução controlada via **Git/GitHub** (versionamento, PRs, histórico).

---

## 📁 Estrutura do Repositório

Exemplo de estrutura sugerida:


```bash 
.
├── README.md 
├── LICENSE 
├── mercado-livre/ 
│   ├── opiniao-mercado-livre.md 
│   └── opiniao-mercado-livre.json 
└── outros/ 
    └── exemplo-modelo.md
```

Sugestão de categorias (pastas):

- `mercado-livre/` – prompts específicos para avaliações, interações e automações envolvendo Mercado Livre.
- `atendimento/` – prompts para suporte ao cliente, respostas padrão, etc.
- `codigo/` – prompts para geração, revisão ou explicação de código.
- `documentacao/` – prompts para gerar ou melhorar documentação técnica.
- `outros/` – modelos genéricos ou em rascunho.

---

## 🧱 Padrões e Convenções

### 📛 Nome de arquivos

- Usar **kebab-case**:  
  `opiniao-mercado-livre.md`, `resumo-artigo-tecnico.json`
- Nome **descritivo**, refletindo a função do prompt:
  - `opiniao-mercado-livre.md` – gera opiniões/reviews.
  - `email-followup-cliente.json` – gera e-mails de follow-up para clientes.

---

### 📄 Formato dos arquivos `.md`

Estrutura sugerida:

```markdown
# Nome do Modelo – Contexto

## Descrição:
Breve descrição do objetivo do prompt.

## Prompt:
Texto completo do prompt a ser utilizado com a IA.

## Notas (opcional):

## Observações adicionais.
## Regras específicas de uso.
```

#### Exemplo (resumido):


```markdown

# Modelo de Prompt – Opinião Mercado Livre

## Descrição:
Gera opiniões com base em grau de satisfação e título do produto.

## Prompt:
Crie uma opinião de até 1500 caracteres sobre um produto que comprei no Mercado Livre. […]

```
---

### 🧾 Formato dos arquivos `.json`

Estrutura sugerida (simples e fácil de consumir em código):


```json 
{ 
    "name": "Nome curto do modelo", 
    "description": "Descrição breve do que o modelo faz.", 
    "prompt": "Texto completo do prompt a ser usado com a IA." 
}
```

#### Exemplo (real):


```json 
{ 
    "name": "Opinião Mercado Livre", 
    "description": "Gera opiniões com base em grau de satisfação e título do produto.", 
    "prompt": "Crie uma opinião de até 1500 caracteres sobre um produto que comprei no Mercado Livre. O texto deve ter um tom simples, direto e leigo, como se fosse uma avaliação espontânea de um consumidor comum. Para cada produto, vou te informar: - Um grau de satisfação (de 1 a 5, onde 1 significa que não recomendo e 5 significa que gostei e recomendo); - O título do anúncio ou nome do produto; - (Opcional) Uma observação que quero que seja incluída na opinião. Na resposta, quero apenas o texto da opinião, sem explicações ou formatações extras. Depois da sua resposta, posso pedir uma nova opinião para outro produto ou solicitar que refaça a anterior." 
}
```

---

## 🧪 Exemplos de Uso

Abaixo alguns exemplos práticos de como consumir os arquivos `.json` deste repositório em diferentes stacks.
´
> ⚠️ Nos exemplos com APIs de IA, o código é ilustrativo. Adapte para o provedor/modelo que estiver usando (OpenAI, Anthropic, etc.).

---

### 🟦 1. Uso com Node.js (JavaScript)

#### 1.1. Carregar e usar um prompt localmente

- Instalação básica:


```bash
npm init -y npm install fs
```

- Exemplo:


```js 
// scripts/usarPrompt.js 
import fs from "fs"; import path from "path";

const promptPath = path.join( process.cwd(), "mercado-livre", "opiniao-mercado-livre.json" );

const raw = fs.readFileSync(promptPath, "utf-8"); const promptData = JSON.parse(raw);

const promptBase = promptData.prompt;

// Exemplo de dados dinâmicos 
const grauSatisfacao = 5; 
const tituloProduto = "Fone de Ouvido Bluetooth XYZ"; 
const observacao = "A bateria dura bastante e é confortável para usar por horas.";

const entradaUsuario = 
GrauDeSatisfacao: ${grauSatisfacao}
Produto: ${tituloProduto}
Observação: ${observacao}
;

// Aqui você concatenaria com o prompt base antes de enviar para a IA 
const mensagemFinal = ${promptBase}\n\n${entradaUsuario};

console.log("Mensagem final a ser enviada para a IA:\n"); 
console.log(mensagemFinal);
```

---

#### 1.2. Exemplo com API (OpenAI via SDK – simplificado)


```bash 
npm install openai dotenv
```



```js 
// scripts/usarComOpenAI.js 
import fs from "fs"; 
import path from "path"; 
import OpenAI from "openai"; 
import dotenv from "dotenv";

dotenv.config();

const client = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });

async function main() { const promptPath = path.join( process.cwd(), "mercado-livre", "opiniao-mercado-livre.json" );

const promptData = JSON.parse(fs.readFileSync(promptPath, "utf-8"));

const grauSatisfacao = 4; const tituloProduto = "Mouse sem fio ergonômico ABC"; const observacao = "Uso para trabalho o dia inteiro.";

const entradaUsuario = 
Grau de satisfação: ${grauSatisfacao}
Produto: ${tituloProduto}
Observação: ${observacao}
;

const systemPrompt = promptData.prompt;

const completion = await client.chat.completions.create({ model: "gpt-4o-mini", messages: [ { role: "system", content: systemPrompt }, { role: "user", content: entradaUsuario } ] });

console.log("Opinião gerada:"); console.log(completion.choices[0].message.content); }

main().catch(console.error);
```

---

### ⚫ 2. Uso com Next.js (App Router)

Exemplo de API route em Next.js 13+ usando o prompt JSON.


```bash
npm install openai dotenv
```



```ts 
// app/api/opiniao-mercado-livre/route.ts 
import { NextRequest, NextResponse } from "next/server"; 
import OpenAI from "openai"; 
import fs from "fs"; 
import path from "path";

const client = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });

export async function POST(req: NextRequest) { try { const { grauSatisfacao, tituloProduto, observacao } = await req.json();

const promptPath = path.join(
  process.cwd(),
  "mercado-livre",
  "opiniao-mercado-livre.json"
);

const promptData = JSON.parse(fs.readFileSync(promptPath, "utf-8"));
const systemPrompt = promptData.prompt;

const entradaUsuario = `
Grau de satisfação: ${grauSatisfacao} Produto: ${tituloProduto} Observação: ${observacao || "Nenhuma observação adicional."} `;

const completion = await client.chat.completions.create({
  model: "gpt-4o-mini",
  messages: [
    { role: "system", content: systemPrompt },
    { role: "user", content: entradaUsuario }
  ]
});

const opiniao = completion.choices[0].message.content;

return NextResponse.json({ opiniao }, { status: 200 });


} catch (error) { console.error(error); return NextResponse.json( { error: "Erro ao gerar opinião." }, { status: 500 } ); } }
```

Exemplo de chamada dessa API no frontend:


```ts 
// app/page.tsx (Next.js) "use client";

import { useState } from "react";

export default function HomePage() { const [titulo, setTitulo] = useState(""); 
const [grau, setGrau] = useState(5); 
const [obs, setObs] = useState(""); 
const [resultado, setResultado] = useState("");

const gerarOpiniao = async () => { const res = await fetch("/api/opiniao-mercado-livre", { method: "POST", headers: { "Content-Type": "application/json" }, body: JSON.stringify({ grauSatisfacao: grau, tituloProduto: titulo, observacao: obs }) });

const data = await res.json();
setResultado(data.opiniao || "Erro ao gerar opinião.");
};

return (

Gerar Opinião – Mercado Livre

  <div style={{ display: "flex", flexDirection: "column", gap: 8, maxWidth: 400 }}>
    <label>
      Título do produto:
      <input
        value={titulo}
        onChange={e => setTitulo(e.target.value)}
        style={{ width: "100%" }}
      />
    </label>

    <label>
      Grau de satisfação (1 a 5):
      <input
        type="number"
        min={1}
        max={5}
        value={grau}
        onChange={e => setGrau(Number(e.target.value))}
      />
    </label>

    <label>
      Observação (opcional):
      <textarea
        value={obs}
        onChange={e => setObs(e.target.value)}
      />
    </label>

    <button onClick={gerarOpiniao}>Gerar opinião</button>
  </div>

  <section style={{ marginTop: 24 }}>
    <h2>Opinião gerada:</h2>
    <pre>{resultado}</pre>
  </section>
</main>


); 
}
```
---

### 🐍 3. Uso com Python

#### 3.1. Carregar o prompt `.json` e montar a mensagem


```bash
pip install openai python-dotenv
```



```python

>> scripts/usar_prompt.py

import json 
import os from pathlib 
import Path from dotenv 
import load_dotenv from openai 
import OpenAI

load_dotenv() client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))

BASE_DIR = Path(file).resolve().parent.parent prompt_path = BASE_DIR / "mercado-livre" / "opiniao-mercado-livre.json"

with open(prompt_path, "r", encoding="utf-8") as f: prompt_data = json.load(f)

system_prompt = prompt_data["prompt"]

grau_satisfacao = 3 titulo_produto = "Teclado mecânico ABC" observacao = "Barulho um pouco alto, mas confortável."

entrada_usuario = f""" Grau de satisfação: {grau_satisfacao} Produto: {titulo_produto} Observação: {observacao} """

response = client.chat.completions.create( model="gpt-4o-mini", messages=[ {"role": "system", "content": system_prompt}, {"role": "user", "content": entrada_usuario}, ], )

opiniao = response.choices[0].message.content print("Opinião gerada:\n") print(opiniao)
```

---

## 🤝 Contribuições

Se o repositório estiver aberto a contribuições, fluxo sugerido:

1. Faça um **fork** deste repositório.
2. Crie uma nova *branch*:


```bash
git checkout -b feature/novo-modelo-prompt
```
3. Adicione seus arquivos de prompt seguindo os padrões:
   - Estrutura de pastas coerente.
   - Nomes em kebab-case.
   - Formatos `.md` e, opcionalmente, `.json`.
4. Faça *commit* das alterações:


```bash
git commit -m "feat: adiciona modelo de prompt para XYZ"
```
5. Envie a *branch*:


```bash 
git push origin feature/novo-modelo-prompt
``` 
6. Abra um **Pull Request** descrevendo:
   - O objetivo do novo modelo.
   - Exemplos de uso (se relevante).

---

## 📜 Licença

Este projeto está licenciado sob a **Licença MIT**.  
Consulte o arquivo [`LICENSE`](./LICENSE) para mais detalhes.