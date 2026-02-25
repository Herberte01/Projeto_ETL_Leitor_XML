# Projeto_ETL_Leitor_XML
ETL em Python para ler XMLs de NF-e e gerar tabelas analíticas (Fiscal_docs, Fiscal_items e doc_referencias) para Power BI. Base para dashboards fiscais: controle de remessa p/ depósito e radar estimado de ressarcimento ICMS-ST em devoluções.

# O que este notebook faz
- Varre pastas com arquivos .xml de NF-e e eventos.
- Detecta o tipo do XML (NF-e x evento) e faz o parsing.
- Gera 3 saídas principais: documentos (header), itens e validações.
- Complementa com referências (NFref) e marca cancelamentos (quando disponível).
- Aplica tipagem “Power BI safe” e salva em arquivos na pasta de saída.

<img width="1482" height="597" alt="image" src="https://github.com/user-attachments/assets/a509e8cd-26d9-454c-b953-40f51074bd46" />

# Imports
- xml.etree.ElementTree (ET): parsing de XML.
- pandas: construir DataFrames e tipagem para export.
- hashlib: SHA-256 para ContentHash e fallback de DocKey.
- re: regex para localizar chaves de 44 dígitos (DocKey).
- pathlib/os: varrer diretórios e normalizar paths.
- pyodbc: conexão SQL Server (importado — uso pode estar em bloco não presente/omisso).
- 
# Parâmetros principais
- XML_BASE_FOLDER + XML_FOLDERS: diretórios (Windows) onde os XMLs são coletados.
- COMPANY_CNPJ: CNPJ “âncora” para inferir perspectiva (entrada/saída/devolução).
- OUT_DIR: pasta de saída (arquivos_processados) consumida pelo Power BI.
- BATCH_CODE / TENANT_CNPJ / USER_OPENID: rastreio analítico do lote.
- SQL_CONN: string ODBC para SQL Server (se usado, geralmente para gravar staging/logs).
- 
# Funções utilitárias (cell 2)
- only_digits(s): remove tudo que não é dígito (CNPJ/CPF/chave).
- to_text(x, pad=?): converte para texto e opcionalmente “zera à esquerda”.
- normalize_windows_path(path): padroniza separador e tenta resolver caminhos longos.
- safe_text(elem): evita None e faz .strip() quando existe texto.
- safe_float(x): converte números (com vírgula/ponto) com tolerância a erros.
