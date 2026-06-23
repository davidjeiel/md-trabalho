# GIT com COBOL

COBOL funciona perfeitamente com Git — o Git não se importa com a linguagem, ele versiona qualquer arquivo de texto. Os desafios reais são alguns detalhes específicos do mundo COBOL/mainframe.

## O básico funciona igual a qualquer projeto

```bash
cd /caminho/do/seu/sistema
git init
git add .
git commit -m "Versão inicial do sistema COBOL"
```

E para conectar ao GitHub:
```bash
git remote add origin https://github.com/davidjeiel/seu-sistema-cobol.git
git push -u origin main
```

## Os pontos de atenção específicos do COBOL

**1. Codificação de caracteres (EBCDIC vs ASCII)**
Se o código vem direto de um mainframe (z/OS), os arquivos podem estar em **EBCDIC**, não em UTF-8/ASCII. O Git vai tratá-los como binário e você perde os benefícios do diff. Converta para ASCII antes de versionar, usando ferramentas como `iconv`:

```bash
iconv -f IBM037 -t UTF-8 programa.cbl > programa_utf8.cbl
```

**2. Final de linha (CRLF vs LF)**
Mainframe e Windows usam convenções diferentes de quebra de linha. Configure um `.gitattributes` para padronizar:
```
*.cbl text
*.cob text
*.cpy text
```

**3. COBOL de colunas fixas (formato fixed)**
COBOL tradicional usa colunas significativas (área A começa na coluna 8, área B na 12, sequência nas colunas 1-6). Cuidado com editores que reformatam ou removem espaços à direita — isso pode quebrar a compilação. Evite "trim" automático de whitespace.

## .gitignore para COBOL

Não versione os artefatos compilados, só o fonte:
```
# Compilados
*.o
*.so
*.dll
*.exe
*.gnt
*.int
*.lst

# Listagens e dumps
*.LISTING
*.dump
```

## Organização sugerida do repositório

```
seu-sistema-cobol/
├── src/          # programas .cbl / .cob
├── copybooks/    # copybooks .cpy
├── jcl/          # scripts JCL (se mainframe)
├── docs/
├── .gitattributes
└── .gitignore
```

## Dica para a matriz de rastreabilidade

Como você estava montando aquela matriz, vale saber: copybooks são ótimos pontos de rastreabilidade. Como vários programas compartilham o mesmo copybook, dá para mapear "qual funcionalidade depende de qual estrutura de dados" facilmente referenciando os `.cpy` na sua tabela.

Uma pergunta que ajuda a afinar a resposta: o código está num **mainframe IBM (z/OS)** ou é um COBOL mais moderno rodando em PC (GnuCOBOL, Micro Focus)? O fluxo de tirar o código de lá para o Git muda bastante entre os dois casos.
