# 🚀 INSTRUÇÕES DE DEPLOY - PASSO A PASSO

## Commit Atual
```
636935c (HEAD -> main) fix: corrigir seripm.gs completo - sintaxe, login, portal empresa
```

**Status**: ✅ Pronto para deploy

---

## ⚠️ IMPORTANTE: Você PRECISA fazer estas 3 coisas:

### 1️⃣ COPIAR seripm.gs PARA GOOGLE APPS SCRIPT

**Passo 1**: Abra o arquivo `seripm.gs` local
- Caminho: `d:\Users\rafael\Documents\Github e Google Sheets\seripm.gs`
- Ou abra direto em seu editor favorito

**Passo 2**: Selecione TODO o conteúdo
- `Ctrl + A` para selecionar tudo
- `Ctrl + C` para copiar

**Passo 3**: Acesse o Google Apps Script
- URL: https://script.google.com/macros/s/AKfycbwxfrN3AmulqSm2UvwZq45gbrFwTGM5qnufmov1OV_HtjBwg0UTjL_Xc3PwBA-u6-BZ/edit
- Clique no arquivo principal (provavelmente chamado "Code.gs" ou "seripm")

**Passo 4**: Substitua o código
- Selecione TODO o código (Ctrl + A)
- Delete (Delete ou Backspace)
- Cole o novo código (Ctrl + V)

**Passo 5**: Salve
- Clique em "Salvar" (ícone do disco) ou Ctrl + S
- Aguarde "Projeto salvo com sucesso" aparecer

---

### 2️⃣ FAZER DEPLOY DO GOOGLE APPS SCRIPT

**Passo 1**: No Apps Script, clique em "Deploy" (botão azul no topo)

**Passo 2**: Clique em "Novo Deploy"

**Passo 3**: Em "Selecionar tipo":
- Clique no dropdown
- Escolha **"API Executable"**

**Passo 4**: Clique em "Deploy"

**Passo 5**: Uma nova URL será gerada
- Exemplo: `https://script.google.com/macros/s/AKfycbw...NEW...xyz/exec`
- Copie esta URL

**Passo 6**: Verifique se a URL mudou
- Se a URL for igual à anterior: tudo OK
- Se for diferente: atualize em `index.html` (veja passo 3️⃣)

---

### 3️⃣ VERIFICAR URL NO index.html (SE MUDOU)

**Apenas se a URL do Apps Script mudou:**

**Passo 1**: Abra `index.html` em seu editor

**Passo 2**: Procure pela linha 346:
```javascript
const API_URL = "https://script.google.com/macros/s/AKfycbwxfrN3AmulqSm2UvwZq45gbrFwTGM5qnufmov1OV_HtjBwg0UTjL_Xc3PwBA-u6-BZ/exec";
```

**Passo 3**: Substitua pela nova URL do deploy

**Passo 4**: Salve (`Ctrl + S`)

**Passo 5**: Faça commit e push
```bash
git add index.html
git commit -m "chore: atualizar URL do Apps Script"
git push origin main
```

---

## 🧪 TESTAR TUDO

### Teste 1: Limpar Cache
1. Abra a página: https://rafaelvis89.github.io/seripm-painel/
2. Pressione `Ctrl + Shift + Del` (limpar cache)
3. Selecione "Imagens e arquivos armazenados em cache"
4. Clique em "Limpar dados"

### Teste 2: Login Admin
1. Recarregue a página (`Ctrl + R`)
2. Faça login com:
   - **Username**: `admin`
   - **Password**: `admin123`
3. Se entrar no dashboard ✅ **LOGIN FUNCIONANDO**

### Teste 3: Verificar Console
1. Pressione `F12` para abrir ferramentas do desenvolvedor
2. Clique na aba "Console"
3. Verifique se há erros em vermelho:
   - ✅ Se não há erros: OK!
   - ❌ Se há erros: anote e veja "Debug" abaixo

### Teste 4: Portal Empresa
1. Faça logout
2. Crie um usuário EMPRESA se não tiver:
   - Aba USUARIOS no Google Sheets
   - New row: `empresa | senha123 | EMPRESA | ATIVO | COM FUNCIONÁRIO`
3. Faça login como empresa
4. Vá para aba "COM FUNCIONÁRIO"
5. Verifique se aparecem 159 protocolos ✅

---

## 🐛 SE ALGO NÃO FUNCIONAR

### Erro 1: "Credenciais inválidas"
**Solução**: 
- Verifique Username/Password na aba USUARIOS do Google Sheets
- Confirme que STATUS é "ATIVO"
- Confirme que a senha está EXATAMENTE como digitou

### Erro 2: "Aba USUARIOS não encontrada"
**Solução**:
- Verifique o nome exato da aba: é "USUARIOS" ou "USUÁRIOS"?
- Se for "USUÁRIOS" (com acento), execute no Apps Script:
  ```javascript
  inicializarSistemaSERIPM()
  ```
  Isso criará a aba correta

### Erro 3: CORS Error no Console
**Solução**:
- Verifique a URL do Apps Script em `index.html` linha 346
- Confirme que o deploy foi feito (veja passo 2️⃣)
- Limpe cache (`Ctrl + Shift + Del`)

### Erro 4: Portal Empresa vazio (0 protocolos)
**Solução**:
- Verifique se há protocolos com STATUS "COM EMPRESA" em ENTRADA
- Execute em F12 → Console:
  ```javascript
  fetch(API_URL, {
    method: 'POST',
    body: JSON.stringify({
      action: "login",
      username: "empresa",
      password: "senha123"
    })
  }).then(r => r.json()).then(d => console.log(d));
  ```
- Se retornar `success: false`, há problema no backend
- Se retornar `success: true` mas role estiver errado, execute `inicializarSistemaSERIPM()`

---

## 📋 CHECKLIST PRÉ-DEPLOY

- [ ] Arquivo `seripm.gs` foi copiado para Google Apps Script?
- [ ] `seripm.gs` foi salvo no Apps Script (Ctrl + S)?
- [ ] Novo Deploy foi feito (tipo "API Executable")?
- [ ] URL foi verificada em `index.html` (se mudou)?
- [ ] Cache do navegador foi limpo?
- [ ] Login com admin funciona?
- [ ] Portal empresa mostra 159 protocolos?
- [ ] Sem erros no console (F12)?

---

## 🎯 RESULTADOS ESPERADOS APÓS DEPLOY

### Login Screen
- ✅ Campo de Username acessível
- ✅ Campo de Password acessível  
- ✅ Botão "Entrar" funciona
- ✅ Sem erros ao clicar

### Dashboard (Admin)
- ✅ 8 cards de status com números corretos
- ✅ Abas: ENTRADA, ADMINISTRATIVO, OBRA, TELECOM, GARANTIA, COM FUNCIONÁRIO, IMPLANTAÇÃO
- ✅ Tabelas carregam corretamente
- ✅ Sem lag ou delay

### Portal Empresa (Empresa)
- ✅ 159 protocolos aparecem
- ✅ Status "COM EMPRESA" visível
- ✅ Colunas: DATA, PROTOCOLO, ATENDENTE, ENDERECO, etc.
- ✅ Botões Print/PDF/Excel funcionam

### Mudança de Status
- ✅ Ao mudar status de um protocolo
- ✅ Cards atualizam automaticamente
- ✅ Protocolo desaparece da aba anterior

---

## 📞 PRÓXIMAS ETAPAS (APÓS DEPLOY)

1. **Implementar Portal Empresa responsive**
   - Arquivo: `empresa.html` (já existe)
   - Adicionar: Responsive design, PDF/Excel export
   - Timeline: 1-2 dias

2. **Otimizar layout para mobile**
   - Cards mais compactos
   - Botões maiores
   - Scroll horizontal em tabelas
   - Timeline: 1 dia

3. **Adicionar funcionalidades**
   - Busca/filtro de protocolos
   - Relatórios personalizados
   - Notificações em tempo real
   - Timeline: 3-5 dias

4. **Migração para PHP+MariaDB** (opcional)
   - Quando: Após validar o sistema atual
   - Documentação: `PROJETO_SERIPM_2.0.md`
   - Timeline: 2-3 semanas

---

## 🎉 SUCESSO!

Se você chegou aqui e tudo funcionou:

1. Faça um commit final:
```bash
git add -A
git commit -m "chore: deploy v2.1 completo e testado"
git push origin main
```

2. Comemore! 🎊 O sistema está funcionando!

3. Próximo passo: Otimizar Portal Empresa

---

**Versão**: 2.1  
**Data Deploy**: 1 de julho de 2026  
**Status**: ✅ PRONTO  
**Suporte**: Consulte `GUIA_TESTES.md` ou `CORRECOES_APLICADAS.md`
