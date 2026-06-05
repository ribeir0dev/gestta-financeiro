# Gestta

Gestta e um aplicativo desktop para Windows focado em controle financeiro pessoal e de pequenos negocios.

Slogan: "Seu dinheiro sob controle."

## Recursos do MVP

- Login local com senha protegida por hash PBKDF2.
- Cadastro de usuario local.
- Banco SQLite persistente criado automaticamente como `gestta.db`.
- Dashboard com saldo atual, receitas, despesas, resultado mensal e grafico simples.
- CRUD de transacoes financeiras.
- CRUD de categorias.
- Filtros por tipo, categoria, mes e descricao.
- Relatorios mensais por categoria e transacoes recentes.
- Exportacao CSV e backup do banco SQLite.
- Interface PySide6 moderna com sidebar.

## Instalar

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

## Executar

```powershell
python main.py
```

Na primeira execucao, crie uma conta local. As categorias iniciais sao criadas automaticamente para esse usuario.

## Gerar .exe

Build simples:

```powershell
pyinstaller --noconfirm --onefile --windowed main.py
```

Build incluindo assets visuais:

```powershell
pyinstaller --noconfirm --onefile --windowed `
  --name Gestta `
  --icon app\assets\icons\icone-aplicativo.ico `
  --add-data "app\assets;app\assets" `
  main.py
```

Quando executado por Python, o banco `gestta.db` e criado na raiz do projeto. Quando executado pelo `.exe`, o banco local e criado em:

```text
%LOCALAPPDATA%\Gestta\gestta.db
```

Isso permite instalar o aplicativo em `C:\Program Files\Gestta` sem problemas de permissao de escrita.

## Gerar instalador Windows

O projeto inclui um script do Inno Setup em:

```text
installer\Gestta.iss
```

1. Gere o `.exe` com PyInstaller.
2. Instale o Inno Setup: https://jrsoftware.org/isinfo.php
3. Compile o instalador:

```powershell
ISCC installer\Gestta.iss
```

O instalador sera gerado em:

```text
installer\output\Gestta-Setup-1.0.0.exe
```

Por padrao, o instalador instala o Gestta em:

```text
C:\Program Files\Gestta
```

## Atualizacoes

O Gestta verifica atualizacoes consultando um manifesto JSON remoto.

URL padrao:

```text
https://ribeirodev.com/gestta/latest.json
```

Tambem e possivel sobrescrever a URL por variavel de ambiente:

```powershell
$env:GESTTA_UPDATE_MANIFEST_URL="https://seu-dominio.com/gestta/latest.json"
```

Formato esperado:

```json
{
  "version": "1.0.1",
  "installer_url": "https://seu-dominio.com/downloads/Gestta-Setup-1.0.1.exe",
  "download_url": "https://seu-dominio.com/downloads/Gestta-Setup-1.0.1.exe",
  "sha256": "hash opcional do instalador",
  "notes": "Correcoes e melhorias da nova versao."
}
```

Quando a versao remota for maior que a versao local, o sino de notificacoes do Dashboard sera destacado e a aba `Configuracoes > Sobre` permitira baixar e iniciar o instalador da nova versao.

O manifesto JSON precisa estar acessivel publicamente. Se ele estiver em um repositorio privado do GitHub, usuarios externos nao conseguirao verificar atualizacoes sem autenticacao. Para GitHub, use um arquivo publico via GitHub Pages, um repositorio publico apenas para releases/manifesto, ou hospede o JSON em seu dominio.

Exemplo usando GitHub Releases:

```json
{
  "version": "1.0.1",
  "installer_url": "https://github.com/ribeir0dev/gestta-financeiro/releases/download/v1.0.1/Gestta-Setup-1.0.1.exe",
  "download_url": "https://github.com/ribeir0dev/gestta-financeiro/releases/download/v1.0.1/Gestta-Setup-1.0.1.exe",
  "sha256": "",
  "notes": "Melhorias visuais e correcoes gerais."
}
```

## Estrutura

```text
app/
  database/   conexao SQLite e migracoes
  models/     dataclasses base
  services/   regras de negocio
  ui/         telas e componentes PySide6
  utils/      formatacao, validacao e seguranca
```

## Futuro

A estrutura ja deixa espaco para metas financeiras, contas a pagar/receber, recorrencia, exportacao PDF/Excel, multiplas carteiras, modo escuro e graficos avancados.
