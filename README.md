# SAP Activate Effort Estimator (POC)

Este repositório é um *starter kit* para:
1) gerar um `estimate.json` no Claude a partir de exports do SAP Roadmap Viewer; e
2) gerar um Excel final (layout consistente) a partir do JSON.

## Como usar (fluxo POC)
### A) Claude → estimate.json
- Anexe ao Claude: **WBS Tree**, **Accelerators**, e (se existir) **MS Project XML**.
- Cole o prompt em `prompts/claude_master_prompt.md`.
- Salve o JSON final em `estimate.json`.

### B) Gerar Excel
```bash
python generator/generate_excel.py --input estimate.json --output out.xlsx --start-date 2026-01-15
```

> Observação: o timeline gerado é **sequencial por fase** e serve como POC (não representa dependências reais).

## Estrutura
- `prompts/` prompt único (parametrizado para 3 roadmaps)
- `schema/` JSON schema do estimate
- `generator/` script Python (openpyxl) para gerar .xlsx
- `web/` HTML opcional para validar/preview do JSON
- `samples/` exemplo de JSON (sem dados sensíveis)

## O que NÃO commitar
- Arquivos exportados do SAP for Me com dados de cliente / IDs internos
- Qualquer credencial (usuário/senha)
