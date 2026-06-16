# R3 Capital — Site Institucional (memória de projeto)

Este arquivo é a **memória completa** do projeto. Ao retomar trabalho em outra sessão (Claude Code no terminal, outro chat, etc.), leia este arquivo primeiro.

Última atualização: 2026-06-16.

---

## 1. Visão geral

**Empresa**: R3 Capital Wealth Planning Ltda
**CNPJ**: 65.971.768/0001-58
**Regulação**: CVM — Resolução CVM nº 19/2021 (Consultor de Valores Mobiliários)
**Sócio responsável / DPO**: Eder Fabrilo
**Sede**: Maringá/PR

**Objetivo do site**: site institucional estático, hospedado no GitHub Pages, com:
- Páginas institucionais (Home, Sobre, Equipe, Contato)
- Políticas obrigatórias (Privacidade/LGPD, Compliance, Suitability, Conflito de Interesses)
- Formulário de contato integrado ao Formspree
- Botão "Agendar conversa" que abre WhatsApp

O motivador principal do site foi atender a exigência da **Veritas** (escritório de compliance) que precisa dos links públicos das 4 políticas para finalizar os contratos da CVM.

---

## 2. Stack técnico

- **HTML/CSS puro** — sem frameworks, sem build step.
- **CSS único** em `css/style.css` (compartilhado entre todas as páginas).
- **Assets** em `assets/` (logos PNG e og-image).
- **Deploy**: GitHub Pages via GitHub Actions (`.github/workflows/pages.yml`) — rodando em push para a branch `main`.
- **Formulário de contato**: Formspree (endpoint `https://formspree.io/f/mzdwwpgw`).
- **WhatsApp**: link direto `https://wa.me/5544997051998`.

---

## 3. Estrutura do repositório

```
r3-capital-site/
├── CNAME                          # Conteúdo: r3cap.com.br (custom domain do GitHub Pages)
├── CLAUDE.md                      # Este arquivo
├── LEIA-ME.txt                    # Documentação original da entrega
├── .github/workflows/pages.yml    # Workflow de deploy
├── index.html                     # Home
├── sobre.html
├── equipe.html
├── contato.html                   # Inclui formulário Formspree
├── politica-de-privacidade.html   # LGPD
├── politica-de-compliance.html    # PLD/FT
├── politica-de-suitability.html
├── conflito-de-interesses.html
├── robots.txt
├── sitemap.xml
├── assets/
│   ├── logo-icon.png
│   ├── logo-icon-branco.png
│   └── og-image.png
├── css/style.css
└── r3-wordpress-import.xml        # Backup p/ futura migração WP (não usado)
```

---

## 4. Repositório GitHub

- **URL**: https://github.com/ederfabrilo-byte/r3-capital-site
- **Branch de produção**: `main` (qualquer push aqui dispara redeploy do Pages)
- **Branch de trabalho do Claude Code Web**: `claude/zen-carson-lgOcc`
- **Workflow de deploy**: `.github/workflows/pages.yml` — usa `actions/deploy-pages@v4`, faz upload do repo inteiro como artifact estático

### Como disparar redeploy manual
```bash
git checkout main
git pull origin main
git commit --allow-empty -m "Trigger Pages redeploy"
git push origin main
```

---

## 5. Domínio e DNS

### Domínio
- **Domínio**: `r3cap.com.br`
- **Registrador**: Registro.br
- **Servidores DNS atuais**: Registro.br (`a.dns.br` / `b.dns.br`) — migrado de `dns-parking.com` (Hostinger) em 2026-06-15
- **Painel de DNS**: https://registro.br → login → painel do domínio → **DNS**

### Modos do Registro.br

O Registro.br tem **2 modos mutuamente exclusivos**:

1. **Modo básico** ("Configurar endereçamento") — interface simplificada:
   - 1 campo "Endereço do site" (IP ou hostname)
   - 1 campo "Servidor de e-mail" (1 MX só)
   - **Não suporta** TXT (SPF/DKIM/DMARC), múltiplos MX, subdomínios

2. **Modo avançado** ("Configurar zona DNS") — controle total:
   - Adicione manualmente A, CNAME, MX, TXT, etc.
   - **Trocar entre modos trava por 4h** (anti-loop)

### Estado atual da configuração DNS

Confirmado em **2026-06-16** — modo avançado, zona completa:

| Tipo | Nome | Dados |
|------|------|-------|
| A | r3cap.com.br | 185.199.108.153 |
| A | r3cap.com.br | 185.199.109.153 |
| A | r3cap.com.br | 185.199.110.153 |
| A | r3cap.com.br | 185.199.111.153 |
| CNAME | www.r3cap.com.br | r3cap.com.br |
| MX 5 | r3cap.com.br | mx1.hostinger.com |
| MX 10 | r3cap.com.br | mx2.hostinger.com |
| TXT | r3cap.com.br | v=spf1 include:_spf.mail.hostinger.com ~all |
| CNAME | hostingermail-a._domainkey.r3cap.com.br | hostingermail-a.dkim.mail.hostinger.com |
| CNAME | hostingermail-b._domainkey.r3cap.com.br | hostingermail-b.dkim.mail.hostinger.com |
| CNAME | hostingermail-c._domainkey.r3cap.com.br | hostingermail-c.dkim.mail.hostinger.com |
| TXT | _dmarc.r3cap.com.br | v=DMARC1; p=none |

### Verificação via terminal
```bash
# Sandbox do Claude Code Web NÃO tem acesso DNS externo. No terminal local, use:
dig +short r3cap.com.br @1.1.1.1
dig +short r3cap.com.br MX @1.1.1.1
dig +short r3cap.com.br TXT @1.1.1.1
```

---

## 6. Site (GitHub Pages)

### URLs
- **Produção**: https://r3cap.com.br ✅ funcionando com HTTPS (certificado Let's Encrypt automático)
- **www**: https://www.r3cap.com.br ✅ funcionando (CNAME configurado em 2026-06-16)
- **URL nativa GitHub**: https://ederfabrilo-byte.github.io/r3-capital-site/ (não usar publicamente)

### Configuração do custom domain
- Settings → Pages → Custom domain: `r3cap.com.br`
- Enforce HTTPS: ✅ ativado
- Arquivo `CNAME` na raiz: contém exatamente `r3cap.com.br` (sem `www.`)

### Recomendação correta de DNS para GitHub Pages
Para máxima resiliência (não usado atualmente — usamos só 1 IP), o ideal seria os **4 IPs** do GitHub Pages como A records:
```
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```
E um CNAME para `www` apontando para `ederfabrilo-byte.github.io.`.

Em 2026-06-16 migramos para modo avançado — todos os 4 IPs e www configurados.

---

## 7. E-mail

### Provedor
- **Hostinger Email nativo** (não é Google Workspace, não é Titan).
- Webmail: https://mail.hostinger.com
- Painel admin: https://hpanel.hostinger.com → menu **Domains** ou **Emails**

### MX configurado (2026-06-16)
```
Priority 5  → mx1.hostinger.com  ✅
Priority 10 → mx2.hostinger.com  ✅
```

### Caixas existentes
| Endereço | Status | Obrigação |
|----------|--------|-----------|
| `contato@r3cap.com.br` | ✅ ativa | Comercial |
| `compliance@r3cap.com.br` | ✅ criada em 2026-06-16 | **CVM** — canal obrigatório |
| `privacidade@r3cap.com.br` | ✅ criada em 2026-06-16 | **LGPD** — canal do DPO (Eder Fabrilo) |

### Forwarding conhecido
- `contato@r3cap.com.br` → `rafael.r.ratto@gmail.com` (forwarder configurado no Hostinger)
- E-mail principal de operação: `rafa.ratto86@gmail.com`

### Plano
- Upgraded de Free Trial para plano pago em 2026-06-16
- Domínio verificado — painel Hostinger está verde ✅

---

## 8. Formulário de contato (Formspree)

- **Endpoint**: https://formspree.io/f/mzdwwpgw
- **Página**: `contato.html` (linha 76)
- **Painel**: https://formspree.io (login com a conta admin)
- **Campos enviados**: nome, email, telefone, mensagem, `_subject` = "Contato pelo site — R3 Capital"
- **Destino atual**: **não verificado** — em tese vai pro e-mail cadastrado na conta Formspree, que pode ou não ser `contato@r3cap.com.br`.

---

## 9. WhatsApp

- Número: **+55 44 99705-1998**
- Link usado em todos os botões "Agendar conversa":
  ```
  https://wa.me/5544997051998?text=Olá!%20Gostaria%20de%20agendar%20uma%20conversa%20com%20a%20R3%20Capital.
  ```

---

## 10. ✅ O que está funcionando

- Site no ar em `https://r3cap.com.br` com HTTPS
- `www.r3cap.com.br` funcionando ✅ (configurado em 2026-06-16)
- Todas as 9 páginas renderizando corretamente
- Formulário Formspree (envio OK; destino a verificar)
- Botões WhatsApp em todas as páginas
- MX 5 mx1 + MX 10 mx2 configurados ✅
- SPF + DKIM (3 CNAMEs) + DMARC configurados ✅
- `compliance@` e `privacidade@` criados no Hostinger ✅
- Plano Hostinger atualizado (não é mais Free Trial) ✅
- Deploy automático via GitHub Actions em push para `main`
- Sitemap e robots.txt configurados

---

## 11. ⚠️ Pendências (em ordem de risco)

| # | Item | Risco | Onde resolver |
|---|------|-------|---------------|
| 1 | Verificar/ajustar destino do Formspree | Médio (forms podem estar caindo no lugar errado) | Formspree dashboard |

---

## 12. Acessos / credenciais (NÃO armazenadas neste arquivo)

Lugares onde você precisa logar para mexer no projeto:

| Serviço | URL | Para que |
|---------|-----|---------|
| GitHub | https://github.com/ederfabrilo-byte/r3-capital-site | Código, deploy, Pages settings |
| Registro.br | https://registro.br | DNS do domínio |
| Hostinger | https://hpanel.hostinger.com | E-mails (caixas, DKIM, forwarders) |
| Hostinger Webmail | https://mail.hostinger.com | Acessar e-mails recebidos |
| Formspree | https://formspree.io | Configurar destino do formulário de contato |

---

## 13. Comandos úteis (terminal local)

```bash
# Servir o site localmente (Python)
cd r3-capital-site && python3 -m http.server 8000
# Abra http://localhost:8000

# Ver últimas mudanças
git log --oneline -10

# Disparar redeploy manual
git checkout main && git pull && git commit --allow-empty -m "Trigger redeploy" && git push

# Verificar DNS (precisa de dig instalado)
dig +short r3cap.com.br @1.1.1.1
dig +short r3cap.com.br MX @1.1.1.1
dig +short r3cap.com.br TXT @1.1.1.1
dig +short www.r3cap.com.br @1.1.1.1

# Verificar HTTPS / certificado
curl -I https://r3cap.com.br

# Testar acesso ao site
curl -sI https://r3cap.com.br | head -5
```

---

## 14. Convenções de edição

- **Sempre editar HTML manualmente** — sem template engine, sem build.
- **Mudanças de conteúdo** (textos, e-mails, telefone) ficam repetidas em todas as páginas HTML. Use Grep/Edit globais. Exemplos:
  ```bash
  grep -rn "contato@r3cap.com.br" *.html
  grep -rn "wa.me/5544997051998" *.html
  ```
- **CSS único** em `css/style.css` — qualquer mudança visual vai aqui.
- **Sem JavaScript framework** — só JS inline em `contato.html` para o status do formulário (se houver).
- **Commits**: padrão imperativo em português ("Adiciona...", "Corrige...", "Ajusta...").

---

## 15. Histórico de decisões importantes (jornada)

Cronologia resumida do que aconteceu:

1. **Maio/2026**: site entregue como pacote estático (LEIA-ME.txt original).
2. **Início Jun/2026**: site versionado no GitHub, workflow de deploy criado, Formspree integrado, WhatsApp configurado.
3. **15/06/2026**:
   - Domínio estava com DNS em `dns-parking.com` (Hostinger).
   - Migramos servidores DNS para Registro.br (`a.dns.br` / `b.dns.br`).
   - Configuramos endereçamento básico: site `185.199.108.153`, e-mail (errado inicialmente) `smtp.google.com`.
   - HTTPS emitido com sucesso no GitHub Pages.
   - **Bug identificado**: e-mail apontava para Google (assumi Google Workspace por engano), mas o provedor real é Hostinger nativo.
   - **Correção**: MX trocado para `mx1.hostinger.com`.
   - Site funcionando 100%, e-mail recebendo de novo.
4. **16/06/2026** (retomada): identificadas pendências de SPF/DKIM/DMARC/www/aliases. Em andamento.

---

## 16. Gotchas / coisas para lembrar

- **Cache DNS local**: ao testar mudanças de DNS, lembre que macOS/Chrome cacheiam por horas. Use:
  ```bash
  sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder   # macOS
  ```
  E no Chrome: `chrome://net-internals/#dns` → Clear host cache.

- **NXDOMAIN é cacheado por até 24h** em alguns ISPs. Se um domínio "não existia" antes e você acabou de criá-lo, pode demorar até 1 dia pra resolver na sua rede de casa. Teste pelo 4G ou DNS público (1.1.1.1).

- **Modo básico vs avançado do Registro.br**: ao mudar, perde tudo do modo anterior. Sempre faça inventário antes.

- **GitHub Pages + custom domain**: ao salvar o domínio em Settings → Pages, o GitHub commita automaticamente o arquivo `CNAME` no repo. Se você apaga ou edita esse arquivo errado, pode quebrar o custom domain.

- **Workflow de deploy só roda em `main`** — pushes em outras branches não disparam Pages.

- **O e-mail `r3capital.com.br` (com "ital") NÃO existe** — confusão recorrente. O domínio é `r3cap.com.br` (sem "ital"). Verifique sempre em qualquer referência externa.

---

## 17. Quando você (Claude Code) retomar o trabalho

1. Leia este arquivo inteiro.
2. Confira o estado real do DNS rodando `dig` (ver seção 13).
3. Confira o status do deploy: https://github.com/ederfabrilo-byte/r3-capital-site/actions
4. Confira se o site responde: `curl -I https://r3cap.com.br`
5. Pergunte ao usuário o que ele quer atacar primeiro entre as pendências da seção 11.
6. Para qualquer mudança de DNS ou e-mail, **o usuário precisa clicar no painel** — você não tem acesso direto a Registro.br/Hostinger/Formspree. Guie por screenshot.
