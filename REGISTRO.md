# Registro do Projeto — Gerador de FCIV

**Data:** 16/11/2026  
**Pasta do projeto:** `c:\Users\Administrator\Documents\GERADOR DE FCIV`

---

## 1. Arquivos no Workspace
- [index.html](file:///c:/Users/Administrator/Documents/GERADOR%20DE%20FCIV/index.html) — Aplicação web 100% offline/autocontida (CSS + JS + modelo `.ods` embutido em Base64, sem CDNs externos, compatível com Firefox ESR e abertura direta via `file://`).
- [NIP_Esqd_ANO.ods](file:///c:/Users/Administrator/Documents/GERADOR%20DE%20FCIV/NIP_Esqd_ANO.ods) — Planilha modelo oficial utilizada como base.

---

## 2. O que já foi implementado hoje

### Arquitetura Offline
- Todo o estilo (CSS) e lógica (JS) embutidos em um único arquivo `index.html` (`139.225` bytes), sem `cdn.tailwindcss.com` ou qualquer requisição externa.
- Motor `OdsZip` em JavaScript puro (leitura ZIP com `inflateRaw` RFC 1951 próprio e escrita ZIP com `CompressionStream` + fallback para `STORE`).
- Motor `FcivOdsEngine` para edição direta do `content.xml` do `.ods`, preservando bordas, cores, mesclagens, fórmulas e o carimbo `ASSINADO DIGITALMENTE`.
- Opção no cabeçalho para **Trocar Modelo (.ods)** caso seja necessário usar outro arquivo base.

### Configuração Geral do Lote
- **Seletor de Modo:** `XY` ou `Piloto`.
- **OM em que Voou (`AA7`):** preenche `AA7` e compõe o nome do arquivo (`<NIP>_<OM_VOOU>_<ANO>.ods`).
- **OM em que Serve (`AA8`):** campo global com opção *"Mesma OM em que voou"* e campo individual por militar caso sirva em outra OM.
- **Ano da FCIV (`N7` e `N8`):** preenche `01/01/<ANO>` em `N7` e `31/12/<ANO>` em `N8`.
- **Encarregado (`N41` e `N42`):** Nome do Encarregado (`N41`) e Patente do Encarregado (`N42`), deslocados automaticamente pelo número de aeronaves (`41 + delta` e `42 + delta`).

### Cadastro de Militares e Aeronaves (sem repetir militar)
- **Dados do Militar (1 cartão por militar):**
  - `B7`: Posto/Graduação
  - `B8`: NIP
  - `B9`: Nome Completo
  - `N9`: Data do Último Voo (calculada automaticamente pela maior data entre as aeronaves do militar, com edição manual opcional)
  - `AA8`: OM em que Serve (herda o padrão ou pode ser editada)
  - `AA9`: Posto/Graduação do Último Voo
- **Blocos Dinâmicos de Aeronaves (`N` modelos por militar):**
  - **1 modelo:** exclui automaticamente as linhas `18` a `21` (`delta = -4`).
  - **2 modelos:** preenche os blocos `14–17` e `18–21` (`delta = 0`).
  - **3+ modelos:** clona o bloco de 4 linhas a partir de `A22` (`delta = +4 * (N - 2)`).
  - Preenche **Tipo de Motor** (`A14`, `A18`, `A22`...) e **Modelo ANV 2x** (`A16`/`A17`, `A20`/`A21`, `A24`/`A25`...).
  - **Modo XY:** `K` (Horas XY) e `L` (Data do último voo no modelo).
  - **Modo Piloto:** `B` (1P), `C` (2P), `D` (AL), `E` (INST IN), `F` (INST IV), `K` (XY), `L` (Últ. Voo no Modelo), `M` (Simulador), `O..S` (Tipos 1, 2, 3, 4D, 4N), `U..X` (Pousos Navio D/N e Terra D/N), `Z/AA/AC/AD` (CMD ON, COP ON, CMD OFF, COP OFF) e campos expansíveis opcionais `G..J` (PTM, PEE, MEC VOO, FIEL ANV).
- **Importador por Colagem (`Importar / Colar da Planilha`):** aceita linhas copiadas do Excel/Calc/Sheets e **agrupa automaticamente pelo NIP** (reunindo múltiplos modelos no mesmo militar).
- **Downloads:** individual por militar (`.ods`), todos individuais em sequência, ou pacote único `.zip` (`FCIVs_<OM>_<ANO>.zip`).

---

## 3. Verificações já realizadas
- Conferência de `0` URLs externas no `index.html` e validação de sintaxe JS (`node --check`).
- Teste byte a byte do `OdsZip.readZip` / `inflateRaw` contra todas as `9` entradas de `NIP_Esqd_ANO.ods`.
- Testes end-to-end no Chrome headless + abertura e conversão para PDF no LibreOffice Calc para:
  - XY com 1 modelo (linhas 18–21 excluídas)
  - XY com 2 modelos (agrupados via importador estilo Print 4)
  - XY com 3 modelos (bloco clonado em `A22..L25`)
  - Piloto completo com 2 modelos e pacote `.zip`

---

## 4. Próximos Passos (para continuar amanhã)
- Revisar e testar o [index.html](file:///c:/Users/Administrator/Documents/GERADOR%20DE%20FCIV/index.html) diretamente no **Firefox ESR** do ambiente do usuário.
- Ajustar detalhes pendentes de layout, campos ou regras que o usuário identificar na revisão.

