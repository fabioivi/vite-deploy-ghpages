# 🚀 Guia Completo: Deploy de Aplicação Vite no GitHub Pages

Este guia fornece instruções passo a passo para configurar e fazer deploy automático de uma aplicação Vite no GitHub Pages usando GitHub Actions.

## 📋 Pré-requisitos

- Repositório no GitHub
- Aplicação Vite criada e funcionando localmente
- Node.js e npm/yarn instalados

## ⚙️ Configuração do Vite

### 1. Configurar o `vite.config.js`

**IMPORTANTE:** Configure o `base` no arquivo `vite.config.js` com o nome do seu repositório:

```javascript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react' // ou o plugin que você estiver usando

export default defineConfig({
  plugins: [react()],
  base: "/nome-do-seu-repositorio/" // 🔥 ESSENCIAL para GitHub Pages
})
```

**Exemplo deste projeto:**
```javascript
export default defineConfig({
  plugins: [react()],
  base: "/vite-deploy-ghpages/"
})
```

> ⚠️ **Atenção:** O `base` deve corresponder exatamente ao nome do seu repositório GitHub, incluindo as barras `/` no início e no final.

## 🔧 Configuração do GitHub Actions

### 2. Criar o arquivo de workflow

Crie o arquivo `.github/workflows/deploy.yml` na raiz do seu projeto:

```yaml
# Simple workflow for deploying static content to GitHub Pages
name: Deploy static content to Pages

on:
  # Executa em pushes para a branch principal
  push:
    branches: ['main']

  # Permite executar o workflow manualmente
  workflow_dispatch:

# Define permissões necessárias para deploy no GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Permite apenas um deploy por vez
concurrency:
  group: 'pages'
  cancel-in-progress: true

jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v5
        
      - name: Set up Node
        uses: actions/setup-node@v5
        with:
          node-version: lts/*
          cache: 'npm'
          
      - name: Install dependencies
        run: npm ci
        
      - name: Build
        run: npm run build
        
      - name: Setup Pages
        uses: actions/configure-pages@v5
        with:
          enablement: true
          
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v4
        with:
          path: './dist'
          
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

## 🔐 Configuração do GitHub Pages

### 3. Habilitar GitHub Pages no repositório

1. Vá para o seu repositório no GitHub
2. Clique na aba **"Settings"**
3. No menu lateral, clique em **"Pages"**
4. Em **"Source"**, selecione **"GitHub Actions"** (não "Deploy from a branch")
5. Clique em **"Save"**

![GitHub Pages Settings](https://docs.github.com/assets/cb-20701/images/help/pages/publishing-source-github-actions.png)

## 🚀 Processo de Deploy

### 4. Como fazer deploy

1. **Commit e push das alterações:**
   ```bash
   git add .
   git commit -m "feat: add GitHub Pages deployment"
   git push origin main
   ```

2. **Acompanhar o deploy:**
   - Vá para a aba **"Actions"** do seu repositório
   - Observe o workflow **"Deploy static content to Pages"** em execução
   - Quando concluído, sua aplicação estará disponível em: `https://seu-usuario.github.io/nome-do-repositorio/`

### 5. Deploy manual (opcional)

Você também pode executar o deploy manualmente:
- Vá para a aba **"Actions"**
- Selecione o workflow **"Deploy static content to Pages"**
- Clique em **"Run workflow"**

## 📁 Estrutura de Arquivos Necessária

```
seu-projeto/
├── .github/
│   └── workflows/
│       └── deploy.yml          # Workflow do GitHub Actions
├── src/                        # Código fonte da aplicação
├── dist/                       # Pasta de build (gerada automaticamente)
├── vite.config.js             # Configuração do Vite (com base definida)
├── package.json
└── index.html
```

## 🔍 Verificação e Troubleshooting

### URLs esperadas:
- **Repositório:** `https://github.com/seu-usuario/nome-do-repositorio`
- **Site publicado:** `https://seu-usuario.github.io/nome-do-repositorio/`

### Problemas comuns:

1. **Página em branco:**
   - ✅ Verifique se o `base` no `vite.config.js` está correto
   - ✅ Confirme que o GitHub Pages está configurado para usar "GitHub Actions"

2. **Erro 404 para recursos:**
   - ✅ O `base` deve incluir as barras: `/nome-do-repositorio/`

3. **Deploy falha:**
   - ✅ Verifique se as permissões estão corretas no workflow
   - ✅ Confirme que o GitHub Pages está habilitado

4. **Workflow não executa:**
   - ✅ Verifique se o arquivo está em `.github/workflows/deploy.yml`
   - ✅ Confirme que o push foi feito para a branch `main`

## 🎯 Exemplo Prático

Para este projeto (`vite-deploy-ghpages`):

- **Repositório:** `https://github.com/fabioivi/vite-deploy-ghpages`
- **Site:** `https://fabioivi.github.io/vite-deploy-ghpages/`
- **Base configurada:** `/vite-deploy-ghpages/`

## 📚 Recursos Adicionais

- [Documentação oficial do Vite sobre deploy](https://vitejs.dev/guide/static-deploy.html#github-pages)
- [GitHub Pages com GitHub Actions](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site#publishing-with-a-custom-github-actions-workflow)
- [Ações disponíveis para GitHub Pages](https://github.com/marketplace/actions/deploy-github-pages-site)

---

## ⚛️ Sobre React + Vite

Este template fornece uma configuração mínima para fazer React funcionar no Vite com HMR e algumas regras ESLint.

Atualmente, dois plugins oficiais estão disponíveis:

- [@vitejs/plugin-react](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react) usa [Babel](https://babeljs.io/) para Fast Refresh
- [@vitejs/plugin-react-swc](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react-swc) usa [SWC](https://swc.rs/) para Fast Refresh

### React Compiler

O React Compiler não está habilitado neste template. Para adicioná-lo, veja [esta documentação](https://react.dev/learn/react-compiler/installation).

### Expandindo a configuração ESLint

Se você está desenvolvendo uma aplicação de produção, recomendamos usar TypeScript com regras de lint type-aware habilitadas. Confira o [template TS](https://github.com/vitejs/vite/tree/main/packages/create-vite/template-react-ts) para informações sobre como integrar TypeScript e [`typescript-eslint`](https://typescript-eslint.io) no seu projeto.

---

🎉 **Parabéns!** Sua aplicação Vite agora será automaticamente deployada no GitHub Pages a cada push na branch principal!
